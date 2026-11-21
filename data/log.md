# Log

## 2026-11-21T14:34:40Z

concept_batch (1 operation(s)):
- progetto/m4-6-eventi-esistenti

## 2026-11-21T12:54:27Z

[progetto] M4.6 — Eventi esistenti: piano scritto in `progetto/m4-6-eventi-esistenti` (status `open`) e aggiunto alla roadmap. Rilascio `v0.6.0`; M5 slitta a `v0.7.0`. Contenuto: `update_event` con If-Match e 412, `delete_event` idempotente, servizi con audit/cache, azioni agente con conferma, id evento in `list_calendar_events`, prompt per le correzioni, test e verifica reale sul «Cambio gomme». Handoff: M4 e M4.5a sono in produzione (`v0.5.2`), M4.6 non è iniziata; si riparte da W1 in build mode.

## 2026-11-21T12:53:17Z

concept_batch (2 operation(s)):
- progetto/m4-6-eventi-esistenti (+ index entry)
- progetto/roadmap

## 2026-11-21T09:54:10Z

[progetto] v0.5.2 — qualità delle risposte: `list_occurrences` restituisce il titolo del task, policy senza capacità di UI né Markdown, sanitizer sull'output e sull'outcome. Deploy revisione 00024-foc. Shadow verificato su tre messaggi reali: previsioni tutte sbagliate (chat/task_write/ingest) con `llm_used=true`, etichette corrette dai tool; il dataset cresce. M4.6 (modifica/cancellazione eventi) è la prossima, v0.6.0, M5 → v0.7.0.

## 2026-11-21T09:54:05Z

concept_batch (1 operation(s)):
- progetto/m4-5-routing-locale

## 2026-11-21T01:37:59Z

2026-11-21 — Importate undici skill di sviluppo (addyosmani/agent-skills, MIT) adattate alla KB: code-review-and-quality, code-simplification, context-engineering, documentation-and-adrs, doubt-driven-development, idea-refine, incremental-implementation, interview-me, source-driven-development, spec-driven-development, test-driven-development. Aggiunti il template `adr` e la pagina [[sviluppo/skill-di-sviluppo]]; ADR in `architettura/adr-NNNN-<slug>`, idee/intenti/spec in `progetto/`. Lint 0, gate verde, client sincronizzati.

## 2026-11-21T01:31:59Z

concept_patch: sviluppo/convenzioni-agenti

## 2026-11-21T01:31:46Z

concept_write: sviluppo/skill-di-sviluppo

## 2026-11-21T01:27:17Z

[progetto] M4.5a — shadow routing deployato (v0.5.0, poi v0.5.1): client MCP generico, LayaClassifier su `agent_runs` (route/confidence/modello/llm_used), export JSONL e set congelato con eval. Baseline zero-shot 68,3% su 60 frasi. Verificato da Cloud Run con un job one-off (308 ms, task_write per «che task ho oggi?»); il primo tentativo ha scoperto un newline finale nel secret `laya-mcp-token`, corretto e documentato. Prossimo: raccogliere ≥1.000 esempi dalle conversazioni reali, poi fine-tuning e canary delle sole query.

## 2026-11-21T01:27:11Z

concept_batch (1 operation(s)):
- progetto/m4-5-routing-locale

## 2026-11-21T01:16:51Z

concept_batch (2 operation(s)):
- progetto/m4-5-routing-locale (+ index entry)
- progetto/roadmap

## 2026-11-19T12:53:15Z

[progetto] M4 chiusa il 2026-11-19: prova dal telefono confermata (proposta `create_event` + bottone, evento su Google Calendar), sync idempotente, cancellati gestiti, creazione reale verificata. Rilascio `v0.4.1`, revisione `00017-zef`. Prossima milestone: M5 — dashboard in sola lettura.

## 2026-11-19T12:52:14Z

concept_batch (2 operation(s)):
- progetto/m4-google-calendar
- progetto/roadmap

## 2026-11-19T09:24:55Z

[progetto] M4 — dopo v0.4.1 verificati in cloud: sync full e incrementale dal job Scheduler (200), creazione reale di un evento via `CreateCalendarEventService`, sync (upsert 1), cancellazione su Google (204), sync incrementale (`deleted: 1`) e cache vuota. Resta solo la prova dal telefono (proposta `create_event` + bottone). Rilascio `v0.4.1`, revisione `00017-zef`.

