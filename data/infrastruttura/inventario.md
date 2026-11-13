---
provenance: [sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]
status: active
timestamp: 2026-11-13
title: Inventario delle risorse GCP
type: Pagina
---
# Inventario delle risorse GCP

Stato reale delle risorse dei progetti Family Bot su Google Cloud, aggiornato al 2026-11-09 e trascritto dal file `docs/gcp-inventory.md` del repository ([[sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]]). Senza Terraform l'inventario è la **fonte di verità**: va aggiornato a ogni modifica con nomi stabili, così le risorse potranno essere importate in Terraform senza ricrearle. La procedura che lo produce è in [[infrastruttura/gcp-bootstrap]].

Email dell'account proprietario e identificativo del billing account sono redatti come `<owner-email>` e `<billing-account>`: i valori reali stanno nel file sorgente nel repository ({{repo:family-bot}}/docs/gcp-inventory.md), mai in questa pagina.

## Progetto e ambienti

| Campo | Valore |
|---|---|
| Nome | Family Bot Dev |
| Project ID | `family-bot-dev` |
| Project number | `602013947946` |
| Billing account | `<billing-account>` |
| Account proprietario | `<owner-email>` |
| Regione principale | `europe-west8` |
| Organizzazione / folder | No organization |

L'ambiente di **produzione** è il progetto `family-bot-prod-514113` (project number `98566746466`), con il budget `family-bot-prod`. Non è usato in M0/M1 e **non va toccato** finché non si arriva al deploy reale; sviluppo e sperimentazione vivono su `family-bot-dev`.

## Budget

| Nome | Scope | Importo | Soglie | Crediti |
|---|---|---|---|---|
| `family-bot-dev` | progetto `family-bot-dev` | 10 EUR/mese | 50%, 80%, 100% | esclusi |
| `family-bot-prod` | progetto `family-bot-prod-514113` | — | 50%, 80%, 100% | esclusi |

Il budget avvisa e non blocca la spesa; i dati di billing arrivano in ritardo.

Le soglie configurate sono **50/80/100** e sono il valore canonico. Il runbook in `docs/gcp-setup.md` (sezione A2) indicava 50/90/100: il documento va allineato al valore reale, non il contrario.

## Firestore

| Campo | Valore |
|---|---|
| Database ID | `family-bot` |
| Database UID | `63ea0fad-3b37-4915-9eff-b4b3fd292589` |
| Location | `europe-west8` |
| Edition | Enterprise |
| Compatibilità MongoDB | abilitata |
| Free tier | sì |
| Point-in-time recovery | disabilitato |
| Delete protection | disabilitata |
| Creato il | 2026-11-08 |

### Utenti database

| Username | Principal IAM | Ruolo | Uso |
|---|---|---|---|
| `local-dev` | `principal://firestore.googleapis.com/projects/602013947946/name/databases/family-bot/userCreds/local-dev` | `roles/datastore.user` (condizionato al DB) | Sviluppo locale, CRUD dati |

`local-admin` non è ancora stato creato: serve in M1 per `ensure_indexes()` e per i contract test sul database reale, con `roles/datastore.owner` condizionato al database oppure un custom role con `datastore.schemas.*`. Non va usato per l'app. I permessi dati e schema sono descritti in [[infrastruttura/gcp-bootstrap]].

## Service account

| Nome | Email | Ruoli | Uso | Stato |
|---|---|---|---|---|
| `family-bot-api` | `family-bot-api@family-bot-dev.iam.gserviceaccount.com` | `roles/datastore.user` (condizionato al DB) | Runtime API | creato |
| `family-bot-worker` | — | — | Runtime worker | da creare (M2) |
| `family-bot-tasks-invoker` | — | — | OIDC Cloud Tasks | da creare (M3) |
| `family-bot-scheduler-invoker` | — | — | OIDC Cloud Scheduler | da creare (M3) |
| `github-deployer` | — | — | CI/CD con WIF | da creare (M2) |

Il disegno dei ruoli e dei runtime è in [[infrastruttura/identita-e-runtime]]; qui si registra solo lo stato effettivo.

## API abilitate

Abilitate a mano il 2026-11-08 (Fase A):

- `cloudresourcemanager.googleapis.com`
- `serviceusage.googleapis.com`
- `firestore.googleapis.com`
- `run.googleapis.com`
- `secretmanager.googleapis.com`
- `cloudtasks.googleapis.com`
- `cloudscheduler.googleapis.com`
- `artifactregistry.googleapis.com`
- `storage.googleapis.com`
- `iamcredentials.googleapis.com`
- `monitoring.googleapis.com`
- `logging.googleapis.com`
- `billingbudgets.googleapis.com`
- `cloudbilling.googleapis.com`

Altre API risultano abilitate, probabilmente come default del progetto o abilitate dai servizi Google: `analyticshub`, `apptopology`, `backupdr`, `bigquery*`, `cloudapis`, `cloudtrace`, `compute`, `containerregistry`, `dataform`, `dataplex`, `datastore`, `firebaserules`, `oslogin`, `pubsub`, `servicemanagement`, `servicenetworking`, `sql-component`, `storage-api`, `storage-component`, `telemetry`. Non sono state abilitate a mano; se non servono, valutarne la disabilitazione in M9. `calendar-json.googleapis.com` arriva in M4.

## Registro modifiche

| Data | Modifica | Comando / percorso console |
|---|---|---|
| 2026-11-08 | Creato progetto `family-bot-dev` e collegato il billing account | `gcloud projects create`, `gcloud billing projects link` |
| 2026-11-08 | Creato budget `family-bot-dev` (10 EUR/mese, soglie 50/80/100%) | Console → Billing → Budgets & alerts |
| 2026-11-08 | Abilitate le 14 API della Fase A | `gcloud services enable ...` |
| 2026-11-08 | Creato database Firestore Enterprise `family-bot` in `europe-west8` | Console → Firestore → Create database |
| 2026-11-08 | Creato utente SCRAM `local-dev` con `roles/datastore.user` condizionato | `gcloud firestore user-creds create`, `gcloud projects add-iam-policy-binding` |
| 2026-11-08 | Creato service account `family-bot-api` con `roles/datastore.user` condizionato | `gcloud iam service-accounts create`, `gcloud projects add-iam-policy-binding` |
| 2026-11-09 | Verifica read-only e aggiornamento inventario | `./scripts/verify_gcp.sh family-bot-dev`, `gcloud ... describe/list` |
