---
title: Sicurezza
type: Concept
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
status: active
---
# Sicurezza

Regole di autenticazione, identità e trattamento dei dati del servizio family-bot, da §10 del primer e dai vincoli di §5 su Actor e audit.

## Autenticazione

Tre superfici distinte, ciascuna con la propria regola.

### Dashboard (`GET /api/v1/...`)

- Espone panoramica settimanale, task e occorrenze, menu, agent runs, reminder log; OpenAPI è il contratto.
- Login Google → ID token → verifica lato backend di firma, `aud` e `iss` → mapping email → `members`.
- Email non presente in `members` → **403**.
- Log dell'agente visibili solo a `admin`.
- Fuori scope: qualsiasi route di scrittura sul web.

### Webhook Telegram

- Verifica dell'header con secret token.
- Whitelist tramite `telegram_user_id`.

### Endpoint interni (Cloud Tasks / Scheduler)

- Non sono pubblici: verificano il token OIDC del service account chiamante.
- Su Cloud Run solo `api` espone gli endpoint pubblici necessari; `worker` e gli handler interni sono privati, con IAM invoker = service account di Cloud Tasks/Scheduler.

## Login della dashboard

Configurazione del provider di identità per la dashboard (fase M5). La regola di autenticazione lato backend è in *Dashboard* qui sopra: qui c'è solo cosa si configura.

- Si crea un **progetto Firebase collegato al progetto GCP**, oppure si usa **Identity Platform**.
- Si abilita il provider **Google** in *Authentication*.
- Si aggiungono i **domini autorizzati** della dashboard.
- Il backend verifica l'ID token (firma, `aud`, `iss`, scadenza) e mappa l'email su `members`: dettaglio e 403 restano in *Dashboard*.
- La configurazione Firebase lato frontend è **pubblica** e **non è un segreto**: non servono chiavi di service account.

## Identità e privilegi

- Service account dedicati con privilegi minimi; nessuna chiave JSON.
- CI autenticata con Workload Identity Federation.
- L'identità del chiamante viaggia con la richiesta fino ai servizi sotto forma di `Actor` (vedi Actor e audit).

## Segreti

- Segreti solo in Secret Manager; niente segreti nel repository.
- `.env.example` è il modello per lo sviluppo locale.
- Nessun segreto in configurazione, log o output.

## Dati verso il provider LLM

- Testo, trascrizioni e contenuto del calendario escono verso il provider LLM: minimizzare ciò che si invia.
- Scegliere regioni UE dove supportate dalle quote gratuite (da verificare).
- Output sempre escapato; nessun HTML generato dall'LLM è reso senza sanitizzazione.

## Actor e audit

- Ogni servizio riceve un `Actor(member_id, source)` con `source ∈ {telegram, web, agent, scheduler}`: l'audit sa chi ha agito e da quale canale.
- Ogni scrittura produce un `audit_events` (actor, source, azione, entità, diff).

## Punti aperti

- [ ] Verificare quali regioni UE sono coperte dalle quote gratuite del provider LLM, prima di fissare la region dei dati in uscita.

## Fonte

Distillato da [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].
