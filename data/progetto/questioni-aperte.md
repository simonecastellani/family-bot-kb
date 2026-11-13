---
title: Questioni aperte
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-13
---
# Questioni aperte

Domande non ancora risolte, con la condizione o il momento in cui vanno chiuse (§14). Molte si chiudono dentro una milestone precisa: vedi [[progetto/roadmap]]. Fonte: [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].

## Aperte

- **Copertura reale della compatibilità MongoDB di Firestore** — per gli operatori e gli stage che servono (aggregazioni, indici, transazioni): verificare in M1 con i contract test su database reale.
- **Autenticazione dal servizio Cloud Run all'endpoint MongoDB di Firestore** — preferibilmente con IAM e identità del servizio, senza segreti statici.
- **Regioni** — disponibilità dell'edizione Enterprise e applicabilità delle quote gratuite in regioni UE, vicine a Cloud Run.
- **Strategia di backup/restore** — da definire, dato che non ha uso gratuito.
- **Modelli con vision/audio** — quali modelli di OpenCode Go li supportano; STT gestito oppure audio nativo del modello.
- **Termini d'uso di OpenCode Go** — ammettono un bot non-coding? Prevedere un provider alternativo dietro `LLMClient` (es. Gemini) e la gestione di errori 401 e retry.
- **Libreria Telegram in webhook** — `aiogram` 3 oppure `python-telegram-bot` ≥ 20.

Le decisioni già prese, con il loro stato, stanno in [[progetto/stack]]; le regole architetturali non negoziabili sono un soggetto a sé e non sono ripetute qui.