## 2026-11-19T09:16:18Z

[progetto] M4 — deploy v0.4.0 riuscito (revisione 00015-nol, `/livez` 0.4.0, tre job Scheduler), ma il primo sync reale è fallito con 500: Google non restituisce `nextSyncToken` a una listing con `timeMin`/`timeMax`/`orderBy`. Fix in v0.4.1: la full sync legge l'intero calendario e applica la finestra [-7, +90] alla cache; verificato con due sync reali consecutivi (full + incrementale) contro l'API. Il token Service Account funziona su Cloud Run: la chiamata a `events.list` era 200.

## 2026-11-19T09:16:10Z

concept_patch: progetto/m4-google-calendar

## 2026-11-19T00:06:52Z

[progetto] M4 — Calendar: l'app OAuth non si poteva pubblicare (Google richiede un dominio autorizzato e verificato nella Branding page). Scelta del 2026-11-19: ADC del service account di runtime con scope `calendar.events`, agenda «Famiglia» condivisa con `family-bot-api` e `family-bot-worker`. W0 verificato con token impersonato: `events.list` 200 (`accessRole: writer`), `events.insert` 200, `DELETE` 204; nessun secret OAuth. Concesso `serviceAccountTokenCreator` all'operatore sui due SA per le verifiche. Resta il deploy v0.4.0.

## 2026-11-19T00:06:33Z

concept_batch (2 operation(s)):
- progetto/m4-google-calendar
- progetto/roadmap

## 2026-11-18T17:11:49Z

[progetto] M4 — Google Calendar: implementati W1–W5 (dominio, cache `calendar_events`, `calendar_sync_state`, adapter Google su httpx, sync con 410 e full resync, creazione con id deterministico e conferma, tool `create_event`/`list_calendar_events`/`get_week_overview`). Suite verde (lint, pyright, contract su MongoDB reale). Restano W0 (API Calendar, agenda «Famiglia», app OAuth pubblicata, refresh token, tre secret) e il deploy v0.4.0. Debito provider LLM rimandato a milestone dedicata con classificatore locale.

## 2026-11-18T17:11:32Z

concept_batch (2 operation(s)):
- progetto/m4-google-calendar (+ index entry)
- progetto/roadmap

## 2026-11-18T14:04:02Z

[progetto/m3-promemoria-event-driven] Contract sul Firestore reale eseguito (2026-11-18) col consenso dell'operatore: 41 test verdi, transazioni incluse. Guard ALLOW_REMOTE_CONTRACT per non svuotare per sbaglio un database remoto; TEST_MONGODB_DATABASE punta a family-bot. Gli indici sopravvivono allo svuotamento. Dati ricreati (membro + task nido) e promemoria ripianificato. Suite notturna rinviata alla pre-produzione.

## 2026-11-18T14:04:01Z

concept_write: progetto/m3-promemoria-event-driven

## 2026-11-18T14:03:45Z

concept_write: progetto/questioni-aperte

## 2026-11-18T12:57:10Z

