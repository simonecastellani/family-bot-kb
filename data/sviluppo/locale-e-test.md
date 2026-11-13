---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
status: active
timestamp: 2026-11-13
title: Sviluppo locale e test
type: Pagina
---
# Sviluppo locale e test

Ambiente di sviluppo e strategia di test del progetto, dal primer §7. Le scelte di stack e la lista dei tool sono in [[progetto/stack]]; le convenzioni di scrittura del codice in [[sviluppo/qualita]].

## Ambiente locale

- `docker-compose.yml` con **MongoDB** in una versione allineata alle funzionalità supportate da Firestore, e fake per Telegram, Calendar, LLM, STT e coda dei task.
- Le immagini Docker sono minimali e girano non-root (primer §12).
- La configurazione arriva solo da variabili d'ambiente, letta con `pydantic-settings` (primer §12).

## Compose reale

`docker-compose.yml` (nome progetto `family-bot`) definisce due servizi, e solo due:

| Servizio | Immagine / build | Porta | Note |
|---|---|---|---|
| `mongo` | `mongo:8.0` | solo loopback, `127.0.0.1:27017` | volume `mongo_data`; avviato con `--replSet rs0`; l'healthcheck con `mongosh` inizializza il replica set ed è verde quando `rs.status().ok` vale 1 (ogni 10s, timeout 5s, 5 tentativi, 20s di grazia) |
| `api` | build dal `Dockerfile` del repo | solo loopback, `127.0.0.1:8080` | `init: true`, `no-new-privileges`, parte solo a Mongo sano (`depends_on` con `service_healthy`), healthcheck HTTP su `/healthz` |

- L'`api` riceve la configurazione inline: `APP_ENV=local`, `LOG_LEVEL=INFO`, `TIMEZONE=Europe/Rome`, `MONGODB_URI=mongodb://mongo:27017/?directConnection=true`, `MONGODB_DATABASE=family_bot`. Sono un sottoinsieme di `.env.example` e valgono solo dentro il compose; l'app eseguita sul host legge `.env`.
- `directConnection=true` è necessario perché il replica set annuncia il nome del servizio Docker (`mongo:27017`), che dal host non risolve; il driver si collega direttamente al membro.
- I fake elencati sopra non sono servizi del compose, ma implementazioni in memoria dentro l'app: dettagli in [[architettura/porte-e-fake]].
- Nel repo non esiste nessun altro file compose; il comando di avvio è in [[sviluppo/qualita]].

## Fake

- L'ambiente locale include fake per Telegram, Calendar, LLM, STT e coda dei task.
- Il fake di `TaskQueue` esegue (o simula) l'invocazione dell'endpoint interno all'orario previsto.
- Nei test l'LLM è mockato (ad esempio `TestModel`/`FunctionModel` di PydanticAI) e il `Clock` è finto.

## Contract test

- **Contract test dei repository**: una sola suite, eseguita in due contesti — su MongoDB locale a ogni commit e su un database Firestore Enterprise reale di sviluppo in un workflow manuale/notturno. In M1 esiste il primo contesto; il secondo arriva in M2.
- Serve a intercettare le differenze di compatibilità che i test locali non vedono.
- Non usare librerie che simulano MongoDB in memoria al posto di un MongoDB reale: divergono dal comportamento reale più di quanto convenga. Il doppio in memoria del progetto serve solo agli unit test dei servizi.
- La suite **fallisce** se MongoDB non risponde: non viene saltata in silenzio. Prima di `uv run pytest` serve `docker compose up -d mongo`.
- La suite usa un database condiviso (`family_bot_test`): gli indici sono creati una volta e le collection vengono svuotate con `delete_many` attorno a ogni test. Niente `dropDatabase`, che Firestore non supporta.
- Le impostazioni dei test usano `_env_file=None` e rifiutano URI non locali: un `.env` che punta a Firestore non può far scrivere la suite sul database reale.
- La CI GitHub Actions avvia un service `mongo:8.0` con `--replSet rs0` e inizializza il replica set prima della suite.

## Script di verifica

In `scripts/` c'è un solo script: `verify_gcp.sh`, verifica **read-only** delle risorse Google Cloud create a mano.

- Uso: `./scripts/verify_gcp.sh <project-id>`, oppure con `PROJECT_ID` nell'ambiente; `REGION` vale `europe-west8` se non impostata.
- Precondizioni: `gcloud` installato (altrimenti esce con 1) e un project id (altrimenti esce con 2).
- Con `set -euo pipefail` elenca in sequenza: progetto, billing, API abilitate, database Firestore, service account, secret, bucket, Artifact Registry, servizi Cloud Run, code Cloud Tasks, job Cloud Scheduler.
- Solo la lettura del progetto è bloccante; le altre sezioni tollerano l'errore, così un'API non ancora abilitata non ferma la verifica.
- Non crea né modifica nulla: affianca il controllo manuale di [[infrastruttura/gcp-bootstrap]]; l'inventario atteso è in [[infrastruttura/inventario]].

### Suite di test attuale

- `tests/unit/test_config.py`: default locali (`app_env=local`, `LOG_LEVEL=INFO`, `TIMEZONE=Europe/Rome`, regione `europe-west8`, database `family_bot`), override da ambiente, `get_settings()` in cache.
- `tests/unit/test_health.py`: `/healthz` con servizio e ambiente iniettati, `/readyz`, generazione di `/openapi.json`.
- `tests/unit/test_fakes.py`: le porte e i fake di [[architettura/porte-e-fake]], compresi il rifiuto delle scadenze naive e il determinismo dei fake LLM e STT.
- `tests/conftest.py`: fixture `settings` e `client` (TestClient sull'app costruita dalle impostazioni), il `FixedClock`, e una fixture automatica che sposta ogni test in una directory temporanea perché un `.env` locale non venga letto.
- `tests/unit/domain/`: ricorrenze (mese corto, `BYMONTHDAY=-1`, finestra di 60 giorni, cambio d'ora) e validazione dei modelli.
- `tests/unit/services/`: casi d'uso dei task sul doppio in memoria, inclusi rollback e conflitto di versione.
- `tests/contract/`: repository, unit of work e indici su MongoDB reale; marcati `pytest.mark.contract`.
- `tests/integration/`: i sette criteri di accettazione di M1, con transazioni reali e audit atomico.
- `pyproject.toml`: `testpaths = ["tests"]`, `asyncio_mode = "auto"`, output ridotto a `-q`, marker `contract`.
