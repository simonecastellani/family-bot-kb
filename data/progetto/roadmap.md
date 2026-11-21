---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-18
title: Roadmap
type: Pagina
---
# Roadmap

Milestone del progetto e criteri con cui ciascuna si considera chiusa (§11). L'ordine è sequenziale: una milestone alla volta, senza anticipare le successive (§13). Fonte: [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].

## Milestone

| # | Milestone | Criteri di accettazione |
|---|---|---|
| M0 | Fondamenta: repo, `uv`, lint/type-check, config, Dockerfile, compose con MongoDB, porte e fake, setup GCP manuale (progetto, API, budget alert), CI | `pytest` e lint passano; l'app parte in container; checklist Fase A verificata |
| M1 | Task core su MongoDB: modelli, repository con **contract test**, servizi, RRULE, audit | Test su ricorrenze mensili con finestra, completamento, snooze, concorrenza ottimistica |
| M2 | Deploy su Cloud Run + webhook Telegram + agente con tool dei task; Secret Manager; CD con Workload Identity Federation; `agent_runs` | "Ricordami di chiamare l'idraulico giovedì" crea un task previa conferma, in cloud |
| M3 | Promemoria event-driven: Cloud Tasks + worker + dedupe + Scheduler (digest) | Nessun doppio invio sotto retry; task rimandato → nessun invio al vecchio orario |
| M4 | Google Calendar: sync con `syncToken` in `calendar_events`, tool di lettura, creazione evento con conferma | Sync idempotente; gestione eventi cancellati e token scaduto |
| M4.5 | Routing locale: Laya in shadow, dataset etichettato, fine-tuning, canary delle letture | Accuratezza held-out ≥ 95%; **0** scritture instradate senza LLM; chiamate LLM ridotte |
| M4.6 | Eventi esistenti: `update_event`/`delete_event` con conferma, `If-Match`, cache e audit | Correzione senza duplicati; cancellazione idempotente — `v0.6.0` |
| M4.7 | Eventi ricorrenti: serie in calendario (`rrule`), cancellazione di una occorrenza o della serie con bottoni etichettati, sync best-effort | «Tutti i giovedì» crea una serie; niente righe fantasma; la scelta una/tutte è un bottone — `v0.7.0` |
| M5 | Dashboard in sola lettura: `/api/v1` GET, login Google OIDC, viste minime | Utente non registrato → 403; log agente solo admin |
| M6 | Vocali con STT gestito | Un vocale in italiano diventa un task corretto |
| M7 | Link e immagini: estrazione evento con conferma | Un link con data/luogo produce una proposta strutturata |
| M8 | Menu: documenti su GCS, estrazione menu del nido, piano settimanale, lista della spesa | Piano `draft` → `confirmed`; modifica a linguaggio naturale |
| M9 | Osservabilità e operatività: OpenTelemetry, dashboard/alert, runbook, strategia di backup | Un errore nel worker è tracciabile end-to-end e genera un alert |
| M10 | Memoria della conversazione: contesto breve dei turni precedenti, da valutare anche con Cartographer come memoria del sistema | Il bot può rispondere a una propria domanda al turno dopo; finestra breve e costo misurato |
| M11 | *(Dopo settimane di uso)* Chores e carico: **prima progetta**, poi implementa | Carico calcolato da funzione deterministica; l'LLM propone e motiva |

Per l'use case 4 (gestione della casa) vale la regola generale: il bot **propone**, ogni membro **conferma** (§11).

## Stato corrente

- **M0 — fondamenta** è completata.
- **M1 — Task core** è completata (`done`): obiettivo, decisioni, criteri ed esito in [[progetto/m1-task-core]].
- **M2 — Deploy, Telegram e agente** è completata (`done`): criterio raggiunto dal telefono, rilascio `v0.2.0` con canary. Piano, decisioni ed esito in [[progetto/m2-deploy-telegram-agente]].
- **M3 — Promemoria event-driven** è completata (`done`): rilascio `v0.3.0` con coda Cloud Tasks, worker privato, due job Scheduler e bottoni sul promemoria. Esito e ostacoli in [[progetto/m3-promemoria-event-driven]].
- **M4 — Google Calendar** è `done` (2026-11-19): sync in cache, cancellati e token scaduto gestiti, creazione con conferma verificata dal telefono. Esito in [[progetto/m4-google-calendar]].
- **M4.5 — Routing locale** è `in-progress` (2026-11-21): shadow via MCP deployato in `v0.5.0`, baseline zero-shot 68,3%; restano fine-tuning e canary. Dettagli in [[progetto/m4-5-routing-locale]]. **M4.6 — Eventi esistenti** è `done` (2026-11-21): rilasciata in produzione (`v0.6.0`), correzione senza duplicati e cancellazione idempotente. **M4.7 — Eventi ricorrenti** è `in-progress` (2026-11-21): «tutti i giovedì» crea una serie, la cancellazione è una scelta fra una occorrenza e tutta la serie. Dettagli in [[progetto/m4-7-eventi-ricorrenti]]. **M5 — Dashboard** slitta a `v0.8.0`. **M5 — Dashboard** slitta a `v0.7.0`. Dettagli in [[progetto/m4-6-eventi-esistenti]].
- Il passaggio da una milestone alla successiva si misura con i **criteri di accettazione** della tabella, non con il numero di attività svolte.
- **M10** non è pianificabile a calendario: si affronta solo dopo settimane di uso reale, e la prima attività è la progettazione, non l'implementazione (§11).
- Le domande che possono bloccare o rimandare una milestone sono raccolte in [[progetto/questioni-aperte]].
- Le istruzioni con cui l'agente lavora e la definizione di done sono in [[sviluppo/convenzioni-agenti]].
- Le regole architetturali non negoziabili sono in [[architettura/regole]].
