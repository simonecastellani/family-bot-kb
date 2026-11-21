---
targets: [infrastruttura/eventi-e-ci]
timestamp: 2026-11-13
verify: "coda, scheduler e pool/provider WIF hanno una sezione ciascuno; nessun riferimento a calendario o login dashboard"
executor: any
extract: "fase D (coda Cloud Tasks e job Scheduler) e E1 (GitHub Actions con WIF); non E2 e E3, che vanno in U12"
plan: ingest/piano-family-bot-docs
title: U11 — Eventi e CI/CD su GCP
type: IngestUnit
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
status: done
claimed_by: subagent-u11
---
Questa unità produce: `infrastruttura/eventi-e-ci`.

**Fonte** — U11: `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `infrastruttura/eventi-e-ci` — nuova: `## Cloud Tasks`, `## Cloud Scheduler`, `## Workload Identity Federation`

**Cosa estrarre**
fase D (coda Cloud Tasks e job Scheduler) e E1 (GitHub Actions con WIF); non E2 e E3, che vanno in U12

**Verifica e fine**
coda, scheduler e pool/provider WIF hanno una sezione ciascuno; nessun riferimento a calendario o login dashboard. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: indipendente.
Pagine prodotte: [[infrastruttura/eventi-e-ci]].
