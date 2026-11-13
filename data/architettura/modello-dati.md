---
status: active
timestamp: 2026-11-13
title: Modello dati
type: Concept
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
---
# Modello dati

Il modello è documentale su **Firestore Enterprise** con compatibilità MongoDB; le regole del primer §6 valgono per ogni collection. La provenienza dei fatti è la Source [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]] (primer.md §6).

## Convenzioni

Dal primer §6, "Convenzioni":

- `_id` è una **stringa** (ULID/UUIDv7). Per `reminders` vale `_id = dedupe_key`: l'unicità la garantisce l'insert.
- Le date sono BSON Date in **UTC**; `Europe/Rome` compare solo in presentazione e per le ricorrenze. Si distingue `due_date` da `due_at`.
- Campi comuni a tutti i documenti: `created_at`, `updated_at`, `deleted_at` (soft delete), `source`, `version`.
- **Concorrenza ottimistica**: `update_one({_id, version: v}, {$set: ..., $inc: {version: 1}})`; se `matched_count == 0` c'è conflitto.
- Nomi di campo senza il pattern `__x__`; ID entro i tipi supportati.
- Transazioni multi-documento solo dove serve un'invariante (es. completare un'occorrenza + audit), brevi, dopo aver verificato il supporto.
- Gli **indici** sono dichiarati nel codice e applicati da `ensure_indexes()`, idempotente al deploy.
- Le **migrazioni di dati e indici** sono script versionati e idempotenti, con collection `schema_migrations`.

Limite di dimensione: la documentazione della compatibilità MongoDB indica **16 MiB** per documento; il progetto adotta **<4 MiB** come margine prudenziale. Non è una discrepanza aperta: è una regola più stretta del limite tecnico (verificato il 2026-11-13).

## Collection

Dal primer §6, "Collection":

| Collection | Contenuto | Note |
|---|---|---|
| `members` | email, `telegram_user_id`, ruolo, timezone, preferenze notifica | whitelist Telegram e mapping OIDC |
| `tasks` | definizione: titolo, assegnatario, `rrule` opzionale | |
| `task_occurrences` | `due_date`/`due_at`, finestra, `status`, `snoozed_until`, completamento | indice su (`status`, `due_*`) |
| `reminders` | `_id = dedupe_key`, destinatario, `scheduled_at`, `sent_at`, `status` | idempotenza invii |
| `calendar_events` | cache da Google: `google_event_id`, `etag`, `start/end`, partecipanti | sync con syncToken |
| `documents` | file originale (riferimento GCS) + estratto strutturato (menu del nido) | |
| `meal_plans` | settimana, stato `draft`/`confirmed`, **item incorporati** | |
| `agent_runs` | input, tipo, modello, token, esito, **step incorporati** (con cap) | visibile solo ad admin |
| `audit_events` | actor, source, azione, entità, diff | |

L'use case 4 (chores e carico) non viene modellato in questa fase.

## Vincoli del free tier

Dal primer §6, "Vincoli del free tier (Firestore Enterprise, da verificare periodicamente)":

- 1 GiB di storage; **50.000 read unit e 40.000 write unit al giorno**; 10 GiB/mese di egress; il free tier vale per **un solo database per progetto**. Le unit sono a scaglioni (4 KB in lettura, 1 KB in scrittura) e le query indicizzate consumano anche letture di indice.
- **TTL gestito, backup e restore non hanno uso gratuito** e richiedono billing: non farci affidamento per retention e dedupe; la retention si fa con un job dello scheduler.
- Niente polling ravvicinato sui dati: usare il design event-driven (§8 del primer).
