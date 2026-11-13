---
timestamp: 2026-11-13
type: IngestUnit
extract: "§3 per intero: decisioni [Confermato] e [Default] con il loro stato; non §4 né §5"
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
title: U02 — Decisioni tecnologiche e stato
verify: "ogni voce di §3 compare una volta con il suo stato; nessun [Default] promosso a [Confermato]"
executor: any
status: draft
targets: [progetto/stack]
---
Questa unità produce: `progetto/stack`.

**Fonte**
Fonte di U02: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `progetto/stack` — nuova: sezioni `## Confermati`, `## Default`, `## Tooling`

**Cosa estrarre**
§3 per intero: decisioni [Confermato] e [Default] con il loro stato; non §4 né §5

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
ogni voce di §3 compare una volta con il suo stato; nessun [Default] promosso a [Confermato]. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: indipendente da U01.
