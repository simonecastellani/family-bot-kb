---
extract: "§8 i sei passi del flusso event-driven; §9 tool, conferma esplicita, contenuto non fidato, agent_runs. Non la configurazione GCP, che è di U11"
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: done
targets: [architettura/promemoria, architettura/agente]
timestamp: 2026-11-13
type: IngestUnit
verify: "i sei passi di §8 sono numerati; esistono le tre sezioni dell'agente; nessun comando gcloud"
executor: any
plan: ingest/piano-family-bot-docs
title: U05 — Promemoria event-driven e agente
claimed_at: "2026-11-13T00:00:00Z"
claimed_by: subagent-u05
---
Questa unità produce: `architettura/promemoria`, `architettura/agente`.

**Fonte** — U05: `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` — la Source registrata; leggi solo le sezioni citate nell'estrazione qui sotto.

**Target**
- `architettura/promemoria` — nuova: `## Flusso`, `## Idempotenza`, `## Scheduler`
- `architettura/agente` — nuova: `## Tool`, `## Conferme`, `## Registrazione delle run`

**Cosa estrarre**
§8 i sei passi del flusso event-driven; §9 tool, conferma esplicita, contenuto non fidato, agent_runs. Non la configurazione GCP, che è di U11

**Verifica e fine**
i sei passi di §8 sono numerati; esistono le tre sezioni dell'agente; nessun comando gcloud. Prima di chiudere esegui `gate_check` con `changed_ids` uguale ai target qui sopra: zero finding.

Ordine: indipendente.
Pagine prodotte: [[architettura/promemoria]], [[architettura/agente]].
