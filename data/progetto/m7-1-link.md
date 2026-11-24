---
status: in-progress
timestamp: 2026-11-24
title: M7.1 — Link
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
---
# M7.1 — Link

## Obiettivo

Un link inoltrato con data e luogo produce una **proposta strutturata** di evento, da confermare con il bottone (criterio di M7 in [[progetto/roadmap]]). Il link è solo un altro ingresso al turno, come il vocale di [[progetto/m6-vocali]]: la scrittura resta `create_event` con la conferma di M4.

## Dentro

- Lettura **una volta sola** della pagina inoltrata: solo `http(s)`, niente credenziali nell'URL, porte 80/443, al massimo 3 redirect rivalidati, tetto di byte e timeout.
- Filtro **SSRF**: ogni host, anche dopo un redirect, deve risolvere a un indirizzo pubblico; loopback, reti private, link-local e metadata server vengono rifiutati.
- Estrazione del **testo visibile** (`html.parser`, senza script/style/noscript) e del **JSON-LD schema.org** con `@type` che finisce per `Event` (`@graph` compreso), come suggerimento strutturato prima del testo.
- **Contesto non fidato** del turno: il prompt lo chiude in `<contenuto_non_fidato tipo="link" fonte="…">`, lo dichiara dato e non istruzione, e vieta di inventare date e luoghi.
- Link inoltrato **senza testo** = richiesta implicita di proporre l'evento; se manca la data o il luogo il bot lo dice e non propone nulla.
- Al massimo **2 URL** per messaggio, 1 MB per pagina, 8.000 caratteri di testo, 6 s di timeout; un URL duplicato si legge una volta.
- Dedupe dell'update Telegram **prima** del fetch: un retry non paga una seconda richiesta né un secondo turno.
- `agent_runs.input_kind = "link"`; nessuna collection, nessun indice e nessuna migrazione nuovi.

## Fuori

- PDF, documenti, immagini e video: i documenti sono M8, le foto M7.2.
- Pagine che richiedono JavaScript o un login, e album di più foto.
- Conservazione della pagina: non esiste, per scelta.
- Un secondo adapter (browser headless, servizio di estrazione): la porta è pronta.

## Decisioni (2026-11-24)

- **Adapter** `HttpLinkFetcher` in `app/integrations/http_link_fetcher.py`, sopra `httpx` e `html.parser`: nessuna dipendenza nuova e nessun servizio GCP. La porta `LinkFetcher` è in [[architettura/porte-e-fake]].
- **Nessuna risorsa GCP**: Cloud Run esce su internet pubblico senza connettore VPC; la fase E5 di `docs/gcp-setup.md` registra l'assenza di API, bucket e secret.
- **JSON-LD come suggerimento, non come secondo percorso**: anche quando c'è, decide l'agente con lo stesso tool `create_event`; così una sola strada di conferma e un solo audit.
- **Pagina scartata**: vive in memoria per il turno; nel run restano l'URL (che è il testo del messaggio), l'estrazione negli argomenti di `create_event` e l'esito.
- **Testo della pagina al provider LLM**: è contenuto pubblico ma non fidato; la difesa vera resta la conferma. Dettagli in [[architettura/sicurezza]].

## Implementazione

- `app/integrations/link.py`: porta, `FetchedPage`, `PageEvent`, errori tipizzati e fake.
- `app/integrations/http_link_fetcher.py`: adapter, guard SSRF, redirect, limiti, `extract_page`.
- `app/services/link_turn.py`: dedupe, membro, massimo due URL, fetch best-effort, `describe_page`, delega a `AgentTurnService`.
- `app/interfaces/telegram/updates.py`: `IncomingMessage.links` dagli entity `url` e `text_link` (URL nascosto), con fallback regex; `routes.py` sceglie `link_turn` quando ci sono link.
- `app/integrations/pydantic_ai_agent.py`: `build_prompt` e le istruzioni sul contenuto non fidato.
- Config: `LINK_FETCH_TIMEOUT_SECONDS`, `LINK_MAX_BYTES`, `LINK_MAX_URLS`, `LINK_MAX_TEXT_CHARS` in `Settings`, `.env.example` e `deploy/api.env.yaml`.

## Verifica

La suite completa passa (unit + contract su MongoDB locale), `ruff` e `pyright` puliti. I test coprono: estrazione di titolo/testo/JSON-LD, tetto dei campi, redirect, SSRF, timeout, content-type, dedupe, membro non registrato, link parziale, limite di URL, entity `text_link`, prompt con e senza testo del membro.

Prova reale dell'adapter: il 2026-11-24 `https://www.eventbrite.it/` ha restituito titolo e 3.617 caratteri; `https://www.comune.milano.it/` ha risposto 403 e il bot lo riporta senza creare un run.

Deploy fatto il 2026-11-24: `v0.10.0`, revisione `family-bot-api-00040-goq` al 100% del traffico, `/livez` e `/readyz` riportano `0.10.0`.

Prova dal telefono (2026-11-24/25): una homepage senza eventi (La Stampa) è stata rifiutata correttamente, un PDF inviato come documento è rimasto non supportato, un link Ticketmaster ha ricevuto 403 (vedi il limite sotto). Restano da provare un link leggibile con data/luogo e la pagina ostile `https://gist.github.com/simonecastellani/1745bc4523e5dab594dd9ce0fe08c09a/raw`. Nel log agente il run deve avere `input_kind: link`.

## Limite osservato (2026-11-25)

Ticketmaster risponde **403** alle richieste da Cloud Run, mentre la stessa richiesta dall'ADSL dell'operatore riceve `200`: il sito blocca i client da datacenter, non è il parser del bot. Il bot risponde «Non riesco a leggere questo link» senza creare un run. Per questi eventi la via è la **foto** letta dall'OCR di [[progetto/m7-2-foto]]; un crawler browser-like o un servizio di terze parti resta fuori scope.
