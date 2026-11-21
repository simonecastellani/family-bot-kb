---
status: done
targets: [infrastruttura/gcp-bootstrap]
title: "U09 — Bootstrap GCP: progetto, billing, API, Firestore"
type: IngestUnit
executor: large
extract: "fase A del documento (A1-A6): account e progetto, budget alert, gcloud, API da abilitare, creazione del database Firestore Enterprise con compatibilità MongoDB, inventario manuale"
plan: ingest/piano-family-bot-docs
timestamp: 2026-11-13
verify: "i passi A1-A6 sono tutti presenti nell'ordine; ogni comando è citato come nel sorgente; i valori di progetto e regione sono coerenti con `infrastruttura/inventario`"
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u09
---
Questa unità produce: `infrastruttura/gcp-bootstrap`.

**Fonte** — U09: `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `infrastruttura/gcp-bootstrap` — nuova: `## Prerequisiti`, `## Progetto e billing`, `## API`, `## Firestore`, `## Verifica`

**Cosa estrarre**
fase A del documento (A1-A6): account e progetto, budget alert, gcloud, API da abilitare, creazione del database Firestore Enterprise con compatibilità MongoDB, inventario manuale

**Verifica e fine**
i passi A1-A6 sono tutti presenti nell'ordine; ogni comando è citato come nel sorgente; i valori di progetto e regione sono coerenti con `infrastruttura/inventario`. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: prima di U13, che aggiunge le risorse reali e la distinzione dev/prod.
Pagine prodotte: [[infrastruttura/gcp-bootstrap]].
