---
extract: "§8 i sei passi del flusso event-driven; §9 tool, conferma esplicita, contenuto non fidato, agent_runs. Non la configurazione GCP, che è di U11"
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: draft
targets: [architettura/promemoria, architettura/agente]
timestamp: 2026-11-13
type: IngestUnit
verify: "i sei passi di §8 sono numerati; esistono le tre sezioni dell'agente; nessun comando gcloud"
executor: any
plan: ingest/piano-family-bot-docs
title: U05 — Promemoria event-driven e agente
---
Questa unità produce: `architettura/promemoria`, `architettura/agente`.

**Fonte** — U05,  `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/promemoria` — nuova: `## Flusso`, `## Idempotenza`, `## Scheduler`
- `architettura/agente` — nuova: `## Tool`, `## Conferme`, `## Registrazione delle run`

**Cosa estrarre**
§8 i sei passi del flusso event-driven; §9 tool, conferma esplicita, contenuto non fidato, agent_runs. Non la configurazione GCP, che è di U11

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
i sei passi di §8 sono numerati; esistono le tre sezioni dell'agente; nessun comando gcloud. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: indipendente.
