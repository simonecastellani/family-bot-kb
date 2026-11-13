---
status: active
title: Promemoria event-driven
type: Architecture
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
---
# Promemoria event-driven

I promemoria delle occorrenze non nascono da un job periodico ma da un flusso event-driven: l'`api` pianifica un task differito per il singolo orario di invio, il `worker` lo esegue e verifica sul dato corrente se l'invio ha ancora senso.

## Flusso

1. **Pianificazione.** Quando un'occorrenza viene creata o spostata, l'`api` pianifica un task differito in Cloud Tasks con l'orario di invio; il nome del task è deterministico e coincide con il `dedupe_key`.
2. **Chiamata del worker.** A scadenza, Cloud Tasks chiama il `worker` via HTTP con autenticazione OIDC.
3. **Rilettura dello stato.** Il worker rilegge lo stato attuale dell'occorrenza: se è completata, annullata o rimandata non invia nulla.
4. **Invio.** L'invio crea il documento `reminders` con `_id = dedupe_key`.
5. **Retry.** Cloud Tasks ritenta sugli errori; il worker deve tollerare i retry.
6. **Cosa resta allo scheduler.** Cloud Scheduler copre solo ciò che è periodico (vedi *Scheduler* sotto); spostare o cancellare un'occorrenza non richiede di cancellare il task accodato, perché il controllo di stato al momento dell'esecuzione lo rende innocuo. La cancellazione esplicita del task è solo un'ottimizzazione.

## Idempotenza

- La chiave di idempotenza è il `dedupe_key`: lo stesso nome deterministico identifica il task pianificato e il documento `reminders` che l'invio crea.
- Se al momento dell'invio il documento `reminders` esiste già, l'invio è già avvenuto: un retry o un task duplicato non produce un secondo promemoria.
- La rilettura dello stato prima dell'invio è l'altra metà della garanzia: un task che arriva su un'occorrenza completata, annullata o rimandata è innocuo anche se nessuno lo ha cancellato.
- [ ] Verificare il comportamento di dedupe di Cloud Tasks e cosa accade al riuso di un nome di task già usato.

## Scheduler

**Cloud Scheduler** interviene solo sul lavoro periodico, mai sul singolo promemoria:

- digest mattutino;
- sync del calendario;
- materializzazione delle ricorrenze su un orizzonte rolling (default 60 giorni);
- pulizia e retention.

La separazione è deliberata: gli orari puntuali vivono in Cloud Tasks e sono ricontrollati al momento dell'invio, mentre lo scheduler non deve conoscere le singole occorrenze.
