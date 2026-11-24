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
- **STT locale (Whisper sull'homelab)** — esperimento in coda alla roadmap: richiederebbe l'eccezione «media verso il server domestico» e un fallback cloud, perché il bot non deve perdere i vocali quando l'homelab è giù. Il confronto va fatto in shadow (trascrive e registra, risponde il cloud) prima di spostare il traffico.
- **Memoria della conversazione** — i turni sono stateless: una domanda del bot («quando?», «sì o no?») non può avere risposta al turno dopo. Da chiudere in **M10** (dopo M9, vedi [[progetto/roadmap]]): opzioni sul tavolo, i turni recenti già salvati in `agent_runs` (`input_text`, `outcome`) oppure un uso di Cartographer come memoria del sistema. Fino ad allora: il bot non chiude con una domanda secca e le ambiguità si risolvono con bottoni etichettati ([[progetto/m4-7-eventi-ricorrenti]]).

## Chiuse di recente

- **Modelli con vision** — decisa il 2026-11-24: le immagini le legge l'OCR di Cloud Vision (`TEXT_DETECTION`, endpoint `eu`), non il modello, quindi nessun modello con vision serve; l'immagine non viene conservata e il modello riceve solo il testo ([[architettura/integrazioni]]).

- **STT gestito o audio nativo del modello** — decisa il 2026-11-23: Cloud Speech-to-Text V2 (`short`, multi-regione `eu`), sincrono, audio mai conservato e nessun object storage ([[progetto/m6-vocali]]).

- **Compatibilità MongoDB di Firestore** — chiusa il 2026-11-18 con la suite contract eseguita sul database vero; il presidio periodico vivrà nella pre-produzione.

- **Autenticazione dal servizio Cloud Run all'endpoint MongoDB di Firestore** — chiusa il 2026-11-17: identità del service account, nessun segreto statico, verificata con una richiesta reale.

- **Autenticazione Cloud Run → Firestore** — **risolta** il 2026-11-17: `MONGODB-OIDC` con `ENVIRONMENT:gcp` funziona dal runtime, verificata end-to-end (vedi [[progetto/m2-deploy-telegram-agente]]).
- **Termini d'uso di OpenCode Go** — risposta **no** il 2026-11-16: il piano è «designed for OpenCode and other coding agents» e monitora il traffico; un bot famigliare non rientra. Per M2 si usa la chiave Go **solo in sviluppo** e il provider va sostituito prima della produzione (gate in [[progetto/m2-deploy-telegram-agente]]).
- **Libreria Telegram in webhook** — decisa il 2026-11-16: `python-telegram-bot` ≥ 20 (v22.x) in webhook, con `telegram.Bot` come client e non `Application`/`Dispatcher`. Motivazione e conseguenze in [[progetto/stack]] e [[progetto/m2-deploy-telegram-agente]].

Le decisioni già prese, con il loro stato, stanno in [[progetto/stack]]; le regole architetturali non negoziabili sono un soggetto a sé e non sono ripetute qui.
