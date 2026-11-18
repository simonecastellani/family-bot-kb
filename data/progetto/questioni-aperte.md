---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-18
title: Questioni aperte
type: Pagina
---
# Questioni aperte

Domande non ancora risolte, con la condizione o il momento in cui vanno chiuse (§14). Molte si chiudono dentro una milestone precisa: vedi [[progetto/roadmap]]. Fonte: [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].

## Aperte

- **Copertura reale della compatibilità MongoDB di Firestore** — **verificata il 2026-11-18**: la suite contract (41 test) è passata sul database Enterprise reale, transazioni comprese. Resta aperto solo il presidio periodico: la suite è distruttiva, quindi non diventa notturna finché non esiste il progetto di pre-produzione dove spostarla.
- **Regioni** — disponibilità dell'edizione Enterprise e applicabilità delle quote gratuite in regioni UE, vicine a Cloud Run.
- **Strategia di backup/restore** — da definire, dato che non ha uso gratuito.
- **Modelli con vision/audio** — quali modelli di OpenCode Go li supportano; STT gestito oppure audio nativo del modello.

## Chiuse di recente

- **Compatibilità MongoDB di Firestore** — chiusa il 2026-11-18 con la suite contract eseguita sul database vero; il presidio periodico vivrà nella pre-produzione.

- **Autenticazione dal servizio Cloud Run all'endpoint MongoDB di Firestore** — chiusa il 2026-11-17: identità del service account, nessun segreto statico, verificata con una richiesta reale.

- **Autenticazione Cloud Run → Firestore** — **risolta** il 2026-11-17: `MONGODB-OIDC` con `ENVIRONMENT:gcp` funziona dal runtime, verificata end-to-end (vedi [[progetto/m2-deploy-telegram-agente]]).
- **Termini d'uso di OpenCode Go** — risposta **no** il 2026-11-16: il piano è «designed for OpenCode and other coding agents» e monitora il traffico; un bot famigliare non rientra. Per M2 si usa la chiave Go **solo in sviluppo** e il provider va sostituito prima della produzione (gate in [[progetto/m2-deploy-telegram-agente]]).
- **Libreria Telegram in webhook** — decisa il 2026-11-16: `python-telegram-bot` ≥ 20 (v22.x) in webhook, con `telegram.Bot` come client e non `Application`/`Dispatcher`. Motivazione e conseguenze in [[progetto/stack]] e [[progetto/m2-deploy-telegram-agente]].

Le decisioni già prese, con il loro stato, stanno in [[progetto/stack]]; le regole architetturali non negoziabili sono un soggetto a sé e non sono ripetute qui.
