---
timestamp: 2026-11-13
type: IngestUnit
extract: "§3 per intero: decisioni [Confermato] e [Default] con il loro stato; non §4 né §5"
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
title: U02 — Decisioni tecnologiche e stato
verify: "ogni voce di §3 compare una volta con il suo stato; nessun [Default] promosso a [Confermato]"
executor: any
status: done
targets: [progetto/stack]
claimed_by: subagent-u02
---
Questa unità produce: `progetto/stack`.

**Fonte** — U02: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `progetto/stack` — nuova: sezioni `## Confermati`, `## Default`, `## Tooling`

**Cosa estrarre**
§3 per intero: decisioni [Confermato] e [Default] con il loro stato; non §4 né §5

**Verifica e fine**
ogni voce di §3 compare una volta con il suo stato; nessun [Default] promosso a [Confermato]. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: indipendente da U01.
Pagine prodotte: [[progetto/stack]].
