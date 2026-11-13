---
status: active
timestamp: 2026-11-13
title: Stack tecnologico
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
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
- **Driver MongoDB**: PyMongo con API async nativa; lo stato di Motor va verificato prima di adottarlo.

## Tooling

Stato **[Default]**.

- `uv`, `ruff`, `pyright`, `pytest`, `pre-commit`, Docker.
