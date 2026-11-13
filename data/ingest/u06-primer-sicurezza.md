---
timestamp: 2026-11-13
verify: "le quattro sezioni esistono; webhook, dashboard e endpoint interni hanno ciascuno la propria regola di auth; nessun segreto o token"
executor: large
title: "U06 — Sicurezza, auth e dati verso il LLM"
type: IngestUnit
extract: §10 per intero più i vincoli di §5 su Actor e audit; non la procedura di creazione dei service account (U10) né il login in dettaglio (U12)
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: draft
targets: [architettura/sicurezza]
---
Questa unità produce: `architettura/sicurezza`.

**Target**
- `architettura/sicurezza` — nuova: `## Autenticazione`, `## Identità e privilegi`, `## Segreti`, `## Dati verso il provider LLM`

**Cosa estrarre**
§10 per intero più i vincoli di §5 su Actor e audit; non la procedura di creazione dei service account (U10) né il login in dettaglio (U12)

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
le quattro sezioni esistono; webhook, dashboard e endpoint interni hanno ciascuno la propria regola di auth; nessun segreto o token. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: prima di U12, che aggiunge il login dashboard.
