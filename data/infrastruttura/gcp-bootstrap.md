---
type: Runbook
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud, sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]
status: active
timestamp: 2026-11-13
title: "Bootstrap GCP — progetto, billing, API, Firestore"
---
# Bootstrap GCP — progetto, billing, API, Firestore

Fase A (fondamenta, M0/M1) della configurazione manuale di Google Cloud: account, progetto, budget, `gcloud`, API e database Firestore Enterprise con compatibilità MongoDB. Fonte: [[sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]].

## Prerequisiti

- Un account Google con un **billing account attivo**: anche i servizi gratuiti richiedono il billing abilitato.
- Con un account Gmail personale il progetto vive in "No organization": funziona ugualmente. Folder e Organization esistono solo con Google Workspace.
- Ruoli necessari per creare il progetto: `roles/resourcemanager.projectCreator` sulla folder/organization e `roles/billing.user` sul billing account.
- Il progetto è il **confine** di API, quote, IAM e billing: `family-bot-dev` per sperimentare, `family-bot-prod` solo al deploy reale.
- I service agent creati abilitando un'API (`service-<PROJECT_NUMBER>@...`) non vanno cancellati.

## Progetto e billing

Creazione da console: **IAM & Admin → Create Project**, nome `Family Bot Dev`, Project ID globalmente unico (es. `family-bot-dev-<iniziali>`).

Da CLI:

```bash
export PROJECT_ID="family-bot-dev-xxxx"
export REGION="europe-west8"

gcloud projects create "$PROJECT_ID" --name="Family Bot Dev"
gcloud config set project "$PROJECT_ID"

gcloud billing accounts list
gcloud billing projects link "$PROJECT_ID" \
  --billing-account=XXXXXX-XXXXXX-XXXXXX

gcloud projects describe "$PROJECT_ID" \
  --format='yaml(projectId,projectNumber,lifecycleState)'
```

Con folder: `gcloud projects create "$PROJECT_ID" --name="Family Bot Dev" --folder=FOLDER_ID`.

**Budget alert dal primo giorno** (Billing → Budgets & alerts → Create budget): scope sul progetto o sull'intero billing account, importo di esempio 10 EUR al mese, soglie 50%, 90%, 100% (opzionale 120%), alert email ai billing admin. Per altri destinatari: canale email in Cloud Monitoring collegato al budget, oppure ruolo billing al destinatario.

Il budget **avvisa e non blocca** la spesa; i dati di billing arrivano in ritardo; su Cloud Run il vero controllo di costo è `max-instances`. Controllo periodico in Billing → Reports, filtrato per progetto e servizio.

**Installazione e configurazione di `gcloud`** (Debian/Ubuntu):

```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-linux-x86_64.tar.gz
tar -xf google-cloud-cli-linux-x86_64.tar.gz
./google-cloud-sdk/install.sh
exec -l "$SHELL"

gcloud init
gcloud auth login
gcloud auth application-default login

gcloud config configurations create family-bot-dev
gcloud config set project "$PROJECT_ID"
gcloud config set compute/region "$REGION"
gcloud config set run/region "$REGION"
```

`gcloud auth application-default login` crea credenziali utente locali per le librerie Google: **non** è una chiave JSON di service account. In alternativa Cloud Shell dal browser ha già `gcloud` e l'autenticazione.

Regione di riferimento: `europe-west8` (Milano). La location di Firestore è permanente; il free tier di Firestore è per progetto, quello di Cloud Run per billing account.

## Ambiente di produzione

Il progetto di produzione `family-bot-prod-514113` (project number `98566746466`) esiste già, con il budget `family-bot-prod` (soglie 50%, 80%, 100%), ma **non è usato in M0/M1 e non va toccato** finché non si arriva al deploy reale: sviluppo e sperimentazione vivono su `family-bot-dev`. Lo stato effettivo delle risorse, comprese quelle del progetto di produzione, è in [[infrastruttura/inventario]].

## API

Abilitare solo il necessario:

```bash
gcloud services enable \
  cloudresourcemanager.googleapis.com \
  serviceusage.googleapis.com \
  firestore.googleapis.com \
  run.googleapis.com \
  secretmanager.googleapis.com \
  cloudtasks.googleapis.com \
  cloudscheduler.googleapis.com \
  artifactregistry.googleapis.com \
  storage.googleapis.com \
  iamcredentials.googleapis.com \
  monitoring.googleapis.com \
  logging.googleapis.com \
  billingbudgets.googleapis.com \
  cloudbilling.googleapis.com

gcloud services list --enabled --format='value(config.name)' | sort
```

- `calendar-json.googleapis.com` si abilita in M4.
- Abilitare tutto "per comodità" aumenta superficie d'attacco e rischio costi.
- Dopo l'abilitazione possono volerci alcuni minuti prima che le API rispondano.
- `iamcredentials.googleapis.com` serve per impersonation e Workload Identity Federation.
- `billingbudgets.googleapis.com` serve a gestire i budget via API; la console funziona comunque.

## Firestore

**Creazione** (Console → Firestore → Create a Firestore Database):

- Database ID: `family-bot` (solo lettere minuscole, numeri e trattini; 4–63 caratteri).
- Edition: **Enterprise Edition**; se richiesto, modalità **MongoDB compatibility**.
- Location: `europe-west8` (Milano); attendere l'inizializzazione.

Regole:

