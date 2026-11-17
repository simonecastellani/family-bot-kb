---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
timestamp: 2026-11-17
title: "M2 — Deploy, Telegram e agente"
type: Pagina
---
# M2 — Deploy, Telegram e agente

Piano della milestone M2: portare il bot in cloud, collegare il webhook Telegram e mettere un agente con i tool dei task davanti ai servizi di [[progetto/m1-task-core]]. Stato **`open`**: approvato il 2026-11-16, non ancora iniziato. I criteri di accettazione restano quelli di [[progetto/roadmap]]; non si anticipa nulla di M3.

## Obiettivo

Dal telefono, in cloud: «Ricordami di chiamare l'idraulico giovedì» produce una **proposta** di task; la conferma con bottone inline crea il task e la sua occorrenza, con audit e `agent_runs` registrata.

## Dentro

- `members`: email, `telegram_user_id`, ruolo, timezone, preferenze; è la whitelist del webhook e il mapping futuro dell'email OIDC.
- `agent_runs`: input, tipo, modello, token, esito, step incorporati con cap, azioni proposte; idempotenza del webhook.
- Porta `AgentEngine` con adapter PydanticAI e fake; tool = wrapper sottili sui servizi.
- Webhook Telegram con secret token, whitelist, dedupe, conferme inline.
- Composition root, deploy Cloud Run del servizio `api`, CD con Workload Identity Federation.

## Fuori (e dove va)

| Tema | Milestone |
|---|---|
| Servizio `worker`, Cloud Tasks, Scheduler, promemoria | M3 |
| Contract test sul Firestore Enterprise reale | M3 |
| `/api/v1` e login Google | M5 |
| Vocali, link, immagini, menu | M6–M8 |
| Terraform | dopo M2, come da primer §3 |

Il `worker` non si crea vuoto: non ha nulla da invocare fino a M3, e il debito IAM su `run.invoker` a livello progetto resta aperto e registrato in [[infrastruttura/inventario]] fino a quel deploy.

## Decisioni

| Tema | Scelta |
|---|---|
| Libreria Telegram | `python-telegram-bot` ≥ 20 (v22.x), webhook. Si usa `telegram.Bot` come client async, **non** `Application`/`Dispatcher`: il route FastAPI verifica l'header e deserializza con `Update.de_json`, così una sola parte possiede il flusso e i servizi non conoscono il framework. Verificato sui documenti v22 il 2026-11-16: `Bot` è un async context manager con `initialize()`/`shutdown()`, `set_webhook(secret_token=…)` imposta `X-Telegram-Bot-Api-Secret-Token`; `BadRequest` deriva da `NetworkError`, quindi l'ordine degli `except` conta |
| Astrazione agente | Porta `AgentEngine` (`AgentRequest`/`AgentReply` con testo, proposte, step, usage) in `app/integrations/`; adapter PydanticAI, fake per i test. Il segnaposto `LLMClient` viene ritirato |
| Conferma | I tool di scrittura **non scrivono**: restituiscono una `ProposedAction` già validata. Le azioni vivono in `agent_runs.actions[]` con `status` e `expires_at` (24h); il bottone porta `cf:<run_id>:<action_id>` (≈35 byte, sotto il limite di 64 di `callback_data`). Doppio tap → `VersionConflict` → «già eseguita» |
| Actor delle scritture confermate | `source=telegram`, con `agent_run_id` nel diff di audit: la scrittura è del membro, non del modello |
| Idempotenza webhook | `agent_runs.dedupe_key = tg:<chat_id>:<update_id>` con indice unico: i retry di Telegram non duplicano run né task |
| Indici | Mai a runtime: li applica il job di deploy con il service account `family-bot-migrator` (`datastore.owner` condizionato al database). Se la migrazione fallisce il deploy non prosegue |
| LLM | Endpoint OpenAI-compatibile `https://opencode.ai/inference/go/openai/v1`; `LLM_MODEL` da env, default scelto con una mini-eval (20 frasi italiane con date relative) fra tre modelli flash |

