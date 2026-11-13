---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-13
title: Qualità del codice
type: Pagina
---
# Qualità del codice

Comandi, tooling e convenzioni di sviluppo, dal primer §12. L'ambiente locale e i test sono in [[sviluppo/locale-e-test]]; i tool adottati e le decisioni di stack in [[progetto/stack]].

## Comandi

- Test: `pytest`.
- Lint: `ruff`.
- Type-check: previsto in CI.

## Tooling

- Python ≥ 3.12, con type hints ovunque.
- `ruff` per il lint; type-check in CI.
- Configurazione solo da env (`pydantic-settings`).
- Immagini Docker minimali, non root.

## Convenzioni di codice

- Commit piccoli e atomici.
- Logging strutturato.
- Mai loggare token o contenuti sensibili non necessari.
