---
timestamp: 2026-11-18
title: M3 — Promemoria event-driven
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: done
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


## Esito (2026-11-18)

Milestone rilasciata come **v0.3.0** con la pipeline completa (8 job verdi): guardia, test, immagine, migrazioni, **worker e job Scheduler**, canary, smoke, traffico. In produzione: `/livez` riporta `0.3.0`, il worker è Ready, i due job Scheduler sono ENABLED, la coda `reminders` contiene il promemoria del **5 dicembre** (il primo reale).

### Consegnato

- `reminders` con `_id` = chiave di dedupe e contract test; retention nei due adapter;
- `PlanRemindersService` (09:00 per le occorrenze tutto il giorno, 30 minuti prima per quelle con orario, snooze come override, nome del task hashato), agganciato a creazione, snooze e manutenzione notturna;
- adapter Cloud Tasks REST (`google-auth` + `httpx`), `409` trattato come successo;
- worker privato con `SERVICE_ROLE`, `/internal/{reminders,digest,daily}` e token OIDC verificato contro un'allowlist di service account;
- bottoni ✅ Fatto / 😴 Domani con prefisso `rm:`;
- due job Scheduler applicati da `deploy/scheduler.json`, orari versionati;
- `run.invoker` stretto al solo worker: debito IAM di M2 chiuso.

### Ostacoli trovati solo in cloud

- Cloud Tasks **rifiuta `schedule_time` oltre 720 ore**: con la finestra di 60 giorni il job notturno falliva sul secondo promemoria. Il pianificatore accoda entro 29 giorni (`REMINDER_MAX_LEAD_DAYS`) e il job notturno recupera il resto.
- Firestore con compatibilità MongoDB **non supporta `$elemMatch`**: la scadenza delle azioni pendenti filtra in Python sulle run più recenti.
- Il worker non ha la chiave LLM, quindi il container non deve costruire il motore dell'agente: `SERVICE_ROLE=worker` usa un motore disabilitato.
- Il worker accoda anche lui (manutenzione notturna): la coda reale va costruita in entrambi i ruoli, non solo nell'API.
- `gcloud auth print-identity-token --audiences` non funziona con la federazione: lo smoke del worker verifica revisione Ready e immagine invece di chiamare un endpoint privato.
- `read` con IFS di default spezza la cron expression: il file degli orari si legge su tab.

### Criteri di accettazione

Coperti da test di integrazione: stesso task eseguito due volte → un solo invio; snooze prima della scadenza → nessun invio al vecchio orario; occorrenza completata → nessun invio; invio fallito → il retry invia una volta sola; promemoria oltre le 720 ore o nel passato → non accodato; digest solo ai membri con la preferenza attiva.

### Resta aperto

- **Contract test sul Firestore Enterprise reale** (debito da M1): il piano prevede isolamento con prefisso di collection `ct_` e autenticazione `PLAIN` con access token dal workflow notturno. Non implementato in questa sessione.
- Prova dal vivo del promemoria: il primo invio reale è il **5 dicembre**. Per accorciare, basta creare un task con scadenza domani.


## Contract sul Firestore reale (2026-11-18)

Decisione dell'operatore: i dati erano sacrificabili, quindi la suite è stata eseguita **direttamente sul database Enterprise**, senza prefisso di collection. **41 test contract verdi**, transazioni comprese: la compatibilità MongoDB copre ciò che serve al progetto (CRUD, filtri, indici, transazioni, concorrenza ottimistica).

Due dettagli utili:

- il guard è esplicito: `ALLOW_REMOTE_CONTRACT=distruggi-i-dati` più `TEST_MONGODB_DATABASE=family-bot`, altrimenti la suite rifiuta un URI non locale;
- **gli indici sopravvivono allo svuotamento** (Firestore elimina la collection quando sparisce l'ultimo documento, ma la definizione degli indici resta), quindi non serve rilanciare le migrazioni dopo un giro distruttivo.

Dopo il giro sono stati ricreati il membro (seed) e il task «Pagare il nido», e il job notturno ha ripianificato il promemoria del 5 dicembre; il task Cloud Tasks rimasto orfano è stato cancellato.

**Presidio periodico**: la suite resterà manuale finché non esiste il progetto di pre-produzione, dove diventerà notturna. Isolamento per progetto, non per prefisso.
