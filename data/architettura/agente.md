---
status: active
title: Agente
type: Architecture
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
---
# Agente

L'agente è il livello conversazionale che traduce i messaggi dei membri in operazioni sui servizi. Non parla direttamente con il database: usa tool, chiede conferma prima di scrivere e registra ogni esecuzione.

## Tool

- I tool sono **wrapper sottili sui servizi**, non logica di business duplicata: `create_task`, `complete_task`, `snooze_task`, `list_tasks`, `get_week_overview`, `create_event`, `save_menu` e così via.
- Il **system prompt** include data e ora corrente, fuso orario e il membro che sta scrivendo.
- Gli output sono **strutturati (Pydantic)** per eventi, task e menu; se l'output non è valido si ritenta.
- Con i modelli open il function calling è meno solido: date relative e ambiguità vanno testate presto.
- Le occorrenze create o spostate dai tool alimentano il flusso dei promemoria descritto in [[architettura/promemoria]].

## Conferme

- **Conferma esplicita** con bottoni inline prima di ogni scrittura su calendario e prima di ogni modifica o cancellazione.
- Il contenuto di link e messaggi inoltrati è **non fidato** (prompt injection): non può innescare una scrittura senza conferma.

## Registrazione delle run

Ogni run dell'agente è registrata in `agent_runs` **dal primo giorno**: la registrazione non è un'aggiunta successiva, fa parte del funzionamento normale.
