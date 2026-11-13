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

## Speech to text

Il documento di configurazione manuale di Google Cloud **non copre** questo soggetto: nessuna API, quota o credenziale STT compare nelle sue fasi.

Quello che la KB sa già: lo STT è un **[Default]** del primer — servizio gestito (Speech-to-Text) oppure audio nativo del modello, se disponibile ([[progetto/stack]]) — ed esiste la porta `STTClient` con il suo fake ([[architettura/porte-e-fake]]).

- [ ] Verificare quale servizio STT si adotta e cosa richiede su GCP (API, quota, credenziali).

## Link e immagini

Anche questo soggetto **non è coperto** dal documento di configurazione GCP: nessuna API, nessun bucket dedicato, nessuna credenziale per il recupero di link o per le immagini.

Quello che la KB sa già: il repo prevede un adapter `link_fetcher` fra le integrazioni previste ([[architettura/regole]]); i file e gli allegati vivono in Cloud Storage, nel bucket dei documenti senza accesso pubblico ([[infrastruttura/identita-e-runtime]]).

- [ ] Verificare quali servizi servono per link e immagini e se riusano il bucket dei documenti.

## Fonte

Distillato da [[sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]], fase E2. Le sezioni *Speech to text* e *Link e immagini* registrano un'assenza della fonte, non un fatto.
