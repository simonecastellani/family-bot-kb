---
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-18
title: Roadmap
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
| M4.5 | Routing locale: Laya in shadow, dataset etichettato, fine-tuning, canary delle letture | Accuratezza held-out ≥ 95%; **0** scritture instradate senza LLM; chiamate LLM ridotte — chiusa il 2026-11-22, gate in fase shadow |
| M4.6 | Eventi esistenti: `update_event`/`delete_event` con conferma, `If-Match`, cache e audit | Correzione senza duplicati; cancellazione idempotente — `v0.6.0` |
| M4.7 | Eventi ricorrenti: serie in calendario (`rrule`), cancellazione di una occorrenza o della serie con bottoni etichettati, più date in una sola conferma, sync best-effort | «Tutti i giovedì» crea una serie; niente righe fantasma; la scelta una/tutte è un bottone — **raggiunto il 2026-11-22** (`v0.7.3`) |
| M5 | Dashboard in sola lettura: `/api/v1` GET, login Google OIDC, viste minime | Utente non registrato → 403; log agente solo admin — **raggiunto il 2026-11-23** (`v0.8.0`) |
| M6 | Vocali con STT gestito | Un vocale in italiano diventa un task corretto — **raggiunto il 2026-11-24** (`v0.9.0`); dettagli in [[progetto/m6-vocali]] |
| M7 | Link e immagini: estrazione evento con conferma | Un link con data/luogo produce una proposta strutturata — **raggiunto il 2026-11-26** (`v0.11.1`) |
| M7.1 | Link: lettura della pagina, JSON-LD, contesto non fidato, bozza di evento con conferma | Link con data/luogo → proposta senza scritture; link malevoli innocui — **raggiunto il 2026-11-26** (`v0.10.0`): link e pagina ostile verificati dal telefono; i siti anti-bot rispondono 403 e la via è la foto; dettagli in [[progetto/m7-1-link]] |
| M7.2 | Immagini: OCR gestito (Cloud Vision, `TEXT_DETECTION`, endpoint `eu`), bozza di evento con conferma | Una foto di un volantino produce una proposta; l'immagine non viene conservata — **raggiunto il 2026-11-26** (`v0.11.1`): foto, OCR e duplicati verificati dal telefono; dettagli in [[progetto/m7-2-foto]] |
| M8 | Menu: documenti su GCS, estrazione menu del nido, piano settimanale, lista della spesa | Piano `draft` → `confirmed`; modifica a linguaggio naturale — **in corso**: M8.1 nel codice |
| M8.1 | Documenti su GCS temporanei, menù del nido da PDF e screenshot, estrazione strutturata, file cancellato dopo la lettura | PDF e screenshot diventano `nursery_menus`; su GCS non resta nulla; un volantino non propone il salvataggio — **nel codice 2026-11-26**, dettagli in [[progetto/m8-1-documenti]] |
| M8.2 | Piano delle cene `draft`/`confirmed`, generazione da menù del nido e calendario, lista della spesa | «Preparami il menu» propone sette cene; il tap conferma; un secondo tap non riscrive |
| M8.3 | Modifica a linguaggio naturale, vista in dashboard, prova dal telefono | «Sposta il pesce a giovedì» cambia solo giovedì; la SPA mostra piano, menù del nido e spesa |
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
- **M4.5 — Routing locale** è `done` (2026-11-22, chiusa dall'operatore): shadow via MCP in `v0.5.0`, baseline zero-shot 68,3%; il gate di accuratezza e il fine-tuning restano nella fase shadow. Esito in [[progetto/m4-5-routing-locale]].
- **M4.6 — Eventi esistenti** è `done` (2026-11-21), `v0.6.0`: correzione senza duplicati e cancellazione idempotente. Esito in [[progetto/m4-6-eventi-esistenti]].
- **M4.7 — Eventi ricorrenti** è `done` (2026-11-22), `v0.7.3`: serie in calendario, più date con una sola conferma, verifica dal telefono superata. Esito in [[progetto/m4-7-eventi-ricorrenti]].
- **M5 — Dashboard in sola lettura** è `done` (2026-11-23), `v0.8.0`: API `/api/v1` con token Firebase e SPA su Firebase Hosting (`family-bot-dev.simonecastellani.com`), deploy indipendenti. Login verificato dal telefono; estraneo → 403 e log solo admin coperti dai test. Dettagli in [[progetto/m5-dashboard]].
- **M6 — Vocali con STT gestito** è `done` (2026-11-24), `v0.9.0`: la trascrizione diventa il testo del turno, l'audio non viene conservato e la prova dal telefono è passata. Esito in [[progetto/m6-vocali]].
- **M7.1 — Link** è `done` (2026-11-26), `v0.10.0` (revisione `family-bot-api-00040-goq`): la pagina viene letta una volta e scartata, il contenuto è non fidato e la proposta resta da confermare. La prova dal telefono ha confermato link e pagina ostile; i siti dietro protezione anti-bot (Ticketmaster) rispondono 403 a Cloud Run e per quegli eventi la via è la foto. Dettagli in [[progetto/m7-1-link]].
- **M7.2 — Foto con OCR** è `done` (2026-11-26), `v0.11.1` (revisione `family-bot-api-00044-yim`): la photo più grande (o l'immagine inviata come file) viene letta da Cloud Vision sull'endpoint `eu`, l'immagine è scartata e il testo è contesto non fidato. La prova dal telefono ha rilevato un evento duplicato, ora intercettato da `FindCalendarDuplicateService`, e la riprova è passata. Dettagli in [[progetto/m7-2-foto]].
- **M8.1 — Documenti e menù del nido** è nel codice (2026-11-26): PDF e screenshot diventano un menù per settimana, il file viene cancellato dopo l'estrazione. Mancano deploy, prova in cloud e tag `v0.12.0`. Dettagli in [[progetto/m8-1-documenti]].
- Restano **M8.2** (piano delle cene `draft`/`confirmed` + lista della spesa) e **M8.3** (modifica a linguaggio naturale + vista in dashboard).
- Il passaggio da una milestone alla successiva si misura con i **criteri di accettazione** della tabella, non con il numero di attività svolte.
- **M10** non è pianificabile a calendario: si affronta solo dopo settimane di uso reale, e la prima attività è la progettazione, non l'implementazione (§11).
- Le domande che possono bloccare o rimandare una milestone sono raccolte in [[progetto/questioni-aperte]].
- Le istruzioni con cui l'agente lavora e la definizione di done sono in [[sviluppo/convenzioni-agenti]].
- Le regole architetturali non negoziabili sono in [[architettura/regole]].
