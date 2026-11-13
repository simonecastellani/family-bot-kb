---
title: Porte e fake
type: Architecture
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
status: active
timestamp: 2026-11-13
---
# Porte e fake

Le porte sono `typing.Protocol` definite accanto al proprio fake: le porte cloud in `app/integrations/`, il tempo in `app/core/clock.py`. Lo stato attuale è quello di M0: esistono solo implementazioni in memoria, nessun adapter reale verso GCP o MongoDB. Il flusso e le regole che le motivano sono in [[architettura/regole]]; la struttura dei moduli è nella stessa pagina.

## Porte implementate

| Porta | Modulo | Cosa astrae | Implementazioni presenti | Test |
|---|---|---|---|---|
| `Clock` | `app/core/clock.py` | il tempo corrente, sempre UTC e timezone-aware | `SystemClock` (produzione), `FixedClock` (test, in `tests/conftest.py`) | `tests/unit/test_fakes.py` |
| `TaskQueue` | `app/integrations/task_queue.py` | accodare un task differito con nome, URL di destinazione, payload e scadenza; rifiuta le scadenze naive | `InMemoryTaskQueue` | `tests/unit/test_fakes.py` |
| `BlobStore` | `app/integrations/blob_store.py` | scrivere, leggere, cancellare e verificare l'esistenza di un blob per chiave | `InMemoryBlobStore` | `tests/unit/test_fakes.py` |
| `SecretProvider` | `app/integrations/secret_provider.py` | leggere un segreto per nome | `InMemorySecretProvider` | `tests/unit/test_fakes.py` |
| `LLMClient` | `app/integrations/llm.py` | completamento testuale su una sequenza di messaggi con ruolo | `FakeLLMClient` (risposta fissa) | `tests/unit/test_fakes.py` |
| `STTClient` | `app/integrations/stt.py` | trascrivere audio con lingua indicata | `FakeSTTClient` (trascrizione fissa) | `tests/unit/test_fakes.py` |

## Fake locali

Tutti i fake sono in memoria, deterministici e senza rete: sono il sostituto locale delle dipendenze cloud previsto dalla regola 10. `InMemoryTaskQueue` conserva i task e sa dire quali sono in scadenza a un istante dato; `InMemoryBlobStore` e `InMemorySecretProvider` partono vuoti e si popolano a runtime; `FakeLLMClient` e `FakeSTTClient` restituiscono un testo fisso configurabile. Il fake del tempo, `FixedClock`, vive nei test perché serve solo a renderli deterministici.

Mancano, e non esistono ancora le porte corrispondenti: adapter reali per Cloud Tasks, Cloud Storage, Secret Manager, provider LLM/STT e Google Calendar; repository e adapter `mongo/` per gli aggregati; porte per Telegram, `link_fetcher` e per i servizi di dominio. `/readyz` oggi verifica solo il caricamento della configurazione, non le dipendenze.
