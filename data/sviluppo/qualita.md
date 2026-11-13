---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo, sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
status: active
timestamp: 2026-11-13
title: Qualità del codice
type: Pagina
---
# Qualità del codice

Comandi, tooling e convenzioni di sviluppo, dal primer §12. L'ambiente locale e i test sono in [[sviluppo/locale-e-test]]; i tool adottati e le decisioni di stack in [[progetto/stack]].

## Comandi del repo

Comandi esatti del repository (coincidono in `README.md` e `AGENTS.md`):

```bash
uv sync
uv run ruff check .
uv run ruff format --check .
uv run pyright
uv run pytest
docker compose up --build
```

`pre-commit` è opzionale ma consigliato: `uv run pre-commit install`, poi `uv run pre-commit run --all-files`.

## Pre-commit

La configurazione è `.pre-commit-config.yaml`: un solo repository di hook, `astral-sh/ruff-pre-commit` (rev `v0.17.4`), con due hook — `ruff-check` con `--fix` e `ruff-format`. Sono gli stessi controlli ruff del job `python` della CI, applicati prima del commit; `pyright` e `pytest` non sono hook e restano in CI o a mano.

Installazione ed esecuzione sono in `## Comandi del repo`: restano facoltative, la CI non le richiede.

## CI

Un solo workflow, `.github/workflows/ci.yml`, su `ubuntu-latest`, con due job indipendenti:

| Job | Timeout | Cosa fa |
|---|---|---|
| `python` | 10 min | checkout, `astral-sh/setup-uv@v5` con cache, installazione dal lockfile (`uv sync --frozen`) e poi in ordine i quattro controlli di `## Comandi del repo` (ruff check, ruff format, pyright, pytest) |
| `docker` | 15 min | build dell'immagine `family-bot-api:ci` dal `Dockerfile` e validazione del compose con `docker compose config`; non avvia i servizi |

- Una sola versione di Python, quella fissata dal progetto: nessuna matrice di versioni.
- Il job `docker` non esegue test e non pubblica l'immagine: verifica solo che Dockerfile e compose restino validi.
- Il deploy e l'autenticazione verso Google Cloud non stanno in questo workflow: la federazione è descritta in [[infrastruttura/eventi-e-ci]].

## Tooling

- Python ≥ 3.12, con type hints ovunque.
- `ruff` per il lint; type-check in CI.
- Configurazione solo da env (`pydantic-settings`).
- Immagini Docker minimali, non root.

## Convenzioni di codice

- Commit piccoli e atomici.
- Logging strutturato.
- Mai loggare token o contenuti sensibili non necessari.
