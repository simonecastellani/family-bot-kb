---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo]
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

## Tooling

- Python ≥ 3.12, con type hints ovunque.
- `ruff` per il lint; type-check in CI.
- Configurazione solo da env (`pydantic-settings`).
- Immagini Docker minimali, non root.

## Convenzioni di codice

- Commit piccoli e atomici.
- Logging strutturato.
- Mai loggare token o contenuti sensibili non necessari.
