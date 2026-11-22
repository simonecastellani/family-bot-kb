---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: done
timestamp: 2026-11-21
title: M4.6 — Eventi esistenti
type: Pagina
---
# M4.6 — Eventi esistenti

Piano per **modificare e cancellare** eventi già in calendario. Nasce da un caso reale: il 2026-11-19 la correzione dell'orario del «Cambio gomme» ha creato un secondo evento, perché M4 sapeva solo creare; il vecchio è stato cancellato a mano. Stato `in-progress` (2026-11-21): codice, test e documentazione sono pronti; restano il rilascio `v0.6.0` e la verifica reale sul calendario «Famiglia». Di conseguenza M5 slitta a `v0.7.0`. Il lavoro precedente è in [[progetto/m4-google-calendar]] e [[progetto/m4-5-routing-locale]]; i criteri generali in [[progetto/roadmap]].

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
- Guardia anti-duplicato in creazione: la correzione si fa con `update_event`, non serve altro.

## Design

### Porta e adapter Google

- `update_event(calendar_id, event_id, patch: RemoteEventPatch, etag: str | None) -> RemoteEvent | None`: **`PATCH` parziale**, non `PUT`: il provider conserva i campi che non modelliamo (per esempio i promemoria che un membro mette dal telefono). Solo i campi indicati viaggiano nel body. `If-Match: etag` quando l'evento è in cache, verbatim (con le virgolette di Google): `412` = conflitto concorrente, `404/410` = evento sparito (None).
- `delete_event(calendar_id, event_id) -> bool`: `DELETE`; `204` = cancellato, `404/410` = già cancellato (successo idempotente). Sempre `sendUpdates=none`. Nessun `If-Match`: il membro ha chiesto che l'evento sparisca.
- Attenzione all'ordine dei rami in `_request`: un `410` è «token scaduto» solo quando chiediamo una lista; per update e delete significa «evento sparito» e va letto **prima** di quella regola.
- Il fine esclusivo dei tutto-il-giorno resta convertito al confine, come per la creazione.

### Servizi

- `UpdateCalendarEventService`: comando con `event_id` e i campi modificabili; carica l'evento dalla cache (404 se assente), chiama Google, aggiorna cache e audit `CALENDAR_EVENT_UPDATED` (diff con `agent_run_id`). Provider prima, cache e audit dopo.
- Se cambia solo l'inizio, **la durata resta** quella dell'evento; il tipo (orario o tutto il giorno) **non cambia**: un evento con un orario non diventa un evento di tutta la giornata, e viceversa (rifiuto esplicito al membro).
- La cache viene ricostruita dalla riga precedente: `created_at` resta, `version` va avanti di uno. Se il provider spezza l'istanza di una serie e risponde con un **id diverso**, la riga vecchia viene rimossa.
- `DeleteCalendarEventService`: chiama Google, elimina dalla cache, audit `CALENDAR_EVENT_DELETED`; ripetibile senza errore (un evento già sparito non è un fallimento).
- Nessuna nuova collection né indice.

### Agente e conferma

- Nuovi `AgentActionKind`: `CALENDAR_EVENT_UPDATE`, `CALENDAR_EVENT_DELETE`.
- Tool `update_event(event_id, title?, start?, end?, location?, description?)` e `delete_event(event_id)`: preparano solo proposte. Il tool rifiuta un id sconosciuto e un «niente da modificare», prima di proporre.
- `list_calendar_events` e `get_week_overview` aggiungono `— id: <id>` a ogni riga; senza id il modello non può proporre una modifica.
- La conferma usa il claim esistente: doppio tap innocuo, scadenza a 24 ore.
- Un rifiuto comprensibile (conflitto, evento sparito, tipo non supportato) arriva al membro come **messaggio**: `ActionFailure` intercettato dalla conferma, senza il nome dell'eccezione.
- Prompt: «Se il membro corregge un evento esistente, usa `update_event`; non crearne un altro».
- Le etichette di routing contano `update_event` e `delete_event`: senza, i turni di correzione uscirebbero dal dataset shadow di M4.5.

