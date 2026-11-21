---
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
timestamp: 2026-11-21
title: M4.5 — Routing locale
---
# M4.5 — Routing locale

Milestone inserita dopo M4 per scegliere **quale workflow** avviare prima di un LLM, con un classificatore locale (Laya). Criteri in [[progetto/roadmap]]; il lavoro precedente è in [[progetto/m4-google-calendar]]. Rilascio `v0.5.0` (M5 diventa `v0.6.0`: la minor segue l'ordine delle milestone).

## Obiettivo

Ridurre le chiamate all'LLM a pagamento instradando le richieste note su percorsi locali, senza keyword o regex: decide un modello addestrato sul dominio della famiglia. In questa fase il routing è **shadow**: la previsione viene registrata, la risposta resta dell'LLM.

## Decisioni

| Tema | Scelta |
|---|---|
| Classificatore | Laya (421M, Apache-2.0), checkpoint `multilingual`, esposto via MCP dall'homelab |
| Trigger | **nessun trigger lessicale**: la variabilità del linguaggio lo rende fragile; eventuali scorciatoie esplicite saranno UX, non NLU |
| Tassonomia | cinque workflow stabili: `query`, `task_write`, `event_write`, `ingest`, `chat` |
| Shadow | classificazione in parallelo all'agente, timeout 1 s, nessuna eccezione sul percorso utente |
| Etichette | argento, dai tool chiamati dall'LLM; i turni senza tool non sono etichettati |
| Deploy | MCP adesso; REST `/v1/systemone` quando l'homelab lo pubblica, per il routing attivo |
| Fine-tuning | `laya-train` su JSONL, notebook ufficiale su Kaggle 2×T4 |

## Architettura

- Porta `IntentClassifier`; adapter `LayaClassifier` sopra un client MCP generico (`app/integrations/mcp.py`), riusabile per altri use case (per esempio un estrattore di documenti).
- `agent_runs` porta `route`, `route_confidence`, `route_model`, `llm_used`.
- Export del dataset con `python -m app.cli.routing_export`; valutazione con `python -m app.cli.routing_eval`.
- Verificato da Cloud Run con un job one-off (`v0.5.0`): chiamata MCP riuscita, 308 ms, previsione `task_write` per «che task ho oggi?». Il primo tentativo ha rivelato un newline finale nel secret: un header HTTP illegale. Corretto in `v0.5.1`, timeout portato a 1,5 s.
- Privacy: il testo esce verso l'homelab (UE, operatore), senza media; eccezione consapevole documentata in [[architettura/sicurezza]].

## Baseline zero-shot (2026-11-21)

Set congelato di 60 frasi, almeno 5 per workflow (`tests/fixtures/routing_seed.jsonl`):

| Metrica | Valore |
|---|---|
| Accuratezza | 41/60 = **68,3%** |
| Confidence media | 0,722 |
| Senza risposta | 0 |
| Latenza MCP | ~0,6 s per chiamata |

Errori concentrati su `task_write` (confuso con `chat`, `ingest`, `event_write`) e `ingest`. Zero-shot non basta e alcuni errori hanno confidence alta: per questo lo shadow non decide nulla.

## Fasi

| Fase | Contenuto | Stato |
|---|---|---|
| M4.5a — `v0.5.0` | MCP, client, shadow, telemetria, set congelato, export | fatto e deployato il 2026-11-21 |
| M4.5b — `v0.5.1` | dataset ≥ 1.000 esempi, fine-tune, eval, canary delle sole `query` | da iniziare |
| M4.5c | routing dei media (documenti, immagini, vocali) insieme a M6–M8 | futuro |

## Gate prima del canary

- accuratezza ≥ **95%** sul set congelato e su un held-out scritto a mano;
- **zero** scritture instradate senza LLM;
- nessuna regressione sulle frasi reali di M2 e M4;
- fallback e rollback provati con Laya spento.

## Collegamenti

Regole dell'agente in [[architettura/agente]]; dipendenza homelab e secret in [[infrastruttura/inventario]]; fonti in [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].
