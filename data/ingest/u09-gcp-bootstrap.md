---
status: open
targets: [infrastruttura/gcp-bootstrap]
title: "U09 — Bootstrap GCP: progetto, billing, API, Firestore"
type: IngestUnit
executor: large
extract: "fase A del documento (A1-A6): account e progetto, budget alert, gcloud, API da abilitare, creazione del database Firestore Enterprise con compatibilità MongoDB, inventario manuale"
plan: ingest/piano-family-bot-docs
timestamp: 2026-11-13
verify: "i passi A1-A6 sono tutti presenti nell'ordine; ogni comando è citato come nel sorgente; i valori di progetto e regione sono coerenti con `infrastruttura/inventario`"
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
---
Questa unità produce: `infrastruttura/gcp-bootstrap`.

**Fonte** — U09: `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `infrastruttura/gcp-bootstrap` — nuova: `## Prerequisiti`, `## Progetto e billing`, `## API`, `## Firestore`, `## Verifica`

**Cosa estrarre**
fase A del documento (A1-A6): account e progetto, budget alert, gcloud, API da abilitare, creazione del database Firestore Enterprise con compatibilità MongoDB, inventario manuale

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
i passi A1-A6 sono tutti presenti nell'ordine; ogni comando è citato come nel sorgente; i valori di progetto e regione sono coerenti con `infrastruttura/inventario`. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: prima di U13, che aggiunge le risorse reali e la distinzione dev/prod.
