---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-16
title: Questioni aperte
type: Pagina
---
# Questioni aperte

Domande non ancora risolte, con la condizione o il momento in cui vanno chiuse (§14). Molte si chiudono dentro una milestone precisa: vedi [[progetto/roadmap]]. Fonte: [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].

## Aperte

- **Copertura reale della compatibilità MongoDB di Firestore** — per gli operatori e gli stage che servono (aggregazioni, indici, transazioni): la verifica con i contract test su database reale è **spostata a M3**, insieme al servizio `worker`, perché richiede un percorso OIDC funzionante dal runner. Fino ad allora i contract girano solo su MongoDB locale. Vedi [[progetto/m2-deploy-telegram-agente]].
- **Autenticazione dal servizio Cloud Run all'endpoint MongoDB di Firestore** — preferibilmente con IAM e identità del servizio, senza segreti statici. In M2 è la prima verifica di W0: se fallisce, il piano si ferma lì.
- **Regioni** — disponibilità dell'edizione Enterprise e applicabilità delle quote gratuite in regioni UE, vicine a Cloud Run.
- **Strategia di backup/restore** — da definire, dato che non ha uso gratuito.
- **Modelli con vision/audio** — quali modelli di OpenCode Go li supportano; STT gestito oppure audio nativo del modello.
- **Termini d'uso di OpenCode Go** — ammettono un bot non-coding? Da verificare in W0 di M2; se la risposta è no, cambia il provider dietro `AgentEngine`, non il piano. Resta comunque da prevedere la gestione di errori 401 e retry.

## Chiuse di recente

- **Libreria Telegram in webhook** — decisa il 2026-11-16: `python-telegram-bot` ≥ 20 (v22.x) in webhook, con `telegram.Bot` come client e non `Application`/`Dispatcher`. Motivazione e conseguenze in [[progetto/stack]] e [[progetto/m2-deploy-telegram-agente]].

Le decisioni già prese, con il loro stato, stanno in [[progetto/stack]]; le regole architetturali non negoziabili sono un soggetto a sé e non sono ripetute qui.
