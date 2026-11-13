---
title: Porte e fake
type: Architecture
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
timestamp: 2026-11-13
---
# Porte e fake

Scheletro delle porte previste dal primer. Il contenuto completo — porte come interfacce nel codice, adapter reali e fake locali — arriva dall'unità U15 della campagna di ingest; il flusso e le regole che le motivano sono in [[architettura/regole]].

## Porte previste

Dipendenze cloud dietro porta, ognuna con adapter reale e fake locale (§5, regola 10):

- `TaskQueue`
- `BlobStore`
- `SecretProvider`
- `LLMClient`
- `STTClient`

Trasversale, non cloud: `Clock` (il tempo è iniettato nei servizi, §5 regola 10).

La struttura del repo prevede inoltre porte `Protocol` per ogni aggregato, con adapter `mongo/` e `in-memory` (§4).
