---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
timestamp: 2026-11-18
title: M3 — Promemoria event-driven
type: Pagina
---
# M3 — Promemoria event-driven

Piano e avanzamento della milestone M3: portare i promemoria fuori dall'applicazione, su Cloud Tasks, con un worker privato e i job periodici su Cloud Scheduler. Criteri in [[progetto/roadmap]]; il lavoro precedente in [[progetto/m2-deploy-telegram-agente]].

## Obiettivo

Un'occorrenza che scade genera **un** messaggio Telegram, anche se il worker viene invocato più volte; un'occorrenza rimandata non produce invii al vecchio orario; un'occorrenza completata non produce nulla.

## Architettura

`api` pianifica su Cloud Tasks quando un'occorrenza nasce o viene rimandata; Cloud Tasks chiama il `worker` a scadenza con OIDC; il worker rilegge lo stato e decide. Scheduler copre solo il periodico (digest la mattina, materializzazione e retention la notte).

Due serrature diverse, entrambe necessarie:

- **nome del task Cloud Tasks** = hash deterministico di `(occorrenza, istante)`: impedisce di pianificare due volte lo stesso promemoria. I nomi dei task eseguiti o cancellati restano prenotati fino a 24 ore, quindi il nome non è mai riciclato per un promemoria diverso; un `ALREADY_EXISTS` è un successo, non un errore. Il nome è hashato perché la documentazione avverte che i prefissi sequenziali (timestamp) aumentano latenza ed errori.
- **`reminders._id`** = la stessa chiave: impedisce il doppio *invio*, perché Cloud Tasks può consegnare lo stesso task più volte. Si invia e poi si scrive il documento (primer §8): al massimo un invio, al più qualche tentativo.

## Decisioni

| Tema | Scelta |
|---|---|
| Istante del promemoria | tutto il giorno: 09:00 locali; con orario: 30 minuti prima; mai nel passato (se è già trascorso non si accoda nulla) |
| Bottoni | ✅ Fatto e 😴 Domani sul messaggio, callback `rm:` accanto a `cf:`/`cx:` |
| Endpoint interni | `/internal/reminders`, `/internal/digest`, `/internal/daily`; autenticazione OIDC con audience = URL del worker e email in whitelist |
| Una immagine, due ruoli | `SERVICE_ROLE=api|worker`: il worker non monta il webhook, l'API non monta gli endpoint interni |
| Scheduler | due job (04:00 manutenzione, 07:00 digest) perché i job gratuiti sono tre e uno serve a M4; orari in `deploy/scheduler.yaml`, applicati dal workflow |
| Configurazione | istanti e bottoni in `Settings` (env, condivisi da api e worker); gli orari Scheduler sono infrastruttura, non configurazione dell'app |
| Contract su Firestore | isolamento con prefisso di collection, perché il free tier espone un solo database e la suite cancella documenti |

## Workstream

| # | Cosa | Stato |
|---|---|---|
| W1 | Dominio `Reminder`, repository, contract test, indici | in corso |
| W2 | `PlanRemindersService` + adapter Cloud Tasks REST + fake | |
| W3 | Worker, endpoint interni, verifica OIDC, servizi di invio/digest/manutenzione | |
| W4 | Infrastruttura: coda, worker, invoker sul servizio, job Scheduler | |
| W5 | Bottoni `rm:` | |
| W6 | Contract test sul Firestore reale | |
| W7 | Documentazione e rilascio `v0.3.0` | |
