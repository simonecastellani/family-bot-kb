---
timestamp: 2026-11-18
title: M4 — Google Calendar
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
---
# M4 — Google Calendar

Piano e avanzamento della milestone che porta il calendario dentro il bot. Criteri di accettazione in [[progetto/roadmap]]; il lavoro precedente è in [[progetto/m3-promemoria-event-driven]].

## Obiettivo

Il bot legge il calendario della famiglia e risponde a «cosa abbiamo questa settimana?» unendo eventi e scadenze; «aggiungi in calendario la visita venerdì alle 16» produce una **proposta** che il membro conferma con un bottone e che diventa un evento reale su Google Calendar, con audit e `agent_runs`.

## Decisioni

| Tema | Scelta |
|---|---|
| Agenda | una sola agenda condivisa dedicata («Famiglia»); il calendario personale dell'account OAuth non viene mai toccato |
| Autenticazione | ADC del service account di runtime, scoped a `calendar.events`; l'agenda è condivisa con i due SA (*Apporta modifiche agli eventi*). Nessuna app OAuth, nessun refresh token, nessuna chiave JSON |
| Dipendenze | nessuna nuova: token exchange e due chiamate REST (`events.list`, `events.insert`) con `httpx` |
| Cache | `calendar_events` è uno specchio, non una fonte di verità; i cancellati si eliminano e un full resync settimanale limita il drift fuori dalla finestra |
| Stato del sync | collection `calendar_sync_state` (`_id = calendar_id`), token salvato solo a fine giro |
| Fine dei tutto-il-giorno | inclusiva nel dominio, esclusiva nell'API Google: conversione solo al confine |
| Creazione | id deterministico da `(run_id, action_id)` in base32hex; un 409 è trattato come successo |
| Inviti | nessuno: `sendUpdates=none`; destinatari solo in cache per gli eventi creati altrove |
| Sync | ogni 15 minuti via Scheduler: è il terzo e ultimo job gratuito |
| Prima sync | senza `timeMin`/`timeMax` né `orderBy`: Google restituisce `nextSyncToken` solo a una listing non ristretta. La finestra [-7, +90] si applica alla cache dopo il fetch |

## Architettura

- Porta `CalendarGateway` con adapter `GoogleCalendarGateway` (REST + token dal service account via ADC) e `FakeCalendarGateway` per i test. Dettagli in [[architettura/porte-e-fake]].
- Servizi: `SyncCalendarService` (worker, endpoint `/internal/calendar/sync`), `CreateCalendarEventService` (conferma dell'agente), `ListCalendarEventsService` e `GetWeekOverviewService` (letture).
- L'agente guadagna i tool `create_event`, `list_calendar_events`, `get_week_overview`; la scrittura resta dietro la conferma inline già usata per i task.
- Modello dati e indici in [[architettura/modello-dati]]; risorse GCP, secret e job in [[infrastruttura/inventario]] e `docs/gcp-setup.md` fase E2.

## Stato dei workstream

| # | Cosa | Stato |
|---|---|---|
| W0 | API Calendar, agenda «Famiglia», condivisione con i due SA | fatto il 2026-11-19: `events.list` 200, `insert` 200, `DELETE` 204 con token impersonato |
| W1 | Dominio, repository, contract test, indici, adapter memoria | fatto (9 contract test su MongoDB reale) |
| W2 | Porta, token provider, adapter Google, fake | fatto (test con `httpx.MockTransport`) |
| W3 | Sync incrementale, 410 → full resync, job Scheduler | fatto |
| W4 | Creazione evento con conferma e id deterministico | fatto |
| W5 | Query e tool di lettura, panoramica settimanale | fatto |
| W6 | Secret, env, scheduler, deploy `v0.4.0` | `blocked` da W0 |
| W7 | Retention cache, documentazione, chiusura | in corso |

## Cosa resta

1. Tag `v0.4.0` e deploy: il workflow crea il terzo job Scheduler.
2. Verifica in cloud dei criteri: doppio sync innocuo, evento cancellato che sparisce, creazione dal telefono.

## Debito registrato

Il provider LLM resta OpenCode Go, che non ammette un bot famigliare: debito di produzione tracciato in [[progetto/questioni-aperte]], da chiudere in una milestone dedicata insieme a un classificatore locale che lasci al modello esterno solo i casi complessi.
