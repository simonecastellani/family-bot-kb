---
type: IngestUnit
extract: "§4 (i due diagrammi Mermaid e il blocco della struttura del repo) e §5 (le dieci regole, numerate come nel sorgente); non il modello dati, non i flussi di promemoria o agente"
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
timestamp: 2026-11-13
verify: le dieci regole sono presenti e numerate; i due diagrammi sono blocchi Mermaid validi; `architettura/porte-e-fake` elenca solo le porte previste
executor: large
status: draft
targets: [architettura/regole, architettura/porte-e-fake]
title: U03 — Architettura e regole non negoziabili
---
Questa unità produce: `architettura/regole`, `architettura/porte-e-fake`.

**Target**
- `architettura/regole` — nuova: `## Flusso`, `## Struttura del repo`, `## Regole non negoziabili`
- `architettura/porte-e-fake` — nuova, solo scheletro: `## Porte previste`

**Cosa estrarre**
§4 (i due diagrammi Mermaid e il blocco della struttura del repo) e §5 (le dieci regole, numerate come nel sorgente); non il modello dati, non i flussi di promemoria o agente

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
le dieci regole sono presenti e numerate; i due diagrammi sono blocchi Mermaid validi; `architettura/porte-e-fake` elenca solo le porte previste. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: prima di U15, che riempie `porte-e-fake` con il codice reale.
