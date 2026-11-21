---
targets: [sviluppo/convenzioni-agenti, sviluppo/qualita]
title: U14 — Convenzioni del repo e comandi correnti
executor: any
provenance: [sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo]
timestamp: 2026-11-13
type: IngestUnit
verify: le regole rapide non ripetono `architettura/regole` né `sviluppo/qualita`; i comandi coincidono con README e AGENTS; nessuna sezione duplicata
extract: "stato M0, milestone corrente, infrastruttura manuale e regole rapide da AGENTS.md; comandi, stack e struttura reale da README.md. Non duplicare ciò che il primer possiede già"
plan: ingest/piano-family-bot-docs
status: done
claimed_by: subagent-u14
---
Questa unità produce: `sviluppo/convenzioni-agenti`, `sviluppo/qualita`.

**Fonte** — U14: `sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `sviluppo/convenzioni-agenti` — esistente (creata da U08): aggiungi `## Stato della milestone` e `## Regole rapide del repo`
- `sviluppo/qualita` — esistente (creata da U07): aggiungi `## Comandi del repo`

**Cosa estrarre**
stato M0, milestone corrente, infrastruttura manuale e regole rapide da AGENTS.md; comandi, stack e struttura reale da README.md. Non duplicare ciò che il primer possiede già

**Verifica e fine**
le regole rapide non ripetono `architettura/regole` né `sviluppo/qualita`; i comandi coincidono con README e AGENTS; nessuna sezione duplicata. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: dopo U08 (patch su convenzioni-agenti).