- La **location è permanente**: non si cambia dopo la creazione.
- Il free tier vale per **un solo database per progetto**: non creare database "usa e getta" nello stesso progetto.
- TTL, backup, restore e point-in-time recovery **non sono gratuiti**: non farci affidamento per la retention.
- La compatibilità MongoDB supporta MongoDB **8.0**; per i test locali si usa `mongo:8.0`.
- Il limite documento documentato per la compatibilità MongoDB è **16 MiB**, mentre il primer adotta **4 MiB** per prudenza: discrepanza da riconciliare in M1, nel dubbio si resta sotto 4 MiB.

**Identificatori**: il database ha un *Database ID* (`family-bot`) e un *UID* (UUID generato da Google, usato nell'hostname di connessione).

```bash
gcloud firestore databases describe \
  --database=family-bot \
  --format='yaml(locationId,uid)'
```

**Utente SCRAM per lo sviluppo locale** — `gcloud firestore user-creds create local-dev --database=family-bot` restituisce la password **una sola volta** (va nel `.env` locale, ignorato da git, o in Secret Manager). L'utente è un principal IAM dedicato al database:

```bash
export PROJECT_NUMBER=$(gcloud projects describe "$PROJECT_ID" --format='value(projectNumber)')

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="principal://firestore.googleapis.com/projects/${PROJECT_NUMBER}/name/databases/family-bot/userCreds/local-dev" \
  --role="roles/datastore.user" \
  --condition="expression=resource.name == \"projects/${PROJECT_ID}/databases/family-bot\",title=family-bot-db-only"
```

Connection string locale (con `UID` segnaposto da sostituire sempre con il valore reale, altrimenti il frontend chiude la connessione):

```text
mongodb://local-dev:PASSWORD@UID.europe-west8.firestore.goog:443/family-bot?loadBalanced=true&authMechanism=SCRAM-SHA-256&tls=true&retryWrites=false
```

I flag non sono opzionali: `loadBalanced=true` (endpoint gestito, non un replica set), `tls=true`, `retryWrites=false` (retryable writes non supportate), porta `443` e non `27017`. Se la password contiene caratteri speciali va URL-encodata oppure tenuta separata dalla connection string.

Verifica con `mongosh`: `db.pages.insertOne(...)`, `db.pages.find({})`, `db.pages.deleteMany({})`.

**Permessi dati vs permessi schema**: `roles/datastore.user` copre `find`/`aggregate`/`distinct`/`insert`/`update`/`delete`, ma **non** `drop()` (`datastore.schemas.delete`), `createIndex()` (`datastore.schemas.create`) né `dropDatabase()` (`datastore.databases.delete`). L'account Google Owner del progetto non trasferisce i suoi permessi all'utente SCRAM: sono principal distinti. Per cleanup e migrazioni locali serve un principal amministrativo separato, `local-admin` con `roles/datastore.owner` e la stessa condizione sul database; l'app e i test CRUD restano su `local-dev`. Il service account di runtime non deve mai avere `roles/datastore.owner`.

**`ensure_indexes()` (M1)**: crea indici, quindi richiede `datastore.schemas.create`. Strategia da decidere: un principal di migrazione con custom role limitato a `datastore.schemas.create`, `get` e `list`, oppure `roles/datastore.indexAdmin` solo in CI/dev (include anche `datastore.schemas.delete`), mai `roles/datastore.owner` sul service account che serve le richieste.

**Connessione da Cloud Run senza password (OIDC)**, strada consigliata in cloud: service account del servizio (es. `family-bot-api`) con `roles/datastore.user`, meglio con condizione sul database, e connection string

```text
mongodb://UID.europe-west8.firestore.goog:443/family-bot?loadBalanced=true&tls=true&retryWrites=false&authMechanism=MONGODB-OIDC&authMechanismProperties=ENVIRONMENT:gcp,TOKEN_RESOURCE:FIRESTORE
```

Cloud Run fornisce le credenziali del service account via metadata server: nessuna password statica e nessuna chiave JSON. PyMongo supporta `MONGODB-OIDC` dalla 4.7. Da `mongosh` locale **non** usare `ENVIRONMENT:gcp` (non c'è metadata server): per la diagnostica usare SCRAM oppure un access token temporaneo con `--username access_token --password "$(gcloud auth print-access-token)"` e `authMechanism=PLAIN&authSource=$external`.

**Free tier**: 50.000 read unit e 40.000 write unit al giorno, 1 GiB di archiviazione, 10 GiB/mese di egress; le unità non coincidono con i documenti (query, indici e dimensioni contano), il conteggio si resetta a mezzanotte **Pacific time** e oltre il free tier la spesa è automatica: il budget alert è l'unica protezione.

## Verifica

- `gcloud projects describe "$PROJECT_ID" --format='yaml(projectId,projectNumber,lifecycleState)'` e `gcloud billing projects link` confermano progetto e billing collegato.
- `gcloud services list --enabled --format='value(config.name)' | sort` mostra solo le API previste.
- `gcloud firestore databases describe --database=family-bot --format='yaml(locationId,uid)'` restituisce `europe-west8` e l'UID reale.
- `mongosh` con l'utente `local-dev`: insert, find e `deleteMany({})` funzionano; `drop()` fallisce con `missing or insufficient permissions` (comportamento atteso).
- **Inventario manuale** (A6): senza Terraform l'inventario del repository è la fonte di verità e va aggiornato a ogni modifica con Project ID e project number, billing account, regione/location, database Firestore (ID, UID, location, edizione), service account e ruoli, secret e versioni, bucket, queue e scheduler job, servizi Cloud Run e URL, pool/provider WIF, comandi eseguiti e date. Quei nomi stabili permetteranno di importare le risorse in Terraform senza ricrearle. Vedi [[sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp]].
- Le scelte di stack che questa fase presuppone sono in [[progetto/stack]].
