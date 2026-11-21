---
title: M4.7 — Eventi ricorrenti
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
timestamp: 2026-11-21
---
# M4.7 — Eventi ricorrenti

Piano per **creare** un evento che si ripete e **cancellarlo**, una volta sola o per
tutta la serie. Nasce da un caso reale: «piscina con Matilde tutti i giovedì
18:15-18:30» — il bot ha risposto che il calendario non gestisce le ripetizioni e
ha chiesto una cosa a cui il turno dopo non poteva rispondere (le sessioni sono
stateless). Rilascio `v0.7.0`; M5 slitta a `v0.8.0`. Il lavoro precedente è in
[[progetto/m4-6-eventi-esistenti]]; i criteri generali in [[progetto/roadmap]].

## Obiettivo

Un impegno che si ripete («tutti i giovedì») diventa **una serie** in calendario,
con la solita conferma. Cancellarla è una scelta esplicita fra **una occorrenza** e
**tutta la serie**, fatta con il dito: due bottoni etichettati, nessuna domanda.

## Decisioni

| Tema | Decisione |
|---|---|
| Scope | Creare una serie; cancellare una occorrenza o tutta la serie; modificare una singola occorrenza |
| Ambiguità «questa o tutte» | Due proposte con bottoni etichettati («Solo questa» / «Tutte le volte») |
| Alternativa non scelta | Confermandone una, l'altra del turno viene chiusa |
| Orario di tutta la serie | Fuori: si sposta la prossima e si propone di cancellare e ricreare la serie |
| Coerenza della cache | Sync incrementale **best-effort** dopo ogni scrittura sul calendario |
| Frequenze ammesse | Sottoinsieme RFC 5545 condiviso con i task, esteso con `YEARLY` |
| Memoria della conversazione | Rinviata dopo M9: nuova **M10**, con la valutazione di Cartographer come memoria del sistema; Chores diventa **M11** |

## Design

### Porta, adapter e schemi

- `NewRemoteEvent.recurrence: tuple[str, ...]`: le righe RFC 5545; `payload_from_new` aggiunge `recurrence`.
- `CreateCalendarEventCommand.rrule: str | None`, validato con `validate_rule` (che ora accetta `YEARLY`).
- La `DELETE` non cambia: cancella l'id che riceve; per una serie è il **master**.

### Creazione di una serie

- Con `rrule`, il servizio **non cacha il master**: il sync espande le serie (`singleEvents=true`) e non restituisce il master, quindi in cache resterebbe una riga fantasma e la lista mostrerebbe un duplicato.
- Dopo la scrittura, **sync incrementale best-effort** in try/except: la serie compare subito e la cache resta coerente. Se il sync fallisce si logga e il messaggio resta «fatto»; il job da 15 minuti recupera.

### Cancellazione

- `DeleteCalendarEventCommand` cresce `scope: "one" | "all"`; `event_id` resta quello dell'**occorrenza** che il modello ha letto. Con `scope="all"` il servizio usa `recurring_event_id` come master (se la riga è già il master o è un evento singolo, usa il suo id), senza leggere nulla da Google.

### Bottoni

- `ProposedAction`/`ProposedActionDraft` crescono `label` (≤ 30 caratteri): la tastiera è una riga di conferme («✅ Solo questa», «✅ Tutte le volte») più una riga con un solo «✖️ Annulla» che chiude **tutte** le proposte pending del turno.
- Editando il messaggio con tastiera vuota Telegram toglie i bottoni: dopo un tap il fratello sparisce, niente doppio tap.

### Tool e prompt

- `create_event(..., rrule?)`; `delete_event(event_id)` apre due proposte solo quando la riga ha `recurring_event_id`.
- `list_calendar_events` e `get_week_overview` marcano le occorrenze di una serie con `(serie)`, senza aggiungere id.
- Prompt: un impegno che si ripete va in calendario con `rrule`, non nei task; niente domande secche (finché non c'è la memoria si chiede di riscrivere la richiesta completa).

## Criteri di accettazione

1. «Piscina con Matilde tutti i giovedì 18:15-19:30» → una proposta; sul telefono la serie si ripete; nessuna riga fantasma in cache.
2. «Cosa ho giovedì?» mostra la piscina **una volta**; nessun duplicato dopo il sync.
3. «Togli la piscina di giovedì» → due bottoni; «Solo questa» toglie una data, «Tutte le volte» toglie la serie.
4. Una serie creata da altri si cancella allo stesso modo.
5. Modifica di una singola occorrenza invariata rispetto a M4.6.
6. Suite, lint e pyright verdi; deploy `v0.7.0`; verifica reale dal telefono.

## Test

- `validate_rule` accetta `YEARLY` e rifiuta il resto.
- Payload `recurrence` con `httpx.MockTransport`.
- Creazione: nessun master in cache, sync invocato; un sync che fallisce non fa fallire il tap.
- Delete `one`/`all` con l'id giusto.
- Tool: una o due proposte secondo `recurring_event_id`; etichetta sul bottone; conferma che chiude l'alternativa; rifiuto che chiude tutte.
- Lista con `(serie)`.

## Difetti trovati strada facendo

- **Gli id delle azioni colludevano**: `new_id()[:10]` è il timestamp di una UUIDv7, quindi due proposte dello stesso turno avevano lo stesso id e il bottone di una eseguiva l'altra. Ora si usa la coda casuale (`_action_id`), con un test.
- **`BYMONTH` non era nel sottoinsieme**: una regola annuale scritta come `FREQ=YEARLY;BYMONTH=3;BYMONTHDAY=12` veniva rifiutata. Aggiunto e validato.
- `close_alternatives` non deve far fallire un tap già riuscito: correzione in `v0.7.1`.

## Esito

- 292 test verdi, ruff e pyright puliti; CI verde su `main`.
- Deploy `v0.7.1` (dopo `v0.7.0`, che non conteneva la correzione della chiusura best-effort): canary e smoke test verdi, traffico al 100% sulla revisione `family-bot-api-00030-peh`, `/livez` riporta `0.7.1`.
- Verifica reale dal telefono ancora da fare (vedi Handoff).

## Ordine dei lavori

| # | Cosa | Esito |
|---|---|---|
| W1 | `YEARLY` nel validatore, `rrule` negli schemi, `recurrence` nella porta | test di validazione e di payload |
| W2 | Creazione serie, politica della cache, sync best-effort | la serie si crea, niente righe fantasma |
| W3 | `label` sui bottoni, chiusura delle alternative, `scope` nella delete | tastiera a due conferme, un Annulla |
| W4 | Tool e prompt | correzione e cancellazione senza domande |
| W5 | Deploy `v0.7.0` e verifica reale | criteri 1-6 in cloud |
| W6 | Primer, AGENTS, KB, roadmap | milestone chiusa |
