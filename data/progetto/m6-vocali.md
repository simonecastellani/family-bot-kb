---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: done
timestamp: 2026-11-23
title: M6 — Vocali con STT
type: Pagina
---
# M6 — Vocali con STT

## Obiettivo

Un vocale in italiano diventa un task corretto: il membro parla invece di scrivere e il bot si comporta come se avesse ricevuto un messaggio di testo (§11). Il vocale è **solo un altro ingresso al turno**: nessuna scrittura senza conferma, nessuna capacità di UI.

## Dentro

- Trascrizione con **Cloud Speech-to-Text V2**, riconoscimento sincrono, lingua `it-IT`.
- Lo stesso `AgentTurnService` del testo: la trascrizione diventa `input_text` del run, con `input_kind: voice`.
- Limiti prima di scaricare: **60 secondi** e 10 MB; oltre, messaggio chiaro e nessuna chiamata al provider.
- Eco di debug «Ho capito: …» sopra la risposta, per vedere subito una trascrizione sbagliata.
- Dedupe dell'update Telegram **prima** della trascrizione: un retry non paga due volte.

## Fuori

- Audio inoltrati (mp3, m4a) e video-note: non supportati.
- Vocali oltre il minuto: il riconoscimento sincrono non li accetta; il batch asincrono (Cloud Storage + job) sarebbe una milestone a sé.
- Conservazione dell'audio: non esiste, per scelta.
- STT locale sull'homelab: rimandato in coda alla roadmap ([[progetto/questioni-aperte]]).

## Criteri di accettazione

- Un vocale con una richiesta chiara produce la trascrizione, una proposta e, dopo conferma, un task corretto.
- Vocale oltre 60 s → rifiuto, nessuna chiamata STT, nessun run.
- STT irraggiungibile → messaggio di errore in italiano, nessuna scrittura, run con l'errore.
- Log agente: il run ha `input_kind: voice`; l'audio non compare da nessuna parte.

## Decisioni prese (2026-11-23)

- **Provider**: Cloud Speech-to-Text V2, modello `short` (l'unico che la tabella delle lingue supportate elenca per il multi-regione `eu` con `it-IT`), endpoint implicito `recognizers/_`.
- **Regione**: `eu`, perché `europe-west8` (Milano) non è una regione Speech; l'audio resta in Europa.
- **Costo accettato**: la V2 si paga dal primo minuto (0,016 $/min); il free tier di 60 min/mese è della V1, che non si usa. Un vocale di 20 s costa circa mezzo centesimo.
- **Autenticazione**: ADC del service account `api`, scope `cloud-platform`, nessun secret. L'adapter usa REST + `httpx`, come il calendario; la porta `STTClient` e il fake sono in [[architettura/porte-e-fake]].
- **Eco della trascrizione**: aiuto di sviluppo (`VOICE_SHOW_TRANSCRIPT`), da spegnere quando il riconoscitore sarà affidabile; a tendere il membro non la vedrà più.
- **Niente storage**: l'audio vive in memoria per la durata della richiesta e viene scartato. La trascrizione resta nel run perché è il testo del turno, come per un messaggio scritto.
- **Rilascio**: `v0.9.0`.

## Implementazione

- Porta e errori in `app/integrations/stt.py`; adapter in `app/integrations/google_stt.py`; provider ADC condiviso in `app/integrations/google_auth.py`.
- `app/services/voice_turn.py` (`VoiceTurnService`) applica dedupe, limiti, download (`TelegramClient.download_file`), trascrizione e delega a `AgentTurnService`; `IncomingVoice` nasce dal parse di `message.voice`.
- `agent_runs.input_kind` distingue testo e voce; la configurazione manuale è la fase E4 di `docs/gcp-setup.md`, lo stato delle risorse in `docs/gcp-inventory.md`.
- I test coprono: adapter (200, 401/403, 429/5xx, timeout, audio oltre il limite), turno vocale (limiti, download fallito, STT giù, trascrizione vuota, retry, eco accesa e spenta) e webhook.

## Verifica (chiusa il 2026-11-24)

Il primo tentativo di concessione del ruolo, fatto dalla console, lo aveva messo nella binding già condizionata al database (`resource.name == "projects/family-bot-dev/databases/family-bot"`): la condizione non è mai vera per una richiesta Speech, quindi il ruolo sembrava concesso e l'API rispondeva `403`. Corretto rimuovendo il binding condizionato e riaggiungendo il ruolo con `--condition=None`; è lo stesso errore di M5 sugli utenti Firebase, e vale come regola per ogni nuovo ruolo a questi service account. La traccia completa è in `docs/gcp-inventory.md`.

- [x] test, lint e type-check verdi;
- [x] `speech.googleapis.com` abilitata e `roles/speech.client` al solo runtime `api`, **senza condizione**;
- [x] verifica con token impersonato sul multi-regione `eu` (trascrizione di un OGG reale);

> Nota post-deploy: modificando il binding dalla console, `roles/datastore.user` è stato sostituito da `speech.client` e il bot ha smesso di rispondere (500, `pymongo` code 13) finché non è stato ripristinato con la condizione `family-bot-db-only`. Dettagli nel log e in `docs/gcp-inventory.md`; lo smoke test non l'ha visto perché `/readyz` non prova il database (candidato M9).

- [x] deploy della revisione `v0.9.0` (canary, smoke test, spostamento del traffico);
- [x] prova reale dal telefono (2026-11-24): vocale → trascrizione → proposta → conferma → task corretto.
Controllo di routine, fuori dalla chiusura: dopo una settimana, minuti consumati e spesa sotto il budget.

## Fonte

Decisioni del primer §3, §9, §10 e §11; configurazione manuale in `docs/gcp-setup.md` (fase E4); stato delle risorse in `docs/gcp-inventory.md`.
