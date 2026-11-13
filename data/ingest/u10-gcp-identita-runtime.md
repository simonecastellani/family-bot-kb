---
verify: ogni risorsa di B e C ha la sua sezione; i ruoli IAM sono quelli del sorgente; nessuna chiave JSON o segreto
executor: large
plan: ingest/piano-family-bot-docs
status: draft
targets: [infrastruttura/identita-e-runtime]
title: "U10 — Identità, segreti, storage e runtime"
extract: "fasi B e C: service account dedicati, Secret Manager, Cloud Storage, guardrail, Artifact Registry, servizi Cloud Run api e worker, service identity e logging"
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
timestamp: 2026-11-13
type: IngestUnit
---
Questa unità produce: `infrastruttura/identita-e-runtime`.

**Fonte**
Leggi `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` (la Source registrata) e nient'altro: le sezioni citate nelle estrazioni sono le sue.

**Target**
- `infrastruttura/identita-e-runtime` — nuova: `## Service account`, `## Segreti e file`, `## Artifact Registry`, `## Cloud Run`, `## Guardrail`

**Cosa estrarre**
fasi B e C: service account dedicati, Secret Manager, Cloud Storage, guardrail, Artifact Registry, servizi Cloud Run api e worker, service identity e logging

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
ogni risorsa di B e C ha la sua sezione; i ruoli IAM sono quelli del sorgente; nessuna chiave JSON o segreto. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: indipendente da U09, ma non scrivere sulla pagina di U09.
