---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-13
title: Sviluppo locale e test
type: Pagina
---
# Sviluppo locale e test

Ambiente di sviluppo e strategia di test del progetto, dal primer §7. Le scelte di stack e la lista dei tool sono in [[progetto/stack]]; le convenzioni di scrittura del codice in [[sviluppo/qualita]].

## Ambiente locale

- `docker-compose.yml` con **MongoDB** in una versione allineata alle funzionalità supportate da Firestore, e fake per Telegram, Calendar, LLM, STT e coda dei task.
- Le immagini Docker sono minimali e girano non-root (primer §12).
- La configurazione arriva solo da variabili d'ambiente, letta con `pydantic-settings` (primer §12).

## Fake

- L'ambiente locale include fake per Telegram, Calendar, LLM, STT e coda dei task.
- Il fake di `TaskQueue` esegue (o simula) l'invocazione dell'endpoint interno all'orario previsto.
- Nei test l'LLM è mockato (ad esempio `TestModel`/`FunctionModel` di PydanticAI) e il `Clock` è finto.

## Contract test

- **Contract test dei repository**: una sola suite, eseguita in due contesti — su MongoDB locale a ogni commit e su un database Firestore Enterprise reale di sviluppo in un workflow manuale/notturno.
- Serve a intercettare le differenze di compatibilità che i test locali non vedono.
- Non usare librerie che simulano MongoDB in memoria al posto di un MongoDB reale: divergono dal comportamento reale più di quanto convenga.
