---
title: U04 — Modello dati documentale
type: IngestUnit
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
verify: ogni riga della tabella collection è riportata; i vincoli free tier sono separati dalle convenzioni; nessun segreto
executor: large
extract: "§6: convenzioni documentali, la tabella delle collection e i vincoli Firestore Enterprise con la nota su TTL e backup"
status: draft
targets: [architettura/modello-dati]
timestamp: 2026-11-13
---
Questa unità produce: `architettura/modello-dati`.

**Fonte** — U04: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/modello-dati` — nuova: `## Convenzioni`, `## Collection`, `## Vincoli del free tier`

**Cosa estrarre**
§6: convenzioni documentali, la tabella delle collection e i vincoli Firestore Enterprise con la nota su TTL e backup

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
ogni riga della tabella collection è riportata; i vincoli free tier sono separati dalle convenzioni; nessun segreto. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: indipendente; U09 e U13 possono citarla solo dopo che esiste.
