---
timestamp: 2026-11-13
type: IngestUnit
verify: "i quattro use case sono presenti con l'ordine 1-2 → 3 → 4; §15 è un elenco; nessun link a pagine non esistenti"
executor: any
extract: "§1 obiettivo e principi, §2 i quattro use case con il loro ordine, §15 fuori scope; non stack, architettura o roadmap"
plan: ingest/piano-family-bot-docs
status: in-progress
title: "U01 — Obiettivo, use case e fuori scope"
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
targets: [progetto/overview]
claimed_at: "2026-11-13T09:31:00.638Z"
claimed_by: subagent-u01
review_after: 2026-11-14
---
Questa unità produce: `progetto/overview`.

**Fonte** — U01: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `progetto/overview` — nuova: sezioni `## Obiettivo`, `## Use case`, `## Fuori scope`

**Cosa estrarre**
§1 obiettivo e principi, §2 i quattro use case con il loro ordine, §15 fuori scope; non stack, architettura o roadmap

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
i quattro use case sono presenti con l'ordine 1-2 → 3 → 4; §15 è un elenco; nessun link a pagine non esistenti. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: unità autonoma, nessun prerequisito.
