---
title: U04 — Modello dati documentale
type: IngestUnit
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
verify: ogni riga della tabella collection è riportata; i vincoli free tier sono separati dalle convenzioni; nessun segreto
executor: large
extract: "§6: convenzioni documentali, la tabella delle collection e i vincoli Firestore Enterprise con la nota su TTL e backup"
status: done
targets: [architettura/modello-dati]
timestamp: 2026-11-13
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u04
---
Questa unità produce: `architettura/modello-dati`.

**Fonte** — U04: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/modello-dati` — nuova: `## Convenzioni`, `## Collection`, `## Vincoli del free tier`

**Cosa estrarre**
§6: convenzioni documentali, la tabella delle collection e i vincoli Firestore Enterprise con la nota su TTL e backup

**Verifica e fine**
ogni riga della tabella collection è riportata; i vincoli free tier sono separati dalle convenzioni; nessun segreto. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: indipendente; U09 e U13 possono citarla solo dopo che esiste.
