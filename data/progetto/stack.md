---
title: Stack tecnologico
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-16
---
# Stack tecnologico

Decisioni tecnologiche del progetto family-bot come riportate nel primer §3, con il loro stato di partenza. Fonte: [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].

## Confermati

Stato **[Confermato]**.

- **Soluzione full cloud serverless** su GCP; obiettivo = pratica cloud-native.
- **Interfaccia di chat**: bot Telegram. Telegram Mini App **esclusa**.
- **Integrazione Google Calendar**.
- **LLM**: OpenCode Go (endpoint OpenAI-compatibile), chiamato via HTTPS dal cloud.
- **Linguaggio e framework**: Python, FastAPI, Pydantic v2.
- **Database**: Firestore, edizione Enterprise con compatibilità MongoDB. Le operazioni sui dati sono gestite lato applicazione tramite interfacce/driver compatibili MongoDB, così da poter testare in locale con MongoDB durante lo sviluppo.
- **Dashboard in sola lettura** per ora; l'editing è un'evolutiva, con DB e service layer pensati per una SPA futura.
- **Un unico service layer**, condiviso da Telegram, tool dell'agente e web.

## Default

Stato **[Default]**: scelte di partenza, non ancora confermate.

- **Compute**: Cloud Run, con i servizi `api` e `worker` in scale-to-zero.
- **Promemoria**: Cloud Tasks (task differiti con orario di esecuzione), più Cloud Scheduler solo per i job periodici.
- **Telegram**: webhook con secret token, non polling.
- **Segreti**: Secret Manager. **File**: Cloud Storage.
- **Auth dashboard**: login Google (OIDC, ad esempio Firebase Auth); il backend verifica l'ID token e mappa l'email su `members`.
- **STT**: servizio gestito (Speech-to-Text) oppure audio nativo del modello, se disponibile.
- **Framework agente**: PydanticAI, in alternativa un loop manuale con l'SDK `openai`; LangChain e LangGraph esclusi.
- **IaC**: Terraform, introdotto dopo la fase manuale su GCP (M0–M2). **CI/CD**: GitHub Actions con Workload Identity Federation (niente chiavi JSON). **Registry**: Artifact Registry.
- **Osservabilità**: OpenTelemetry verso Cloud Logging e Trace; budget alert dal primo giorno.
- **Driver MongoDB**: PyMongo con API async nativa (`AsyncMongoClient`, 4.19+). Motor è escluso: deprecato, end of life il 14 maggio 2026 (verificato il 2026-11-13).

## Tooling

Stato **[Default]**.

- `uv`, `ruff`, `pyright`, `pytest`, `pre-commit`, Docker.


## Decisioni prese in M2

Stato **[Confermato]**, decise il 2026-11-16 prima dell'inizio dei lavori.

- **Libreria Telegram**: `python-telegram-bot` ≥ 20 (v22.x) in webhook. Si usa `telegram.Bot` come client async, **non** `Application`/`Dispatcher`: il route FastAPI verifica il secret token, deserializza con `Update.de_json` e chiama un servizio, così il flusso resta uno solo e il framework non entra nei servizi. Verificato sui documenti v22 il 2026-11-16: `Bot` è un async context manager con `initialize()`/`shutdown()`; `set_webhook(secret_token=…)` imposta l'header `X-Telegram-Bot-Api-Secret-Token`; `BadRequest` deriva da `NetworkError`, quindi l'ordine degli `except` conta.
- **Agente**: PydanticAI resta il framework, ma dietro una porta `AgentEngine` con fake locale: i servizi e le interfacce non importano PydanticAI. Il segnaposto `LLMClient` viene ritirato.
- **Endpoint LLM**: `https://opencode.ai/inference/go/openai/v1` (OpenAI-compatibile, provider `@ai-sdk/openai-compatible`), modello scelto da env (`LLM_MODEL`).
- **Provider LLM di sviluppo**: OpenCode Go, con header `x-opencode-session` e user agent proprio (obbligatori). Il piano Go è destinato ai coding agent: la chiave si usa **solo durante lo sviluppo di M2**, e il provider va sostituito prima della produzione ([[progetto/m2-deploy-telegram-agente]]).
- **Conferme**: le azioni proposte dall'agente vivono in `agent_runs.actions[]`, non in una collection dedicata, e vengono eseguite solo dopo il click sul bottone inline (vedi [[architettura/agente]]).
