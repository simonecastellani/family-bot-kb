---
title: Porte e fake
type: Architecture
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
status: active
timestamp: 2026-11-13
---
# Porte e fake

Le porte sono `typing.Protocol` definite accanto al proprio fake: le porte cloud in `app/integrations/`, il tempo in `app/core/clock.py`. Lo stato attuale è quello di M7.1: le porte cloud hanno adapter reali, mentre Cloud Storage e Secret Manager restano fake. Il flusso e le regole che le motivano sono in [[architettura/regole]]; la struttura dei moduli è nella stessa pagina.

## Porte implementate

| Porta | Modulo | Cosa astrae | Implementazioni presenti | Test |
|---|---|---|---|---|
| `Clock` | `app/core/clock.py` | il tempo corrente, sempre UTC e timezone-aware | `SystemClock` (produzione), `FixedClock` (test, in `tests/conftest.py`) | `tests/unit/test_fakes.py` |
| `TaskQueue` | `app/integrations/task_queue.py` | accodare un task differito con nome, URL di destinazione, payload e scadenza; rifiuta le scadenze naive | `InMemoryTaskQueue` | `tests/unit/test_fakes.py` |
| `BlobStore` | `app/integrations/blob_store.py` | scrivere, leggere, cancellare e verificare l'esistenza di un blob per chiave | `InMemoryBlobStore` | `tests/unit/test_fakes.py` |
| `SecretProvider` | `app/integrations/secret_provider.py` | leggere un segreto per nome | `InMemorySecretProvider` | `tests/unit/test_fakes.py` |
| `LLMClient` | `app/integrations/llm.py` | completamento testuale su una sequenza di messaggi con ruolo | `FakeLLMClient` (risposta fissa) | `tests/unit/test_fakes.py` |
| `STTClient` | `app/integrations/stt.py` | trascrivere audio con la lingua indicata; errori tipizzati `SpeechAuthError` / `SpeechUnavailable` / `SpeechRejected` | `GoogleSpeechTranscriber` (Speech-to-Text V2, REST), `NullSTTClient` (non configurato), `FakeSTTClient` (trascrizione fissa, registra le chiamate, può fallire) | `tests/unit/test_fakes.py`, `tests/unit/test_google_stt.py`, `tests/unit/test_voice_turn.py` |
| `LinkFetcher` | `app/integrations/link.py` | leggere una pagina pubblica e restituirne titolo, testo ed eventi JSON-LD; errori tipizzati `LinkUnavailable` / `LinkRefused` / `LinkUnsupported` / `LinkTooLarge` | `HttpLinkFetcher` (httpx + html.parser, guard SSRF), `NullLinkFetcher` (non configurato), `FakeLinkFetcher` (pagine configurate, registra le chiamate) | `tests/unit/test_http_link_fetcher.py`, `tests/unit/test_link_turn.py` |

## Fake locali

Tutti i fake sono in memoria, deterministici e senza rete: sono il sostituto locale delle dipendenze cloud previsto dalla regola 10. `InMemoryTaskQueue` conserva i task e sa dire quali sono in scadenza a un istante dato; `InMemoryBlobStore` e `InMemorySecretProvider` partono vuoti e si popolano a runtime; `FakeLLMClient` restituisce un testo fisso configurabile; `FakeSTTClient` fa lo stesso e registra audio e lingua di ogni chiamata. Il fake del tempo, `FixedClock`, vive nei test perché serve solo a renderli deterministici. `FakeTelegramClient` registra i messaggi inviati e serve i file richiesti (`download_file`), così il percorso del vocale si prova senza rete.

Gli adapter reali esistono ormai per Cloud Tasks, Google Calendar, Speech-to-Text, il provider LLM (PydanticAI) e Telegram; i repository hanno gli adapter `mongo/` accanto a quelli in memoria. Mancano le porte per Cloud Storage e Secret Manager (i fake restano in uso). `/readyz` verifica il caricamento della configurazione, non le dipendenze.
