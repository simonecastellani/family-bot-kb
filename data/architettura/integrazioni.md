---
title: Integrazioni con servizi esterni
type: Concept
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
status: active
timestamp: 2026-11-13
---
# Integrazioni con servizi esterni

Le integrazioni verso l'esterno previste per family-bot e la loro parte su Google Cloud. Il contratto delle porte che le isolano è in [[architettura/porte-e-fake]]; le regole di autenticazione in [[architettura/sicurezza]]; le altre risorse GCP in [[infrastruttura/identita-e-runtime]] e [[infrastruttura/gcp-bootstrap]].

## Google Calendar

Fase M4 della configurazione manuale.

- Va abilitata l'API `calendar-json.googleapis.com`.
- Serve una **OAuth consent screen** configurata.
- **Account Gmail personale**: client OAuth "External", scope Calendar, **refresh token in Secret Manager** (i segreti previsti sono elencati in [[infrastruttura/identita-e-runtime]]).
- Attenzione: un'app OAuth in modalità **Testing** può far scadere il refresh token dopo **7 giorni**; per un uso continuativo va pubblicata, con le implicazioni del caso.
- **Google Workspace**: service account con domain-wide delegation, se l'organizzazione lo consente.
- Il bot deve **chiedere conferma** prima di scrivere sul calendario (comportamento dell'agente in [[architettura/agente]]).
- Gli eventi importati hanno già una cache dedicata, la collection `calendar_events` ([[architettura/modello-dati]]).

## Speech to text (M6)

Adottato: **Cloud Speech-to-Text V2** con riconoscimento sincrono. Il vocale Telegram (OGG/Opus) viene scaricato in memoria, trascritto e **scartato**: nessun oggetto su Cloud Storage, nessuna retention. Dettagli della milestone in [[progetto/m6-vocali]]; la procedura sta in `docs/gcp-setup.md` (fase E4).

- API `speech.googleapis.com`; ruolo `roles/speech.client` al **solo** runtime `api`; autenticazione ADC con scope `cloud-platform`, nessun secret.
- Endpoint REST `https://eu-speech.googleapis.com/v2/projects/{progetto}/locations/eu/recognizers/_:recognize` con `auto_decoding_config` (l'header del container dice l'encoding), `language_codes: ["it-IT"]` e `model: short`.
- `europe-west8` (Milano) **non è una regione Speech**: si usa il multi-regione `eu`, così l'audio resta in Europa. `short` è il modello che la tabella delle lingue supportate elenca per `eu` con `it-IT`.
- Limiti del riconoscimento sincrono: 10 MB o **un minuto**. Il bot rifiuta i vocali più lunghi prima di scaricarli; audio inoltrati e video-note restano fuori da M6.
- Costo: la V2 si paga dal primo minuto (0,016 $/min); il free tier di 60 min/mese è della V1, non della V2.
- La porta `STTClient` con i suoi errori e il fake è in [[architettura/porte-e-fake]].

## Link (M7.1)

Implementato il 2026-11-24: **nessuna API, nessun bucket e nessun secret**. Il bot scarica la pagina inoltrata da Cloud Run (uscita internet pubblica, nessun connettore VPC), ne legge testo e JSON-LD e la scarta.

- Solo `http(s)` pubblici: niente credenziali nell'URL, porte 80/443, massimo 3 redirect rivalidati, 1 MB e 6 s.
- Filtro SSRF prima di ogni richiesta: gli host che risolvono a indirizzi privati, loopback, link-local o metadata (`169.254.169.254`, `metadata.google.internal`) vengono rifiutati.
- Il testo della pagina esce verso il provider LLM come **contenuto non fidato**; nessuna scrittura senza conferma. Dettagli in [[architettura/sicurezza]] e [[progetto/m7-1-link]].

## Immagini (M7.2)

Deciso il 2026-11-24: le legge l'**OCR di Cloud Vision** (`TEXT_DETECTION`, endpoint regionale `eu-vision.googleapis.com`), non un modello con vision. L'immagine resta in memoria e viene scartata; il provider LLM riceve solo il testo. Il bucket dei documenti non c'entra: serve a M8 per i menu, non alle foto.

## Fonte

Distillato da [[sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]], fase E2. Le sezioni *Speech to text* e *Link e immagini* registrano un'assenza della fonte, non un fatto.