[progetto/m3-promemoria-event-driven] M3 rilasciata come v0.3.0 (2026-11-18): coda Cloud Tasks reminders, worker privato con /internal/* verificato via OIDC, job Scheduler da deploy/scheduler.json, bottoni Fatto/Domani, run.invoker stretto al worker. Scoperti in cloud: limite 720h di Cloud Tasks, $elemMatch non supportato da Firestore, worker senza chiave LLM. Primo promemoria reale in coda per il 5 dicembre. Aperto: contract test sul Firestore reale.

## 2026-11-18T12:57:09Z

concept_write: progetto/roadmap

## 2026-11-18T12:57:09Z

concept_write: progetto/questioni-aperte

## 2026-11-18T12:56:53Z

concept_write: progetto/m3-promemoria-event-driven

## 2026-11-18T11:21:04Z

concept_write: progetto/m3-promemoria-event-driven

## 2026-11-17T14:43:22Z

[progetto/m2-deploy-telegram-agente] M2 chiusa (2026-11-17): release v0.2.0 da tag semver attraverso la pipeline (guardia, test, immagine, migrazioni, canary senza traffico, smoke, spostamento traffico). In produzione la revisione 00007-lin riporta 0.2.0. Aggiunti WIF con condizione su ref_type=tag, job di migrazione indici, /livez con versione e /readyz con ping al database. Scoperti e corretti: CI rossa dal 13 novembre (--replSet nei services), indici parziali non supportati da Firestore, createIndex sotto condizione IAM.

## 2026-11-17T14:43:17Z

concept_write: infrastruttura/eventi-e-ci

## 2026-11-17T14:43:17Z

concept_write: progetto/roadmap

## 2026-11-17T14:43:17Z

concept_write: progetto/questioni-aperte

## 2026-11-17T14:40:55Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-17T12:44:49Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-17T08:30:28Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-17T00:32:39Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-16T00:19:40Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-15T23:57:39Z

concept_write: infrastruttura/identita-e-runtime

## 2026-11-15T23:57:38Z

concept_write: progetto/stack

## 2026-11-15T23:57:38Z

concept_write: progetto/questioni-aperte

## 2026-11-15T23:57:38Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-15T23:29:37Z

[progetto/m2-deploy-telegram-agente] Pianificazione M2 (2026-11-16): approvato il piano di deploy Cloud Run + webhook Telegram + agente. Decisioni: python-telegram-bot >=20 con Bot e non Application/Dispatcher; porta AgentEngine davanti a PydanticAI; conferme deterministiche in agent_runs.actions[]; indici applicati dal job di deploy con family-bot-migrator. Spostati a M3 il servizio worker e i contract test sul Firestore Enterprise reale. Nessun codice scritto: la milestone resta open.

## 2026-11-15T23:29:25Z

concept_write: architettura/agente

## 2026-11-15T23:29:25Z

concept_write: progetto/stack

## 2026-11-15T23:29:11Z

concept_write: progetto/questioni-aperte

## 2026-11-15T23:29:02Z

concept_write: progetto/roadmap

## 2026-11-15T23:28:49Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-15T22:42:38Z

concept_patch: infrastruttura/inventario

## 2026-11-15T22:42:38Z

concept_patch: infrastruttura/identita-e-runtime

## 2026-11-15T22:42:28Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:12:52Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:10:35Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:10:22Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:09:45Z

concept_patch: infrastruttura/eventi-e-ci

## 2026-11-15T00:09:36Z

concept_patch: infrastruttura/identita-e-runtime

## 2026-11-14T17:24:59Z

[progetto] Chiusura M1: riverifica locale del 2026-11-14 (74 test verdi su MongoDB locale, ruff e pyright puliti). `local-admin` e il contract test sul database Enterprise reale restano debito di M2, allineati in `docs/gcp-inventory.md` e `docs/gcp-setup.md` del repo (commit `61939ac` e `d16b519`). Nessuna risorsa GCP modificata.

## 2026-11-14T17:24:52Z

concept_patch: progetto/m1-task-core

## 2026-11-14T17:24:40Z

concept_patch: infrastruttura/inventario

## 2026-11-13T18:28:22Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T18:26:59Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T18:22:26Z

[progetto] M1 — task core implementata in {{repo:family-bot}}: dominio e RRULE (dateutil, whitelist, DST), porte + adapter MongoDB (UUIDv7, indice unico task_id+occurrence_key, transazioni per mutazione+audit), servizi comando/query, creazione con materializzazione della prima finestra, 72 test (unit + contract + integration). MongoDB locale e CI ora usano un replica set a nodo singolo. Criteri 1–7 coperti.

## 2026-11-13T18:22:16Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T18:21:48Z

concept_patch: sviluppo/convenzioni-agenti

## 2026-11-13T18:21:42Z

concept_patch: progetto/roadmap

## 2026-11-13T18:21:37Z

concept_patch: progetto/m1-task-core

## 2026-11-13T18:21:23Z

concept_patch: architettura/task-e-occorrenze

## 2026-11-13T17:38:57Z

concept_batch (4 operation(s)):
- architettura/task-e-occorrenze (+ index entry)
- progetto/m1-task-core (+ index entry)
- progetto/roadmap
- progetto/questioni-aperte

## 2026-11-13T10:29:34Z

Contraddizioni risolte (2026-11-13): nome task Cloud Tasks versionato vs dedupe_key (idempotenza su reminders._id); soglie budget canoniche 50/80/100; limite documento 16 MiB con regola di progetto <4 MiB; Motor escluso (EOL 14/05/2026). Pagine: architettura/promemoria, architettura/modello-dati, infrastruttura/eventi-e-ci, infrastruttura/gcp-bootstrap, infrastruttura/inventario, progetto/stack, progetto/questioni-aperte (9→7 aperte), progetto/roadmap. Stesse correzioni applicate a primer.md e docs/gcp-setup.md nel repo family-bot (non committate).

## 2026-11-13T10:28:09Z

concept_batch (8 operation(s)):
- architettura/promemoria
- infrastruttura/eventi-e-ci
- architettura/modello-dati
- infrastruttura/gcp-bootstrap
- infrastruttura/inventario
- progetto/stack
- progetto/questioni-aperte
- progetto/roadmap

## 2026-11-13T09:39:11Z

Ingest family-bot completato: 5 fonti, 16 unità (10 any / 6 large / 0 human), 19 pagine attive. Revisione campione: U01, U04, U07, U09, U11, U13 — gate puliti, redazione di email/billing corretta, nessun contenuto inventato. Aperti due conflitti tra fonti lasciati come checkbox: nome task Cloud Tasks (dedupe) e soglie budget 50/90/100% vs 50/80/100%.

## 2026-11-13T09:39:11Z

concept_batch (6 operation(s)):
- sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot
- sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud
- sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp
- sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo
- sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci
- ingest/piano-family-bot-docs

## 2026-11-13T09:38:22Z

concept_patch: ingest/u16-codice-qualita

## 2026-11-13T09:38:14Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T09:38:14Z

concept_patch: sviluppo/qualita

## 2026-11-13T09:36:45Z

concept_patch: ingest/u13-inventario

## 2026-11-13T09:36:33Z

concept_patch: ingest/u12-gcp-osservabilita-integrazioni

## 2026-11-13T09:36:25Z

concept_batch (3 operation(s)):
- infrastruttura/osservabilita (+ index entry)
- architettura/integrazioni (+ index entry)
- architettura/sicurezza

## 2026-11-13T09:35:52Z

concept_batch (2 operation(s)):
- infrastruttura/inventario (+ index entry)
- infrastruttura/gcp-bootstrap

## 2026-11-13T09:35:49Z

concept_patch: ingest/u16-codice-qualita

## 2026-11-13T09:35:31Z

concept_patch: ingest/u14-agents-readme

## 2026-11-13T09:35:25Z

concept_patch: sviluppo/convenzioni-agenti

## 2026-11-13T09:35:25Z

concept_patch: sviluppo/qualita

## 2026-11-13T09:35:16Z

concept_patch: ingest/u11-gcp-eventi-ci

## 2026-11-13T09:35:10Z

concept_patch: infrastruttura/eventi-e-ci

## 2026-11-13T09:35:10Z

concept_patch: ingest/u15-codice-struttura

## 2026-11-13T09:35:04Z

concept_batch (2 operation(s)):
- architettura/porte-e-fake
- architettura/regole

## 2026-11-13T09:34:59Z

concept_write: infrastruttura/eventi-e-ci

## 2026-11-13T09:34:50Z

concept_patch: ingest/u13-inventario

## 2026-11-13T09:34:40Z

concept_patch: ingest/u10-gcp-identita-runtime

## 2026-11-13T09:34:31Z

concept_write: infrastruttura/identita-e-runtime

## 2026-11-13T09:34:21Z

concept_patch: ingest/u09-gcp-bootstrap

## 2026-11-13T09:34:16Z

concept_patch: ingest/u14-agents-readme

## 2026-11-13T09:34:09Z

concept_write: infrastruttura/gcp-bootstrap

## 2026-11-13T09:34:02Z

concept_patch: ingest/u12-gcp-osservabilita-integrazioni

## 2026-11-13T09:33:53Z

concept_patch: ingest/u08-primer-roadmap

## 2026-11-13T09:33:48Z

concept_patch: ingest/u15-codice-struttura

## 2026-11-13T09:33:39Z

concept_patch: ingest/u11-gcp-eventi-ci

## 2026-11-13T09:33:39Z

concept_patch: ingest/u07-primer-sviluppo

## 2026-11-13T09:33:30Z

concept_batch (2 operation(s)):
- sviluppo/locale-e-test (+ index entry)
- sviluppo/qualita (+ index entry)

## 2026-11-13T09:33:29Z

concept_patch: ingest/u10-gcp-identita-runtime

## 2026-11-13T09:33:27Z

concept_batch (3 operation(s)):
- progetto/roadmap (+ index entry)
- progetto/questioni-aperte (+ index entry)
- sviluppo/convenzioni-agenti (+ index entry)

## 2026-11-13T09:33:20Z

concept_patch: ingest/u03-primer-regole

## 2026-11-13T09:33:11Z

concept_batch (2 operation(s)):
- architettura/regole (+ index entry)
- architettura/porte-e-fake (+ index entry)

## 2026-11-13T09:33:06Z

concept_patch: ingest/u05-primer-promemoria-agente

## 2026-11-13T09:33:06Z

concept_patch: ingest/u06-primer-sicurezza

## 2026-11-13T09:32:57Z

concept_write: architettura/sicurezza

## 2026-11-13T09:32:54Z

concept_batch (2 operation(s)):
- architettura/promemoria
- architettura/agente

## 2026-11-13T09:32:23Z

concept_patch: ingest/u09-gcp-bootstrap

## 2026-11-13T09:32:21Z

concept_patch: ingest/u08-primer-roadmap

## 2026-11-13T09:32:10Z

concept_patch: ingest/u07-primer-sviluppo

## 2026-11-13T09:32:00Z

concept_batch (2 operation(s)):
- architettura/promemoria (+ index entry)
- architettura/agente (+ index entry)

## 2026-11-13T09:31:58Z

concept_patch: ingest/u02-primer-stack

## 2026-11-13T09:31:53Z

concept_patch: ingest/u01-primer-overview

## 2026-11-13T09:31:51Z

concept_patch: ingest/u04-primer-modello-dati

## 2026-11-13T09:31:49Z

concept_write: progetto/stack

## 2026-11-13T09:31:47Z

concept_write: progetto/overview

## 2026-11-13T09:31:42Z

concept_batch (1 operation(s)):
- architettura/modello-dati (+ index entry)

## 2026-11-13T09:31:01Z

concept_patch: ingest/u05-primer-promemoria-agente

## 2026-11-13T09:31:00Z

concept_patch: ingest/u04-primer-modello-dati

## 2026-11-13T09:31:00Z

concept_patch: ingest/u03-primer-regole

## 2026-11-13T09:31:00Z

concept_patch: ingest/u01-primer-overview

## 2026-11-13T09:31:00Z

concept_patch: ingest/u02-primer-stack

## 2026-11-13T09:30:59Z

concept_patch: ingest/u06-primer-sicurezza

## 2026-11-13T09:30:16Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:46Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:36Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:24Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:13Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:25:24Z

concept_batch (17 operation(s)):
- ingest/piano-family-bot-docs (+ index entry)
- ingest/u01-primer-overview (+ index entry)
- ingest/u02-primer-stack (+ index entry)
- ingest/u03-primer-regole (+ index entry)
- ingest/u04-primer-modello-dati (+ index entry)
- ingest/u05-primer-promemoria-agente (+ index entry)
- ingest/u06-primer-sicurezza (+ index entry)
- ingest/u07-primer-sviluppo (+ index entry)
- ingest/u08-primer-roadmap (+ index entry)
- ingest/u09-gcp-bootstrap (+ index entry)
- ingest/u10-gcp-identita-runtime (+ index entry)
- ingest/u11-gcp-eventi-ci (+ index entry)
- ingest/u12-gcp-osservabilita-integrazioni (+ index entry)
- ingest/u13-inventario (+ index entry)
- ingest/u14-agents-readme (+ index entry)
- ingest/u15-codice-struttura (+ index entry)
- ingest/u16-codice-qualita (+ index entry)

## 2026-11-13T09:22:35Z

source_register: sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci

## 2026-11-13T09:22:35Z

source_register: sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo

## 2026-11-13T09:22:35Z

source_register: sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp

## 2026-11-13T09:22:34Z

source_register: sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud

## 2026-11-13T09:22:34Z

source_register: sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot

## 2026-11-13T09:22:25Z

map_create: ingest

## 2026-11-13T09:22:25Z

map_create: sviluppo

## 2026-11-13T09:22:25Z

map_create: infrastruttura

## 2026-11-13T09:22:25Z

map_create: architettura

## 2026-11-13T09:22:25Z

map_create: progetto

## 2026-11-13T09:22:25Z

map_create: sources

