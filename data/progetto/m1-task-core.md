---
status: open
timestamp: 2026-11-13
title: M1 — Task core
type: Pagina
---
# M1 — Task core

Lavoro accettato e **non ancora iniziato**: modello, repository con contract test, servizi e audit per task e occorrenze su MongoDB. I criteri di chiusura sono nella [[progetto/roadmap]]; il modello concreto e le sue regole stanno in [[architettura/task-e-occorrenze]].

## Obiettivo

Coprire gli use case 1 e 2 al livello dei dati: creare una definizione di task (una tantum o ricorrente), materializzarne le occorrenze in una finestra, completarle o rimandarle, con audit e concorrenza ottimistica.

## Dentro

- dominio `Task`, `TaskOccurrence`, `AuditEvent` e regole pure di ricorrenza;
- porte dei repository e adapter MongoDB, con `ensure_indexes()` e migrazioni;
- servizi di comando e query con `Actor`, DTO e audit;
- contract test dei repository su **MongoDB locale** e integration test dei servizi.

## Fuori

- interfacce HTTP, webhook Telegram e agente: arrivano in M2 e M5;
- invio dei promemoria, Cloud Tasks e Scheduler: M3;
- contract test sul Firestore Enterprise reale: rinviati a M2 (vedi [[progetto/questioni-aperte]]);
- nuove risorse GCP: M1 non ne crea.

## Decisioni

| Tema | Scelta |
|---|---|
| Superficie | solo dominio, repository e servizi; nessuna interfaccia HTTP |
| Mensile senza giorno (31, 29 febbraio) | RFC 5545: il mese viene saltato; l'ultimo giorno si esprime con `BYMONTHDAY=-1` |
| Contract test Firestore | solo MongoDB locale in M1; la suite resta parametrizzata sull'URI |
| Audit | transazione mutazione + `audit_events` dove l'invariante conta |
| ID | UUIDv7 tramite `uuid6` |

Il dettaglio tecnico di ciascuna scelta è in [[architettura/task-e-occorrenze]].

## Criteri di accettazione

1. Ricorrenza mensile con finestra di 60 giorni, con conversione corretta in UTC attraverso il cambio di ora legale.
2. Mensile il 31: febbraio saltato; `BYMONTHDAY=-1` produce l'ultimo giorno.
3. Completamento di un'occorrenza: stato, `completed_by` e audit nella stessa transazione.
4. Snooze: l'occorrenza resta `pending`, `snoozed_until` sposta la scadenza effettiva; un'occorrenza completata non si può rimandare.
5. Concorrenza ottimistica: due aggiornamenti con la stessa `version`, uno vince e l'altro riceve `VersionConflict`.
6. Materializzazione idempotente: eseguita due volte produce le stesse occorrenze.
7. Archiviazione del task: le occorrenze future diventano `cancelled`, quelle completate restano intatte.

## Note

- La verifica è affidata a test unit, contract e integration: M1 non espone interfacce.
- La CI esegue la suite contract contro un MongoDB 8.0 reale; senza database i test falliscono invece di essere saltati in silenzio.
- Come eseguire la suite è descritto in [[sviluppo/locale-e-test]].