## Ordine dei lavori

| # | Workstream | Esito verificabile |
|---|---|---|
| W0 | Verifiche: PTB installata, PydanticAI + OpenCode Go, OIDC runtime → Firestore, termini OpenCode Go | note e decisioni, nessun codice |
| W1 | `members` con seed idempotente e contract test | whitelist reale |
| W2 | `agent_runs` con dedupe e `actions[]`, contract test | idempotenza e traccia |
| W3 | Webhook PTB, porta `TelegramClient` e fake | messaggi end-to-end con agente finto |
| W4 | Composition root, deploy manuale Fase C2, `set_webhook` | primo messaggio reale dal telefono |
| W5 | `AgentEngine`, adapter PydanticAI, tool, conferme inline, eval modelli | criterio di accettazione |
| W6 | WIF, SA migrator, workflow di deploy | deploy da `main` senza chiavi |
| W7 | Documentazione (inventario, setup, AGENTS, primer) e chiusura milestone | milestone `done` |

L'ordine mette il percorso Telegram → servizi → database prima dell'LLM: i problemi di identità si isolano da quelli del modello.

## Rischi

- **Latenza**: cold start più chiamata al modello dentro il timeout di 60s del webhook. Ogni turno è breve, ma va misurato; contromisure: timeout a 90s oppure `min-instances=1`.
- **Function calling dei modelli open**: mitigato perché le scritture sono deterministiche e validate, non testo libero del modello.
- **OIDC dal runtime a Firestore**: unico ignoto tecnico vero, da chiudere in W0 prima di scrivere codice.
- **Prompt injection** da testi inoltrati: può al massimo produrre una proposta, mai una scrittura.

## Prerequisiti

`telegram_user_id` ed email dei membri con ruolo e timezone; bot creato e token in `telegram-bot-token`; webhook attuale rimosso prima del primo `set_webhook`.

## Esito delle verifiche W0 (2026-11-16)

Tre verifiche fatte prima di scrivere codice:

- **Firestore MongoDB compatibility raggiungibile**: con l'access token di `gcloud` e meccanismo `PLAIN` (come da guida, §A5), l'endpoint `63ea0fad-3b37-4915-9eff-b4b3fd292589.europe-west8.firestore.goog:443` risponde a `ping` su TLS con `loadBalanced=true&retryWrites=false`. Il database `family-bot` ha **0 collection**: M1 non ha mai scritto in cloud. Resta da provare il ramo `MONGODB-OIDC` (`ENVIRONMENT:gcp`), che si verifica al primo deploy.
- **PTB 22.8**: `Bot` è async context manager; `Update.de_json(data, bot)`; `set_webhook(secret_token=…)`; `BadRequest` deriva da `NetworkError` (ordine degli `except`).
- **PydanticAI 2.68 + OpenCode Go**: funziona con `OpenAICompatibleProvider(name=…, openai_client=AsyncOpenAI(...))` e `OpenAIChatModel`. L'endpoint **pretende** l'header `x-opencode-session` (senza: `400 MissingSessionID`) e un user agent proprio; con quelli, tool calling e output strutturato su `deepseek-v4.1-flash` producono la data relativa corretta («giovedì 19 novembre 2026») con 820+138 token.

### Provider LLM: decisione temporanea, con scadenza

Il piano OpenCode Go è documentato come destinato a «OpenCode and other coding agents», con traffico monitorato: un bot famigliare non rientra nell'intento. Decisione dell'operatore del 2026-11-16: **si sviluppa M2 con la chiave OpenCode già disponibile** e **il provider si sostituisce prima della messa in produzione**. La porta `AgentEngine` rende il cambio una modifica di configurazione; il gate è registrato come tale e non va dimenticato alla chiusura della milestone.

## Collegamenti

