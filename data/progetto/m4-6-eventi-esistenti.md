---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: open
timestamp: 2026-11-21
title: M4.6 — Eventi esistenti
type: Pagina
---
# M4.6 — Eventi esistenti

Piano per **modificare e cancellare** eventi già in calendario. Nasce da un caso reale: il 2026-11-19 la correzione dell'orario del «Cambio gomme» ha creato un secondo evento, perché M4 sapeva solo creare; il vecchio è stato cancellato a mano. Stato `open`: non iniziato. Rilascio `v0.6.0`; di conseguenza M5 slitta a `v0.7.0`. Il lavoro precedente è in [[progetto/m4-google-calendar]] e [[progetto/m4-5-routing-locale]]; i criteri generali in [[progetto/roadmap]].

## Obiettivo

Una correzione («il cambio gomme è alle 9:30») produce una **proposta di modifica** dell'evento esistente, con il solito bottone. Una cancellazione produce una proposta di cancellazione. Mai un duplicato.

## Dentro

- Porta calendario: `update_event` e `delete_event`.
- Servizi con audit e cache coerente.
- Tool agente, executor e conferma.
- Id dell'evento visibile in `list_calendar_events`.
- Prompt: una correzione usa `update_event`, mai una seconda creazione.

## Fuori

- Modifica di un'intera serie ricorrente: si agisce sulla singola istanza per id (`recurringEventId` resta informativo).
- Inviti ai partecipanti (richiedono domain-wide delegation; il bot non invita).
- Undo e editing dalla dashboard.

## Design

### Porta e adapter Google

- `update_event(calendar_id, event_id, event: NewRemoteEvent, etag: str | None) -> RemoteEvent | None`: `PUT events.update` con il corpo completo (riusa `payload_from_new`), `If-Match: etag` quando l'evento è in cache; `412` = conflitto concorrente, `404/410` = evento sparito (None).
- `delete_event(calendar_id, event_id) -> bool`: `DELETE`; `204` = cancellato, `404/410` = già cancellato (successo idempotente). Sempre `sendUpdates=none`.
- Il fine esclusivo dei tutto-il-giorno resta convertito al confine, come per la creazione.

### Servizi

- `UpdateCalendarEventService`: comando con `event_id` e i campi modificabili; carica l'evento dalla cache (404 se assente), chiama Google, aggiorna cache e audit `CALENDAR_EVENT_UPDATED` (diff con `agent_run_id`).
- `DeleteCalendarEventService`: chiama Google, elimina dalla cache, audit `CALENDAR_EVENT_DELETED`; ripetibile senza errore.
- Nessuna nuova collection né indice.

### Agente e conferma

- Nuovi `AgentActionKind`: `CALENDAR_EVENT_UPDATE`, `CALENDAR_EVENT_DELETE`.
- Tool `update_event(event_id, title?, start?, end?, all_day?, location?, description?)` e `delete_event(event_id)`: preparano solo proposte.
- `list_calendar_events` aggiunge `— id: <id>` a ogni riga; senza id il modello non può proporre una modifica.
- La conferma usa il claim esistente: doppio tap innocuo, scadenza a 24 ore.
- Prompt: «Se il membro corregge un evento esistente, usa `update_event`; non crearne un altro».

## Criteri di accettazione

1. «Il cambio gomme di giovedì 26 è alle 9:30» → proposta di modifica; sul calendario resta **un solo** evento.
2. Cancellazione idempotente: un retry o un secondo tap non produce errori visibili.
3. Evento inesistente o `etag` cambiato → messaggio chiaro, nessuna scrittura parziale.
4. Suite, lint e pyright verdi; deploy `v0.6.0`; verifica reale sul calendario «Famiglia».

## Test

- Gateway con `httpx.MockTransport`: PUT con `If-Match`, `412`, `404`, DELETE `204`/`404`.
- Servizi: modifica e cancellazione, audit, cache; idempotenza della delete.
- Conferma: executor, doppio tap, payload non più valido.
- Tool: `list_calendar_events` espone l'id; una correzione produce `CALENDAR_EVENT_UPDATE`.
- Verifica in cloud con un evento di prova, poi cancellazione.

## Ordine dei lavori

| # | Cosa | Esito |
|---|---|---|
| W1 | Porta e adapter Google (`update`, `delete`, If-Match) | test con MockTransport |
| W2 | Servizi, audit, cache | test di servizio |
| W3 | Action kind, executor, conferma | modifica/cancellazione dal bottone |
| W4 | Tool agente, id in lista, prompt | correzione senza duplicati |
| W5 | Deploy `v0.6.0`, M5 → `v0.7.0` | criterio in cloud |
| W6 | Documentazione, KB, verifica reale | milestone chiusa |

## Handoff

- Stato al 2026-11-21: M4 e M4.5a chiusi e in produzione (`v0.5.2`), shadow routing attivo; M4.6 non iniziato.
- Il duplicato del 19 novembre è già stato ripulito a mano: nessuna migrazione dati.
- Prima azione in una nuova sessione: build mode, W1 dal gateway.
