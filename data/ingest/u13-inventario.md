---
status: in-progress
type: IngestUnit
verify: nessun indirizzo email reale né identificativo di fatturazione nella pagina; il progetto di produzione è descritto come non toccato in M0/M1; le API sono elencate
plan: ingest/piano-family-bot-docs
targets: [infrastruttura/inventario, infrastruttura/gcp-bootstrap]
timestamp: 2026-11-13
title: U13 — Inventario GCP reale e ambienti
executor: any
extract: "tutte le tabelle dell'inventario e il registro modifiche; redigi l'email dell'account proprietario e il billing account come <owner-email> e <billing-account>, annotando che i valori reali stanno nel file sorgente"
provenance: [sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]
claimed_at: "2026-11-13T09:34:49.714Z"
claimed_by: subagent-u13
review_after: 2026-11-14
---
Questa unità produce: `infrastruttura/inventario`, `infrastruttura/gcp-bootstrap`.

**Fonte** — U13: `sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `infrastruttura/inventario` — nuova: `## Progetto e ambienti`, `## Budget`, `## Firestore`, `## Service account`, `## API abilitate`, `## Registro modifiche`
- `infrastruttura/gcp-bootstrap` — esistente (creata da U09): aggiungi `## Ambiente di produzione`

**Cosa estrarre**
tutte le tabelle dell'inventario e il registro modifiche; redigi l'email dell'account proprietario e il billing account come <owner-email> e <billing-account>, annotando che i valori reali stanno nel file sorgente

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
nessun indirizzo email reale né identificativo di fatturazione nella pagina; il progetto di produzione è descritto come non toccato in M0/M1; le API sono elencate. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: dopo U09 (patch su gcp-bootstrap).
