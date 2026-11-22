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

## Decisioni da prendere

- Forma della UI: pagine server-rendered dal servizio `api`, oppure SPA separata. Il primer ammette entrambe (§3).
- Fornitore dell'identità: Firebase Auth o OIDC Google diretto; la procedura manuale è in `{{repo:family-bot}}/docs/gcp-setup.md` §E3.
- Quali viste compongono il «minimo» della milestone.
- Dove vive lo schema OpenAPI e come resta allineato ai modelli.

## Test

- Verifica del token (firma, `aud`, `iss`, scadenza) e mapping email → `members`, con un identity provider finto.
- HTTP: 403 per email assente e per ruolo insufficiente sul log dell'agente; 200 per un membro.
- Contract test delle risposte contro l'OpenAPI.

## Ordine dei lavori

| # | Cosa | Esito |
|---|---|---|
| W1 | Verifica ID token e mapping su `members` | da fare |
| W2 | Endpoint GET e schema OpenAPI | da fare |
| W3 | Viste minime | da fare |
| W4 | Deploy `v0.8.0`, canary e smoke test | da fare |

## Collegamenti

- Milestone precedente: [[progetto/m4-7-eventi-ricorrenti]]; debito shadow che resta aperto: [[progetto/m4-5-routing-locale]].
- Auth, ruoli e dati verso l'esterno: [[architettura/sicurezza]]; service account e Cloud Run: [[infrastruttura/identita-e-runtime]].
- Stato di tutte le milestone: [[progetto/roadmap]].
