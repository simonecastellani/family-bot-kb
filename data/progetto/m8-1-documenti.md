---
timestamp: 2026-11-26
title: M8.1 — Documenti e menù del nido
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
---
# M8.1 — Documenti e menù del nido

## Obiettivo

Il menù settimanale del nido arriva come **screenshot o PDF** e diventa un dato strutturato per settimana, senza conservare il file: è il primo pezzo di M8 ([[progetto/roadmap]]). Gli ingressi restano quelli di [[progetto/m7-2-foto]]: un'immagine è effimera finché il membro non decide di conservarla; un PDF non si può leggere prima, quindi il bot propone direttamente di salvarlo.

## Flusso

1. **Screenshot**: OCR effimero (Cloud Vision), l'agente legge il testo; se sembra un menù del nido chiama `save_nursery_menu` e propone «Salvo il menù della settimana del …?».
2. **PDF**: il turno è deterministico, senza LLM: il bot propone «Lo salvo e leggo il menù del nido?».
3. **Tap** → il file viene scaricato da Telegram, scritto su Cloud Storage con l'id del documento, registrato in `documents` e accodato un task Cloud Tasks verso `/internal/documents`.
4. **Worker** → OCR (Cloud Vision per le immagini, Document AI per i PDF) → estrazione strutturata con output Pydantic → merge in `nursery_menus` della settimana → `documents.status = extracted` → **cancellazione dell'oggetto** → messaggio al membro.
5. **Fallimento** → stato `failed`, file cancellato lo stesso, messaggio al membro con la causa quando è utile (settimana assente, non è un menù, lettura fallita).

## Dentro

- Collection `documents` (stato `stored`/`processing`/`extracted`/`failed`, testo OCR, estratto, `week_start`) e `nursery_menus` (`_id` = lunedì ISO, giorni uniti da più documenti). Indici `documents_sha256` e `documents_status_created`.
- Porta `BlobStore` con l'adapter **GoogleCloudStorageBlobStore** (REST + ADC, nessuna dipendenza nuova); porta `PdfOCRClient` con **GoogleDocumentAIOCR** (Enterprise Document OCR, `eu`, PDF inline); porta `MenuExtractor` con **PydanticAIMenuExtractor**.
- `menu.store` come `AgentActionKind`, executor `DocumentActionExecutors`; il payload porta solo `file_id`/MIME/caption, non i byte.
- `input_kind = "document"` per i PDF.
- Config `DOCUMENTS_BUCKET`, `DOCUMENT_MAX_BYTES`, `DOCUMENT_AI_*`; il worker riottiene `opencode-api-key` perché estrae.
- Test: contract su entrambi i repository, unit su GCS/Document AI/extractor, sul salvataggio e sull'estrazione (merge, idempotenza, cancellazione, settimana assente, non-menù), integrazione sul tool dell'agente e sul webhook PDF.

## Fuori

- Piano delle cene `draft`/`confirmed`, lista della spesa e modifica a linguaggio naturale: M8.2 e M8.3.
- Conservazione del file: per scelta il bucket non tiene i documenti.
- PDF oltre 10 MB, più documenti in un solo messaggio, album, HEIC; documenti diversi dal menù.
- Vista in dashboard: arriva con M8.3, quando c'è un piano da mostrare.

## Decisioni

- **Document AI** per i PDF: chiamata sincrona, 15 pagine e 40 MB, prime 1.000 pagine/mese gratuite, multi-regione `eu`. Evita l'output su GCS che il batch di Cloud Vision richiederebbe.
- **Il file si cancella subito**: `documents` non è un archivio di allegati; restano testo ed estratto. Cambiare questa regola significa cambiare il primer §6 e §10.
- **Solo il tap conserva**: uno screenshot di volantino non propone il salvataggio, un menù sì; nessuna immagine viene scritta su GCS senza conferma.
- **Settimana obbligatoria**: se né il testo né la didascalia dicono la settimana, il documento fallisce e il bot chiede di rimandarlo con la settimana scritta; non inventa una data.
- **Due screenshot della stessa settimana si uniscono** per giorno: vince il documento più recente per i giorni che porta, gli altri restano.

## Verifiche fatte (2026-11-26)

- `documentai.googleapis.com` abilitata e processor `family-bot-menu` creato via REST; una chiamata con token impersonato del worker risponde `200` e legge un PDF di prova.
- L'estrazione reale con `deepseek-v4.1-flash` sul testo di un menù di prova restituisce il lunedì corretto e i giorni strutturati.
- Suite completa verde, `ruff` e `pyright` puliti.

## Cosa manca

- Deploy dell'API e del worker, prova dal telefono con uno screenshot reale e un PDF, tag `v0.12.0`.
- Le fixture con screenshot reali dei menù del nido (l'app del nido cambia formato): servono prima della prova in cloud.

## Riferimenti

- Modello: [[architettura/modello-dati]].
- Servizi esterni: [[architettura/integrazioni]].
- Porte e fake: [[architettura/porte-e-fake]].
- Dati e privacy: [[architettura/sicurezza]].
- Risorse GCP: [[infrastruttura/inventario]].
