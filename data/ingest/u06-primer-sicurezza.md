---
timestamp: 2026-11-13
verify: "le quattro sezioni esistono; webhook, dashboard e endpoint interni hanno ciascuno la propria regola di auth; nessun segreto o token"
executor: large
title: "U06 — Sicurezza, auth e dati verso il LLM"
type: IngestUnit
extract: §10 per intero più i vincoli di §5 su Actor e audit; non la procedura di creazione dei service account (U10) né il login in dettaglio (U12)
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: done
targets: [architettura/sicurezza]
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u06
---
Questa unità produce: `architettura/sicurezza`.

**Fonte** — U06: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/sicurezza` — nuova: `## Autenticazione`, `## Identità e privilegi`, `## Segreti`, `## Dati verso il provider LLM`

**Cosa estrarre**
§10 per intero più i vincoli di §5 su Actor e audit; non la procedura di creazione dei service account (U10) né il login in dettaglio (U12)

**Verifica e fine**
le quattro sezioni esistono; webhook, dashboard e endpoint interni hanno ciascuno la propria regola di auth; nessun segreto o token. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: prima di U12, che aggiunge il login dashboard.
Pagine prodotte: [[architettura/sicurezza]].
