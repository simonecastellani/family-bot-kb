---
plan: ingest/piano-family-bot-docs
status: done
type: IngestUnit
executor: any
extract: "§7 (compose, fake, contract test, niente mongomock) e §12 (Python, lint, type-check, config da env, commit atomici); non la CI, che è U16"
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
targets: [sviluppo/locale-e-test, sviluppo/qualita]
timestamp: 2026-11-13
title: "U07 — Sviluppo locale, test e qualità"
verify: "le due pagine esistono con le sezioni indicate; i comandi sono quelli del sorgente, identici; nessun path di macchina"
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u07
---
Questa unità produce: `sviluppo/locale-e-test`, `sviluppo/qualita`.

**Fonte** — U07: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `sviluppo/locale-e-test` — nuova: `## Ambiente locale`, `## Fake`, `## Contract test`
- `sviluppo/qualita` — nuova: `## Comandi`, `## Tooling`, `## Convenzioni di codice`

**Cosa estrarre**
§7 (compose, fake, contract test, niente mongomock) e §12 (Python, lint, type-check, config da env, commit atomici); non la CI, che è U16

**Verifica e fine**
le due pagine esistono con le sezioni indicate; i comandi sono quelli del sorgente, identici; nessun path di macchina. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: prima di U16, che aggiunge CI e test reali.
