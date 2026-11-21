---
type: IngestUnit
extract: "§4 (i due diagrammi Mermaid e il blocco della struttura del repo) e §5 (le dieci regole, numerate come nel sorgente); non il modello dati, non i flussi di promemoria o agente"
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
timestamp: 2026-11-13
verify: le dieci regole sono presenti e numerate; i due diagrammi sono blocchi Mermaid validi; `architettura/porte-e-fake` elenca solo le porte previste
executor: large
status: done
targets: [architettura/regole, architettura/porte-e-fake]
title: U03 — Architettura e regole non negoziabili
---
Questa unità produce: `architettura/regole`, `architettura/porte-e-fake`.

**Fonte** — U03: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/regole` — nuova: `## Flusso`, `## Struttura del repo`, `## Regole non negoziabili`
- `architettura/porte-e-fake` — nuova, solo scheletro: `## Porte previste`

**Cosa estrarre**
§4 (i due diagrammi Mermaid e il blocco della struttura del repo) e §5 (le dieci regole, numerate come nel sorgente); non il modello dati, non i flussi di promemoria o agente

**Verifica e fine**
le dieci regole sono presenti e numerate; i due diagrammi sono blocchi Mermaid validi; `architettura/porte-e-fake` elenca solo le porte previste. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: prima di U15, che riempie `porte-e-fake` con il codice reale.
