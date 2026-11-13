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
status: in-progress
claimed_by: subagent-u11
review_after: 2026-11-14
---
Questa unità produce: `infrastruttura/eventi-e-ci`.

**Fonte** — U11: `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `infrastruttura/eventi-e-ci` — nuova: `## Cloud Tasks`, `## Cloud Scheduler`, `## Workload Identity Federation`

**Cosa estrarre**
fase D (coda Cloud Tasks e job Scheduler) e E1 (GitHub Actions con WIF); non E2 e E3, che vanno in U12

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
coda, scheduler e pool/provider WIF hanno una sezione ciascuno; nessun riferimento a calendario o login dashboard. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: indipendente.
