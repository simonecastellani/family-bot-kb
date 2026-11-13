---
status: active
timestamp: 2026-11-13
title: Task e occorrenze
type: Architecture
---
# Task e occorrenze

Modello concreto con cui M1 rappresenta i task e le loro scadenze. Estende le convenzioni di [[architettura/modello-dati]] e realizza i criteri di [[progetto/roadmap]] per M1.

## Task

`Task` è la **definizione**: titolo, descrizione, `assignee_id`, stato e una `schedule` discriminata.

### Schedule una tantum

`one_off` porta esattamente uno tra:

- `due_date` (data pura, tutto il giorno);
- `due_at` (istante UTC).

### Schedule ricorrente

`recurring` porta:

- `rrule` (stringa RFC 5545);
- `starts_on` (data locale di partenza);
- `due_time` opzionale: se assente l'occorrenza è tutto il giorno;
- `timezone`, per ora `Europe/Rome`.

Una regola mensile che cade in un mese senza quel giorno **salta il mese** (RFC 5545). L'ultimo giorno del mese si esprime con `BYMONTHDAY=-1`, non con una trasformazione custom.

## Occorrenze

`TaskOccurrence` è l'unità su cui si agisce: completamento, snooze e, in M3, promemoria.

- `_id` UUIDv7; l'unicità dello slot è garantita dall'indice unico `(task_id, occurrence_key)`, dove la chiave identifica la data (tutto il giorno) o la data-ora locale prevista. L'indice rende idempotente la materializzazione e evita hotspot da `_id` monotoni.
- `task_id` e `assignee_id` (denormalizzato per le query), `occurrence_key`.
- `due_date` oppure `due_at`, più `window_start` e `window_end`: la finestra per cui l'occorrenza è stata generata.
- `status`: `pending`, `completed`, `cancelled`.
- `snoozed_until`, `completed_at`, `completed_by`, campi comuni.

Ogni task materializza almeno un'occorrenza, anche quando è una tantum: così completamento e snooze hanno un solo percorso.

## Tempo e fuso

- L'espansione della regola avviene su datetime **naive in ora locale**; ogni occorrenza viene poi localizzata con `ZoneInfo("Europe/Rome")`.
- Ora locale inesistente (buco dell'ora legale): si sposta al primo istante valido successivo. Ora ambigua: `fold=0`.
- Le date si salvano in UTC; `due_date` resta una data pura e non viene convertita.
- La finestra di materializzazione è di **60 giorni** (`materialization_horizon_days`, default 60), in linea con la materializzazione periodica descritta in [[architettura/promemoria]].

## Materializzazione e riconciliazione

- Creazione: si generano le occorrenze della finestra con upsert su `(task_id, occurrence_key)`; il servizio di creazione materializza subito la prima finestra.
- Riesecuzione: l'upsert non duplica nulla.
- Cambio di schedule: si riconcilia la finestra futura; le occorrenze pending non più previste diventano `cancelled`, quelle completate non si toccano.

## Transizioni

- `pending → completed`: imposta `completed_at` e `completed_by`; ripetere il completamento della stessa occorrenza è un no-op.
- `pending → cancelled`: usata dalla riconciliazione e dall'archiviazione del task.
- Snooze: lo stato resta `pending`; `snoozed_until` sposta la scadenza effettiva. Per le occorrenze tutto il giorno vale la data locale di `snoozed_until`.
- Un'occorrenza completata non si rimanda e non si annulla.

## Concorrenza

Ogni documento ha `version`. Le modifiche usano il filtro `{_id, version}` con `$inc: {version: 1}`; se `matched_count == 0` il servizio restituisce `VersionConflict`. Il chiamante rilegge e riprova.

## Audit e transazioni

- Ogni comando scrive un `audit_events` con actor, source, azione, entità e diff.
- Completamento, snooze, aggiornamento e archiviazione scrivono documento e audit nella **stessa transazione**.
- La creazione scrive task e audit in transazione, poi materializza: la materializzazione è idempotente, quindi un retry è innocuo.

## Sottoinsieme MongoDB

Compatibile con Firestore Enterprise:

- CRUD, filtri semplici, `$set`, `$inc`, `$in`, ordinamenti espliciti;
- `createIndex` idempotente in `ensure_indexes()`, migrazioni con `schema_migrations`;
- pulizia dei test: in locale si elimina il database usa-e-getta; sul database Firestore reale si usa `delete_many`, perché i permessi dati non coprono lo schema (M2);
- niente ordinamento su `_id`: Firestore non lo indicizza di default;
- connessione con `retryWrites=false`.

## Indici

- `task_occurrences`: `(task_id, occurrence_key)` **unico** (copre anche le query per `task_id`), `(status, due_at)`, `(status, due_date)`, `(assignee_id, status, due_at)`.
- `tasks`: `(status, assignee_id)`.
- `audit_events`: `(entity_type, entity_id, created_at)` e `(created_at)` decrescente.

Sono pochi e mirati: ogni indice in più costa scritture nel free tier.
