---
type: Source
title: "Codice di family-bot — struttura, porte, test e CI"
source_kind: codebase
locator: "{{repo:family-bot}}/{app/,tests/,scripts/,.github/,pyproject.toml,docker-compose.yml,.env.example}"
ingest_status: ingested
source_shape: system
connector: client file read
recipe: "Estrai solo struttura dei moduli, porte e fake implementati, test presenti, workflow CI e servizi del compose. Non copiare corpi di funzioni né dipendenze di dettaglio; escludi .env, .venv, cache, uv.lock e opencode.json."
shape: source
ingested_at: 2026-11-13
---
# Codice di family-bot — struttura, porte, test e CI
