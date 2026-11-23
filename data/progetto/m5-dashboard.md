---
timestamp: 2026-11-22
title: M5 — Dashboard in sola lettura
type: Pagina
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: in-progress
---
# M5 — Dashboard in sola lettura

## Obiettivo

Portare in web, in **sola lettura**, lo stato che il bot tiene già su MongoDB: panoramica settimanale, scadenze e occorrenze, `agent_runs` e `reminder_log`. Nessuna route di scrittura: l'editing è un'evolutiva, mentre database e service layer sono pensati per una SPA futura (§3).

## Dentro

- `GET /api/v1/...` come contratto OpenAPI, sulle viste minime decise qui sotto (§10).
- Login Google con OIDC: ID token verificato lato backend (firma, `aud`, `iss`), email mappata su `members`; email non registrata → **403** (§10).
- Log dell'agente visibile **solo a `admin`** (§10).
- Le viste le serve `api`, l'unico servizio che espone endpoint pubblici; `worker` e handler interni restano privati, invocati dai service account di Cloud Tasks e Scheduler (§10).
- Rilascio `v0.8.0` (§13).

## Fuori

- Qualsiasi route di scrittura sul web (§10).
- Editing di calendario, scadenze e menu dal web: resta l'evolutiva.
- Gestione dei membri e dei ruoli dal web: la mappa email → ruolo si mantiene fuori dalla dashboard.
- Menu e lista della spesa: sono M8.

## Criteri di accettazione

- Utente non registrato → **403**; token non valido o scaduto rifiutato prima di leggere i dati.
- Il log dell'agente è leggibile solo da `admin`.
- Le viste passano dal service layer condiviso con Telegram e con i tool dell'agente, senza query duplicate nel layer HTTP (§3).

## Decisioni prese (2026-11-23)

- **UI separata dall'API**: SPA in `dashboard/` (Vite + TypeScript, nessun framework) su **Firebase Hosting**; l'API resta su Cloud Run. Due rilasci indipendenti: la UI a ogni merge su `main`, l'API solo da tag semver.
- **Identità**: Firebase Authentication (project `family-bot-dev`), provider Google. L'ID token viaggia in `Authorization: Bearer`; il backend verifica firma, `aud`, `iss` e scadenza con `google.oauth2.id_token.verify_firebase_token` e mappa l'email su `members`. Niente cookie, niente sessione, niente upgrade a Identity Platform (gratis fino a 50.000 MAU social).
- **Trasporto**: chiamata diretta cross-origin con CORS a allowlist (`DASHBOARD_ORIGINS`), non rewrite di Hosting: non si dipende dall'inoltro dell'header `Authorization` da parte del proxy.
- **Viste minime**: `/api/v1/me`, `overview` (eventi + scadenze con `task_title`), `tasks`, `occurrences`, `reminders` (a tutti i membri), `members`, `agent-runs` (solo admin). Menu e lista della spesa restano a M8.
- **Contratto**: OpenAPI generato da FastAPI; i servizi restituiscono DTO Pydantic; sotto `/api/v1` nessuna route di scrittura, imposto da un test.
- **IAM del deploy UI**: service account `family-bot-hostinger` e provider WIF dedicato al ramo `main`, così un push su `main` non può impersonare il deployer dell'API.

## Test

- Verifica del token (firma, `aud`, `iss`, scadenza) e mapping email → `members`, con un identity provider finto.
- HTTP: 403 per email assente e per ruolo insufficiente sul log dell'agente; 200 per un membro.
- Contract test delle risposte contro l'OpenAPI.

## Ordine dei lavori

| # | Cosa | Esito |
|---|---|---|
| W1 | Verifica ID token e mapping su `members` | fatto (2026-11-23) |
| W2 | Endpoint GET e schema OpenAPI | fatto (2026-11-23) |
| W3 | Viste minime (SPA su Hosting) | fatto (2026-11-23) |
| W4 | Deploy `v0.8.0`, canary e smoke test | fatto (2026-11-23) |
| W5 | Verifica dal telefono: membro, estraneo → 403, log solo admin | da fare |

## Stato (2026-11-23)

- Rilasciata `v0.8.0`: l'API in produzione riporta `0.8.0` (canary, smoke e traffico verdi) e la SPA è su `https://family-bot-dev.simonecastellani.com` con dominio e certificato attivi.
- Verifiche: 337 test `pytest` (contract inclusi), 22 test vitest, `ruff`, `pyright`, ESLint, build della SPA; smoke della UI verde (dominio 200, `/api/v1/me` 401, preflight CORS con l'origine del dominio).
- Inciampo reale al primo deploy Hosting: i ruoli `firebase.viewer` e `firebasehosting.admin` erano legati alla condizione Firestore `family-bot-db-only` e quindi inefficaci per le API Firebase; mancava anche `roles/serviceusage.serviceUsageConsumer` per usare il progetto come quota project. Risolto con binding senza condizione, documentato in `{{repo:family-bot}}/docs/gcp-setup.md` §E3.
- Resta: verifica dal telefono (W5), restrizione della chiave browser dopo il primo login (referrer + Identity Toolkit), chiusura della milestone in [[progetto/roadmap]].

## Collegamenti

- Milestone precedente: [[progetto/m4-7-eventi-ricorrenti]]; debito shadow che resta aperto: [[progetto/m4-5-routing-locale]].
- Auth, ruoli e dati verso l'esterno: [[architettura/sicurezza]]; service account e Cloud Run: [[infrastruttura/identita-e-runtime]].
