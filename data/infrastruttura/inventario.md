---
provenance: [sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]
status: active
timestamp: 2026-11-13
title: Inventario delle risorse GCP
type: Pagina
---
# Inventario delle risorse GCP

Stato reale delle risorse dei progetti Family Bot su Google Cloud, aggiornato al 2026-11-14 e trascritto dal file `docs/gcp-inventory.md` del repository ([[sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]]). Senza Terraform l'inventario è la **fonte di verità**: va aggiornato a ogni modifica con nomi stabili, così le risorse potranno essere importate in Terraform senza ricrearle. La procedura che lo produce è in [[infrastruttura/gcp-bootstrap]].

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

`local-admin` non è ancora stato creato: serve in **M2**, quando la suite contract viene eseguita anche sul database Enterprise reale, con `roles/datastore.owner` condizionato al database oppure un custom role con `datastore.schemas.*` (`createIndex()`, `drop()`). In M1 gli indici sono stati applicati **solo** sul MongoDB locale; il runtime non usa questo utente. I permessi dati e schema sono descritti in [[infrastruttura/gcp-bootstrap]].

## Service account

| Nome | Email | Ruoli | Uso | Stato |
|---|---|---|---|---|
| `family-bot-api` | `family-bot-api@family-bot-dev.iam.gserviceaccount.com` | `roles/datastore.user` (condizionato al DB), `roles/cloudtasks.enqueuer`, `roles/storage.objectAdmin` (binding condizionato, da correggere), `secretAccessor` per singolo secret | Runtime API | creato 2026-11-14 |
| `family-bot-worker` | `family-bot-worker@family-bot-dev.iam.gserviceaccount.com` | `roles/datastore.user` (senza condizione), `secretAccessor` per singolo secret | Runtime worker | creato 2026-11-14 |
| `family-bot-tasks-invoker` | `family-bot-tasks-invoker@family-bot-dev.iam.gserviceaccount.com` | `roles/run.invoker` (progetto) | OIDC Cloud Tasks | creato 2026-11-14 |
| `family-bot-scheduler-invoker` | `family-bot-scheduler-invoker@family-bot-dev.iam.gserviceaccount.com` | `roles/run.invoker` (progetto) | OIDC Cloud Scheduler | creato 2026-11-14 |
| `github-deployer` | `github-deployer@family-bot-dev.iam.gserviceaccount.com` | `roles/run.admin`, `roles/artifactregistry.writer`, `roles/iam.serviceAccountUser` | CI/CD | creato 2026-11-14 |

Debiti IAM aperti (dettaglio nel repository, `docs/gcp-inventory.md`): il binding `storage.objectAdmin` sul progetto ha una condizione `resource.name == "family-bot-documents"` che non corrisponde a nessuna risorsa Cloud Storage, quindi non concede nulla; manca `iam.serviceAccountUser` di `family-bot-api` sul `tasks-invoker`; `datastore.user` del worker non è condizionato al database; `run.invoker` è a livello progetto.

Il disegno dei ruoli e dei runtime è in [[infrastruttura/identita-e-runtime]]; qui si registra solo lo stato effettivo.

## Secret Manager

Tre secret globali con replica `user-managed` in `europe-west8`, creati il 2026-11-14: `telegram-bot-token` (nato regional secret e poi migrato), `telegram-webhook-secret`, `opencode-api-key`. Accesso concesso per singolo secret a `family-bot-api` e `family-bot-worker`; nessun binding a livello progetto.

## Cloud Storage

`gs://family-bot-documents`, regional `europe-west8`, classe `STANDARD`, uniform bucket-level access, public access prevention `enforced`, soft delete 7 giorni, senza versioning né lifecycle. Creato il 2026-11-14. Nessun free tier: i 5 GiB Always Free valgono solo nei bucket statunitensi.

## Artifact Registry

Repository Docker `family-bot` in `europe-west8`, creato il 2026-11-14; cleanup policy non ancora configurata. Immagine prevista: `europe-west8-docker.pkg.dev/family-bot-dev/family-bot/api:TAG`.

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
