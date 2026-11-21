---
timestamp: 2026-11-13
title: U15 — Struttura del codice e porte reali
extract: "i moduli presenti sotto app/ (core, domain, integrations, interfaces), le porte con i loro fake, e i test unitari che li coprono; non corpi di funzioni"
plan: ingest/piano-family-bot-docs
status: done
type: IngestUnit
verify: "ogni porta reale ha il suo fake o l'indicazione che manca; la struttura attuale è distinguibile da quella prevista dal primer; nessun dettaglio implementativo"
executor: any
provenance: [sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
targets: [architettura/porte-e-fake, architettura/regole]
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u15
---
Questa unità produce: `architettura/porte-e-fake`, `architettura/regole`.

**Fonte** — U15: `sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/porte-e-fake` — esistente (creata da U03): sostituisci lo scheletro con `## Porte implementate` e `## Fake locali`
- `architettura/regole` — esistente (creata da U03): aggiungi `## Struttura attuale dei moduli`

**Cosa estrarre**
i moduli presenti sotto app/ (core, domain, integrations, interfaces), le porte con i loro fake, e i test unitari che li coprono; non corpi di funzioni

**Verifica e fine**
ogni porta reale ha il suo fake o l'indicazione che manca; la struttura attuale è distinguibile da quella prevista dal primer; nessun dettaglio implementativo. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: dopo U03 (patch su due pagine create da U03).
