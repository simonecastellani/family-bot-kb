---
title: M7.2 — Foto con OCR
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
timestamp: 2026-11-25
---
# M7.2 — Foto con OCR

## Obiettivo

Una foto di un volantino con data e luogo produce una **proposta strutturata** di evento, da confermare con il bottone (criterio di M7 in [[progetto/roadmap]]). La foto è un altro ingresso al turno, come il vocale di [[progetto/m6-vocali]] e il link di [[progetto/m7-1-link]]; la scrittura resta `create_event` con la conferma di M4.

## Dentro

- **Foto** (`message.photo`, la più grande per area: Telegram non garantisce l'ordine) e **immagini inviate come file** (`image/jpeg`, `image/png`, `image/webp`); la caption diventa il testo del membro.
- Dedupe dell'update Telegram **prima** del download: un retry non scarica e non riconosce due volte.
- Limite di **10 MB** applicato prima del download, poi di nuovo sui byte ricevuti.
- OCR con **Cloud Vision** `TEXT_DETECTION`, endpoint regionale `eu`, immagine inline; il testo è **contesto non fidato** del turno e l'immagine viene **scartata**.
- `AgentTurnService` riceve `input_kind="image"`; una foto senza testo è una richiesta implicita di proporre l'evento; se l'immagine non è leggibile il bot lo dice.
- Con una caption e un'immagine illeggibile il turno **prosegue** sulla caption, con l'avviso «Non sono riuscito a leggere la foto».
- `agent_runs.input_kind = "image"`; `input_text` è la caption (vuota per una foto nuda); l'estrazione resta visibile negli argomenti di `create_event`. Nessuna collection, indice o migrazione nuovi.

## Fuori

- PDF e altri documenti (M8), HEIC e formati non elencati, video e video-note, album di più foto.
- Conservazione dell'immagine: non esiste, per scelta.
- Un secondo provider OCR: la porta è pronta, l'adapter arriva quando serve.

## Decisioni (2026-11-24/25)

- **OCR, non un modello con vision**: Cloud Vision `TEXT_DETECTION` sull'endpoint `eu`. Chiude la domanda aperta sui modelli vision; il provider LLM riceve solo il testo, come per un vocale.
- **Nessun `languageHints`** per il latino: la documentazione dice che il riconoscimento automatico è migliore e un hint sbagliato peggiora il risultato. La porta accetta un hint opzionale, la configurazione lo lascia vuoto.
- **Provisioning**: `vision.googleapis.com` abilitata il 2026-11-25; **nessun ruolo IAM aggiuntivo** (verificato con token impersonato sulla regione `eu`).
- **Porta provider-agnostica**: `OCRClient` in `app/integrations/ocr.py`; `OCR_PROVIDER` ha un solo valore oggi (`google`) e `_build_ocr` è il punto di scelta.
- **Costo**: 1.000 unità `TEXT_DETECTION`/mese gratuite, poi 1,50 $/1.000.
- **Rilascio**: `v0.11.0`.

## Implementazione

- `app/integrations/ocr.py`: porta, errori (`OCRAuthError`, `OCRUnavailable`, `OCRRejected`), null adapter e fake.
- `app/integrations/google_vision.py`: adapter REST, endpoint regionale, `TEXT_DETECTION`, `fullTextAnnotation` con fallback su `textAnnotations`, errori per immagine mappati.
- `app/services/photo_turn.py`: dedupe, limiti, download, OCR, delega e avviso di lettura parziale.
- `app/interfaces/telegram/updates.py`: `IncomingPhoto`, photo più grande per area, documenti immagine; `routes.py` instrada a `photo_turn`.
- Config: `OCR_PROVIDER`, `OCR_PROJECT_ID` (fallback `GCP_PROJECT_ID`), `OCR_LOCATION=eu`, `OCR_TIMEOUT_SECONDS`, `IMAGE_MAX_BYTES` in `Settings`, `.env.example` e `deploy/api.env.yaml`.
- La procedura manuale è la fase E6 di `docs/gcp-setup.md`; lo stato delle risorse in `docs/gcp-inventory.md`.

## Duplicati (v0.11.1)

La prova dal telefono ha mostrato che lo stesso evento, letto prima da un link e poi da uno screenshot, veniva proposto e creato due volte. Prima di preparare `create_event` il tool chiama `FindCalendarDuplicateService`, che legge la cache attorno all'inizio (±1 giorno) e confronta:

- **titolo** normalizzato (NFKD, senza maiuscole, accenti e punteggiatura) con le parole tutte contenute nell'altro, minimo 8 caratteri — così «… | 04 maggio 2027, Milano» è lo stesso titolo;
- oppure **luogo** normalizzato con la stessa regola, minimo 8 caratteri (una città da sola non basta);
- **stesso giorno** e **inizio entro 60 minuti** (tolleranza per OCR e arrotondamenti).

Se trova una corrispondenza non prepara la proposta: restituisce l'evento trovato al modello, che lo dice al membro e, se serve, propone `update_event`. Il controllo è di sola lettura; due spettacoli diversi nello stesso giorno restano possibili se distano più di un'ora. Non copre due proposte confermate prima che la cache si aggiorni, né eventi diversi allo stesso orario nello stesso luogo.

Test: `tests/unit/test_calendar_duplicate.py` (confronto, tolleranza, all-day, fuso, luogo) e il caso di integrazione in `tests/integration/test_agent_engine.py`.

## Verifica

La suite completa passa (unit + contract su MongoDB locale), `ruff` e `pyright` puliti. I test coprono: richiesta regionale e body, hint opzionale, `fullTextAnnotation` e fallback, errori per immagine, 401/403/429/5xx/timeout, immagine oltre il limite, dedupe, membro non registrato, limiti prima e dopo il download, OCR giù, testo vuoto con e senza caption, MIME di un file, photo più grande e PDF non supportato nel webhook.

Provisioning verificato il 2026-11-25: `vision.googleapis.com` abilitata; chiamata con token impersonato del runtime `api` sull'endpoint `eu` → `200` e testo corretto su un PNG generato in locale.

Deploy fatto il 2026-11-25: `v0.11.0` (revisione `family-bot-api-00042-qiq`) e poi `v0.11.1` (revisione `family-bot-api-00044-yim`) con il controllo duplicati; `/livez` riporta `0.11.1`.

Prova dal telefono (2026-11-25): link, foto di un volantino, PDF come documento, pagina ostile e screenshot Ticketmaster → tutto ok. Ha però rilevato che lo **stesso evento** è stato aggiunto due volte, corretto in `v0.11.1` (vedi sotto); resta la riprova del duplicato e la chiusura. La foto copre anche il caso Ticketmaster di [[progetto/m7-1-link]], dove il link risponde 403 a Cloud Run.