Le regole non negoziabili sono in [[architettura/regole]]; il comportamento dell'agente in [[architettura/agente]]; le porte e i fake in [[architettura/porte-e-fake]]; identità, secret e service account in [[infrastruttura/identita-e-runtime]]; CI/CD in [[infrastruttura/eventi-e-ci]]; la suite e i contract test in [[sviluppo/locale-e-test]]; le domande ancora aperte in [[progetto/questioni-aperte]].

Codice: {{repo:family-bot}}/app, documenti operativi {{repo:family-bot}}/docs/gcp-inventory.md e {{repo:family-bot}}/docs/gcp-setup.md.


## Avanzamento (2026-11-16)

- **W0 fatto**: endpoint Firestore raggiungibile, PTB 22.8 verificata, PydanticAI 2.68 + OpenCode Go funzionanti con l'header di sessione, termini Go chiusi come "solo sviluppo".
- **W1 fatto**: `members` con email normalizzata, ruoli, timezone, indici unici (parziale su `telegram_user_id`), seed idempotente `app.cli.seed_members`.
- **W2 fatto**: `agent_runs` con `dedupe_key` unico, step con cap, azioni con scadenza 24h e macchina a stati `pending → processing → confirmed/failed` (più `rejected`/`expired`), claim a vincitore unico.
- **W3 fatto**: porta `TelegramClient` (+ fake), porta `AgentEngine` (+ fake), adapter PTB solo trasporto, adapter PydanticAI con tool di lettura e di proposta, servizi di turno e di conferma, composition root unico, webhook con secret token, whitelist e dedupe. 130 test verdi, `ruff` e `pyright` puliti.
- **W4 parziale**: servizio `family-bot-api` deployato in `europe-west8` (revisione `00002-s8t`), `/livez` e `/readyz` rispondono, secret montati, **`MONGODB-OIDC` dal runtime verificato** con una richiesta reale al webhook. Restano il seed dei membri e il `set_webhook`, che richiedono `telegram_user_id` ed email dei membri.

### Scoperte da non dimenticare

- Cloud Run risponde **404 sul path esatto `/healthz`** prima del container: la liveness è `/livez`.
- Il database Enterprise `family-bot` è ancora vuoto: la prima scrittura sarà il seed.


## Prova end-to-end in cloud (2026-11-17)

Primo membro creato sul database Enterprise (`sfrulloo@gmail.com`, admin, Europe/Rome). Una richiesta reale al webhook con il suo `telegram_user_id` ha prodotto, dentro Firestore:

- la run `succeeded` con `deepseek-v4.1-flash` (2.499 token in ingresso, 240 in uscita, ~8,7 s);
- gli step `create_task` (call e result) e il messaggio finale;
- una proposta `task.create` **pending** con payload `due_date=2026-11-19`, cioè giovedì.

Manca solo il `setWebhook`: senza di esso Telegram non consegna nulla al servizio. Il test dal telefono è il criterio di accettazione.

### Regola scoperta

Il nome del database nella connection string MongoDB-compatible **deve coincidere con l'ID del database Firestore**: `family_bot` viene rifiutato con `Invalid database name`, `family-bot` funziona. Default, `.env.example`, compose e Cloud Run sono allineati a `family-bot`.


### Correzione dopo il primo test reale (2026-11-17)

Il primo messaggio dal telefono ha smascherato un difetto dell'agente, non dei bottoni: la run mostrava solo `list_tasks` e `list_occurrences`, il modello aveva **scritto** di aver preparato una proposta senza chiamare `create_task`, quindi `actions` era vuoto e nessuna tastiera inline compariva.

Due contromisure:

1. l'istruzione vieta di annunciare una proposta se lo strumento non è stato chiamato;
2. se la risposta parla di una proposta e `actions` è vuoto, l'adapter fa **un giro correttivo** con la cronologia del turno (una sola volta, poi si accontenta).

Verificato in cloud con lo stesso input: ora la run contiene `create_task` e una proposta `task.create` per il 2026-11-20. Coperto da test con `FunctionModel` (nessuna rete).
