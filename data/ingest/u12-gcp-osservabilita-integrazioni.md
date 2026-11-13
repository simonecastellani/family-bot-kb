---
title: "U12 — Osservabilità, checklist e integrazioni"
verify: la checklist è riportata come elenco spuntabile; `architettura/integrazioni` copre le tre integrazioni; la sicurezza cita il login senza duplicare U06
extract: "la sezione osservabilità/quote/sicurezza operativa, la checklist di verifica, le decisioni verificate e le fasi E2 (Calendar) ed E3 (login dashboard); non E1, già in U11"
plan: ingest/piano-family-bot-docs
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
status: draft
targets: [infrastruttura/osservabilita, architettura/integrazioni, architettura/sicurezza]
timestamp: 2026-11-13
type: IngestUnit
executor: large
---
Questa unità produce: `infrastruttura/osservabilita`, `architettura/integrazioni`, `architettura/sicurezza`.

**Target**
- `infrastruttura/osservabilita` — nuova: `## Osservabilità`, `## Quote`, `## Checklist di verifica`, `## Decisioni verificate`
- `architettura/integrazioni` — nuova: `## Google Calendar`, `## Speech to text`, `## Link e immagini`
- `architettura/sicurezza` — esistente (creata da U06): aggiungi `## Login della dashboard`

**Cosa estrarre**
la sezione osservabilità/quote/sicurezza operativa, la checklist di verifica, le decisioni verificate e le fasi E2 (Calendar) ed E3 (login dashboard); non E1, già in U11

**Collocazione già decisa**
Il soggetto di questa unità è posseduto dai target qui sopra: non cercare altre pagine e non crearne di nuove oltre a quelle marcate come nuove. Se ti serve un fatto che appartiene a un'altra pagina, non copiarlo: segnalalo nel digest.

**Conflitti**
Se il sorgente contraddice una pagina esistente, apri una Contradiction (`contradiction_kind: open_question`) e lascia la pagina com'è. Non scegliere da solo e non riscrivere la storia della pagina.

**Verifica e fine**
la checklist è riportata come elenco spuntabile; `architettura/integrazioni` copre le tre integrazioni; la sicurezza cita il login senza duplicare U06. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

**Lease**
Lease 1 giorno: al claim scrivi `claimed_by` e `review_after` (domani, in UTC). Il digest finale sta sotto 10 righe: pagine toccate, lacune, contraddizioni, esito del gate.

Piano: [ingest/piano-family-bot-docs](piano-family-bot-docs).
Ordine: dopo U06 (patch su `architettura/sicurezza`); indipendente da U11.
