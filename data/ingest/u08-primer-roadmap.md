---
executor: any
extract: "§11 la tabella M0-M10 con i criteri di accettazione, §14 le questioni aperte come elenco aperto, §13 le istruzioni per l'agente; non le regole architetturali (U03)"
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: draft
timestamp: 2026-11-13
title: "U08 — Roadmap, questioni aperte e istruzioni agente"
verify: tutte le milestone M0-M10 hanno criteri; ogni questione aperta di §14 è presente; le convenzioni non duplicano `architettura/regole`
plan: ingest/piano-family-bot-docs
targets: [progetto/roadmap, progetto/questioni-aperte, sviluppo/convenzioni-agenti]
type: IngestUnit
---
Questa unità produce: `progetto/roadmap`, `progetto/questioni-aperte`, `sviluppo/convenzioni-agenti`.

**Fonte** — U08,  `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `progetto/roadmap` — nuova: `## Milestone`, `## Stato corrente`
- `progetto/questioni-aperte` — nuova: `## Aperte`
- `sviluppo/convenzioni-agenti` — nuova: `## Come lavorare`, `## Definition of done`

**Cosa estrarre**
§11 la tabella M0-M10 con i criteri di accettazione, §14 le questioni aperte come elenco aperto, §13 le istruzioni per l'agente; non le regole architetturali (U03)

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
tutte le milestone M0-M10 hanno criteri; ogni questione aperta di §14 è presente; le convenzioni non duplicano `architettura/regole`. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: prima di U14, che aggiunge stato M0 e comandi reali.
