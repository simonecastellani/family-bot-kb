---
verify: ogni risorsa di B e C ha la sua sezione; i ruoli IAM sono quelli del sorgente; nessuna chiave JSON o segreto
executor: large
plan: ingest/piano-family-bot-docs
status: done
targets: [infrastruttura/identita-e-runtime]
title: "U10 — Identità, segreti, storage e runtime"
extract: "fasi B e C: service account dedicati, Secret Manager, Cloud Storage, guardrail, Artifact Registry, servizi Cloud Run api e worker, service identity e logging"
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
timestamp: 2026-11-13
type: IngestUnit
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u10
---
Questa unità produce: `infrastruttura/identita-e-runtime`.

**Fonte** — U10: `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `infrastruttura/identita-e-runtime` — nuova: `## Service account`, `## Segreti e file`, `## Artifact Registry`, `## Cloud Run`, `## Guardrail`

**Cosa estrarre**
fasi B e C: service account dedicati, Secret Manager, Cloud Storage, guardrail, Artifact Registry, servizi Cloud Run api e worker, service identity e logging

**Verifica e fine**
ogni risorsa di B e C ha la sua sezione; i ruoli IAM sono quelli del sorgente; nessuna chiave JSON o segreto. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: indipendente da U09, ma non scrivere sulla pagina di U09.