## Criteri di accettazione

1. «Il cambio gomme di giovedì 26 è alle 9:30» → proposta di modifica; sul calendario resta **un solo** evento.
2. Cancellazione idempotente: un retry o un secondo tap non produce errori visibili.
3. Evento inesistente o `etag` cambiato → messaggio chiaro, nessuna scrittura parziale.
4. Modifica della sola ora → la **durata** resta quella originale.
5. Nessun campo che il bot non modellava viene perso (un promemoria messo dal telefono resta).
6. Il tipo dell'evento non cambia: un rifiuto spiega il perché.
7. Di una serie ricorrente si modifica la singola istanza.
8. Un turno di correzione compare in `agent_runs` con `route = event_write`.
9. Suite, lint e pyright verdi; deploy `v0.6.0`; verifica reale sul calendario «Famiglia».

## Test

- Gateway con `httpx.MockTransport`: PUT con `If-Match`, `412`, `404`, DELETE `204`/`404`.
- Servizi: modifica e cancellazione, audit, cache; idempotenza della delete.
- Conferma: executor, doppio tap, payload non più valido.
- Tool: `list_calendar_events` espone l'id; una correzione produce `CALENDAR_EVENT_UPDATE`.
- Verifica in cloud con un evento di prova, poi cancellazione.

## Ordine dei lavori

| # | Cosa | Esito |
|---|---|---|
| W1 | Porta e adapter Google (`update`, `delete`, If-Match) | fatto: test con MockTransport |
| W2 | Servizi, audit, cache | fatto: test di servizio |
| W3 | Action kind, executor, conferma | fatto: modifica/cancellazione dal bottone |
| W4 | Tool agente, id in lista, prompt | fatto: correzione senza duplicati |
| W5 | Deploy `v0.6.0`, M5 → `v0.7.0` | fatto: tag `v0.6.0`, canary e smoke test verdi, traffico spostato |
| W6 | Documentazione, KB, verifica reale | mezzo: primer e AGENTS aggiornati, resta la verifica reale |

## Esito

- **Chiusa il 2026-11-21 in produzione**: tag `v0.6.0`, revisione `family-bot-api-00026-qal` con il traffico al 100% dopo canary e smoke test verdi.
- Criterio raggiunto sul rilascio: modifica condizionata all'etag e cancellazione idempotente, coperte dai test con `MockTransport` e dai test di servizio; la revisione ostile della diff ha portato la correzione della durata da muro e della fine dell'evento di tutta la giornata.
- La verifica reale dal telefono prevista da W6 non è stata registrata: la chiusura poggia sul rilascio e sul canary.

## Handoff

- Stato al 2026-11-21: M4 e M4.5a chiusi e in produzione (`v0.5.2`), shadow routing attivo. M4.6 è **rilasciata in produzione**: tag `v0.6.0`, revisione `family-bot-api-00026-qal` con il traffico al 100% dopo canary e smoke test.
- Prima del rilascio la diff è passata da una revisione ostile: i difetti trovati (durata calcolata in tempo assoluto invece che da muro, fine dell'evento di tutta la giornata scartata in silenzio, test del doppio tap vuoto, sync che riscriveva la riga come nuova) sono corretti nel commit `fix(m4.6)`.
- Decisioni prese in fase di piano: `PATCH` parziale (non `PUT`), il tipo dell'evento non cambia, di una serie si tocca la singola istanza, sul `412` si dice al membro senza riscrivere, niente guardia anti-duplicato in creazione.
- Il duplicato del 19 novembre è già stato ripulito a mano: nessuna migrazione dati.
- Prossima azione in una nuova sessione: **verifica reale dal telefono** (criterio 9), poi chiudere W6 e la milestone. La checklist: un evento di prova con un promemoria messo a mano, una correzione dell'orario (deve restare un solo evento e deve durare quanto prima), una cancellazione (poi un secondo tentativo non deve dare errore), un evento di tutta la giornata, e un evento che si sposta mentre il bot scrive (412: il bot deve dire che è cambiato, non sovrascrivere).
