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
| `local-admin` | `principal://firestore.googleapis.com/projects/602013947946/name/databases/family-bot/userCreds/local-admin` | `roles/datastore.owner` (condizionato al DB) | Contract test sul database Enterprise reale, `createIndex()`, `drop()`, migrazioni |

Entrambi hanno la condizione `family-bot-db-only` (`resource.name == "projects/family-bot-dev/databases/family-bot"`). `local-admin` è il principal di migrazione: il runtime non lo usa, e la sua password è stata mostrata una sola volta e vive solo in `.env`. I permessi dati e schema sono descritti in [[infrastruttura/gcp-bootstrap]].

## Service account

| Nome | Email | Ruoli | Uso | Stato |
|---|---|---|---|---|
| `family-bot-api` | `family-bot-api@family-bot-dev.iam.gserviceaccount.com` | `roles/datastore.user` (condizionato al DB), `roles/cloudtasks.enqueuer`, `roles/storage.objectAdmin` (binding condizionato, da correggere), `secretAccessor` per singolo secret | Runtime API | creato 2026-11-14 |
| `family-bot-worker` | `family-bot-worker@family-bot-dev.iam.gserviceaccount.com` | `roles/datastore.user` (senza condizione), `secretAccessor` per singolo secret | Runtime worker | creato 2026-11-14 |
| `family-bot-tasks-invoker` | `family-bot-tasks-invoker@family-bot-dev.iam.gserviceaccount.com` | `roles/run.invoker` (progetto) | OIDC Cloud Tasks | creato 2026-11-14 |
| `family-bot-scheduler-invoker` | `family-bot-scheduler-invoker@family-bot-dev.iam.gserviceaccount.com` | `roles/run.invoker` (progetto) | OIDC Cloud Scheduler | creato 2026-11-14 |
| `github-deployer` | `github-deployer@family-bot-dev.iam.gserviceaccount.com` | `roles/run.admin`, `roles/artifactregistry.writer`, `roles/iam.serviceAccountUser` | CI/CD | creato 2026-11-14 |

Debiti IAM aperti (dettaglio nel repository, `docs/gcp-inventory.md`): resta solo `run.invoker` concesso a livello progetto, da stringere al servizio `family-bot-worker` subito dopo il deploy (prima concedere sul servizio, poi togliere dal progetto).

Rischi accettati: `iam.disableServiceAccountKeyCreation` non è imposto (decisione del 2026-11-15), quindi il controllo è manuale con `gcloud iam service-accounts keys list`; il service account di default Compute conserva `roles/editor` ma non va usato come runtime.

Chiusi il 2026-11-15: `storage.objectAdmin` spostato dal progetto al bucket (la condizione `resource.name` sul nome nudo non corrispondeva a nessuna risorsa Cloud Storage); `iam.serviceAccountUser` concesso a `family-bot-api` sul solo `tasks-invoker`; `datastore.user` del worker condizionato al database; worker senza accesso a `opencode-api-key`.

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
- `speech.googleapis.com` (M6), `vision.googleapis.com` (M7.2), `documentai.googleapis.com` (M8.1)

Altre API risultano abilitate, probabilmente come default del progetto o abilitate dai servizi Google: `analyticshub`, `apptopology`, `backupdr`, `bigquery*`, `cloudapis`, `cloudtrace`, `compute`, `containerregistry`, `dataform`, `dataplex`, `datastore`, `firebaserules`, `oslogin`, `pubsub`, `servicemanagement`, `servicenetworking`, `sql-component`, `storage-api`, `storage-component`, `telemetry`. Non sono state abilitate a mano; se non servono, valutarne la disabilitazione in M9. `calendar-json.googleapis.com` arriva in M4.

## Document AI e documenti (M8.1, 2026-11-26)

- `documentai.googleapis.com` abilitata; processor `family-bot-menu` (`OCR_PROCESSOR`) in `eu`, risorsa `projects/602013947946/locations/eu/processors/11870ab02f58d539`.
- `roles/documentai.apiUser` al solo `family-bot-worker`; `roles/storage.objectAdmin` sul bucket `family-bot-documents` al worker (l'API ce l'aveva già); `opencode-api-key` di nuovo accessibile al worker, che ora estrae il menù.
- Il file di un documento vive sul bucket solo durante la lettura e viene cancellato dal worker: nessuna policy di lifecycle, la cancellazione è esplicita.
- Dettagli e verifica con token impersonato in [[progetto/m8-1-documenti]].

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
| 2026-11-14 | Creati i 5 service account e i ruoli runtime (Fase B1) | `gcloud iam service-accounts create`, `gcloud projects add-iam-policy-binding` |
| 2026-11-14 | `telegram-bot-token` creato come regional secret e poi migrato a secret globale con replica `europe-west8` | Console, `gcloud secrets create --replication-policy=user-managed` |
| 2026-11-14 | Creati `telegram-webhook-secret` e `opencode-api-key`; accesso per singolo secret, rimossi i binding project-level | `gcloud secrets create`, `gcloud secrets add-iam-policy-binding`, `gcloud projects remove-iam-policy-binding` |
| 2026-11-14 | Creato bucket `family-bot-documents` e repository Artifact Registry `family-bot` | `gcloud storage buckets create`, `gcloud artifacts repositories create` |
| 2026-11-15 | Verifica read-only della Fase B e documentazione dei debiti IAM | `gcloud ... describe/list`, endpoint regionale Secret Manager |
| 2026-11-15 | `storage.objectAdmin` spostato dal progetto al bucket `family-bot-documents` | `gcloud projects remove-iam-policy-binding --all`, `gcloud storage buckets add-iam-policy-binding` |
| 2026-11-15 | `iam.serviceAccountUser` concesso a `family-bot-api` solo sul `tasks-invoker` | Console → Service Accounts → `tasks-invoker` → Permissions |
| 2026-11-15 | `datastore.user` del worker condizionato al database; worker rimosso da `opencode-api-key` | Console → IAM e Secret Manager → Permissions |
| 2026-11-15 | Creato l'utente `local-admin` con `datastore.owner` condizionato al database | Firestore → Databases → `family-bot` → Security → Add User |
| 2026-11-15 | Deciso di non imporre `iam.disableServiceAccountKeyCreation`: rischio accettato con controllo manuale | — |
| 2026-11-26 | Abilitata `documentai.googleapis.com` e creato il processor OCR `family-bot-menu` in `eu` | `gcloud services enable`, REST `POST /v1/projects/.../locations/eu/processors` |
| 2026-11-26 | Worker: `documentai.apiUser`, `storage.objectAdmin` sul bucket e `secretAccessor` su `opencode-api-key` | `gcloud projects/storage buckets/secrets add-iam-policy-binding` |
