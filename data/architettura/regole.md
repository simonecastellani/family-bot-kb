---
timestamp: 2026-11-13
title: "Regole architetturali, flusso e struttura del repo"
type: Architecture
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
---
# Regole architetturali, flusso e struttura del repo

Flusso delle richieste, struttura del repository e regole architetturali del primer (§4 e §5). Le dipendenze cloud stanno dietro porte descritte in [[architettura/porte-e-fake]].

## Flusso

Percorso delle richieste (§4):

```mermaid
flowchart TD
  TG[App Telegram] --> BOT[Telegram Bot API]
  BOT <-->|webhook HTTPS| API[Cloud Run: api]
  BR[Browser, login Google OIDC] --> API
  API --> FS[(Firestore - API MongoDB)]
  API --> GCS[Cloud Storage]
  API --> CAL[Google Calendar API]
  API --> LLM[LLM + Speech]
```

Promemoria event-driven (§4):

```mermaid
flowchart LR
  API[Cloud Run: api] -->|crea task differito| CT[Cloud Tasks]
  CT -->|a scadenza, HTTP+OIDC| W[Cloud Run: worker]
  W <--> FS[(Firestore)]
  W -->|sendMessage| TG[Telegram]
  SCH[Cloud Scheduler] -->|digest, sync, materializzazione| W
```

## Struttura del repo

Struttura prevista dal primer (§4):

```
app/
  domain/          # modelli Pydantic, enum, regole pure (ricorrenze, carico)
  schemas/         # DTO: contratto API (futura SPA)
  services/        # casi d'uso: commands/ e queries/, sempre con Actor
  repositories/    # porte (Protocol) per aggregato + adapter mongo/ e in-memory
  integrations/    # google_calendar, llm, stt, link_fetcher, telegram_client, task_queue
  interfaces/
    telegram/      # webhook handler
    agent/         # tool PydanticAI = wrapper sottili dei services
    api/           # FastAPI /api/v1 (solo GET), auth OIDC, error mapping
    internal/      # endpoint per Cloud Tasks/Scheduler (non pubblici)
    web/           # viste temporanee
  core/            # config, clock, auth, audit, logging/tracing
infra/             # Terraform (futuro)
tests/             # unit, contract (repository), integration
docker-compose.yml # MongoDB locale + fake
```

## Regole non negoziabili

Dieci regole architetturali, numerate come nel sorgente (§5):

1. **Nessuna logica di business in `interfaces/`**: traducono soltanto.
2. Ogni servizio riceve un `Actor(member_id, source)` con `source ∈ {telegram, web, agent, scheduler}`.
3. I servizi restituiscono **DTO**, mai documenti grezzi del DB.
4. **Solo i repository toccano il client MongoDB.** I servizi usano le porte `Protocol`; niente query nei servizi.
5. Nei repository usa un **sottoinsieme minimo e documentato** dell'API MongoDB (CRUD, filtri semplici, indici, aggregazioni basilari). Ogni operatore o stage nuovo va verificato sulla documentazione di compatibilità di Firestore **prima** dell'uso; se non è supportato, sposta la logica in Python.
6. Il tempo passa da un `Clock` iniettato; niente `datetime.now()` fuori da `core/clock.py`.
7. Ogni scrittura produce un `audit_events` (actor, source, azione, entità, diff).
8. Gli endpoint **interni** (Cloud Tasks/Scheduler) non sono pubblici: verificano il token OIDC del service account chiamante.
9. Tutto ciò che è **idempotente per costruzione** lo resta: i worker possono essere invocati più volte (retry) senza effetti doppi.
10. Le dipendenze cloud stanno dietro porte (`TaskQueue`, `BlobStore`, `SecretProvider`, `LLMClient`, `STTClient`, `Clock`), con adapter reali e fake locali.
