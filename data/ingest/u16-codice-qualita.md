---
verify: il workflow è descritto per job; i servizi del compose corrispondono al file; nessun comando duplicato
executor: any
extract: "il workflow CI, la configurazione pre-commit, i test presenti sotto tests/unit e gli script read-only in scripts/; non i comandi già riportati da U07"
plan: ingest/piano-family-bot-docs
title: "U16 — Test, CI e script del repo"
type: IngestUnit
provenance: [sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
status: done
targets: [sviluppo/qualita, sviluppo/locale-e-test]
timestamp: 2026-11-13
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u16
---
Questa unità produce: `sviluppo/qualita`, `sviluppo/locale-e-test`.

**Fonte** — U16: `sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `sviluppo/qualita` — esistente (creata da U07): aggiungi `## CI` e `## Pre-commit`
- `sviluppo/locale-e-test` — esistente (creata da U07): aggiungi `## Compose reale` e `## Script di verifica`

**Cosa estrarre**
il workflow CI, la configurazione pre-commit, i test presenti sotto tests/unit e gli script read-only in scripts/; non i comandi già riportati da U07

**Verifica e fine**
il workflow è descritto per job; i servizi del compose corrispondono al file; nessun comando duplicato. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: dopo U07 e dopo U14 (patch sulle stesse pagine).
