---
targets: [sviluppo/convenzioni-agenti, sviluppo/qualita]
title: U14 — Convenzioni del repo e comandi correnti
executor: any
provenance: [sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo]
timestamp: 2026-11-13
type: IngestUnit
verify: le regole rapide non ripetono `architettura/regole` né `sviluppo/qualita`; i comandi coincidono con README e AGENTS; nessuna sezione duplicata
extract: "stato M0, milestone corrente, infrastruttura manuale e regole rapide da AGENTS.md; comandi, stack e struttura reale da README.md. Non duplicare ciò che il primer possiede già"
plan: ingest/piano-family-bot-docs
status: draft
---
Questa unità produce: `sviluppo/convenzioni-agenti`, `sviluppo/qualita`.

**Fonte** — U14,  `sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `sviluppo/convenzioni-agenti` — esistente (creata da U08): aggiungi `## Stato della milestone` e `## Regole rapide del repo`
- `sviluppo/qualita` — esistente (creata da U07): aggiungi `## Comandi del repo`

**Cosa estrarre**
stato M0, milestone corrente, infrastruttura manuale e regole rapide da AGENTS.md; comandi, stack e struttura reale da README.md. Non duplicare ciò che il primer possiede già

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
le regole rapide non ripetono `architettura/regole` né `sviluppo/qualita`; i comandi coincidono con README e AGENTS; nessuna sezione duplicata. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: dopo U08 (patch su convenzioni-agenti).
