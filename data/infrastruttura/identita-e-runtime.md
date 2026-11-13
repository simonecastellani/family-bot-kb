---
status: active
timestamp: 2026-11-13
title: Identità e runtime su Google Cloud
type: Concept
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
---
# Identità e runtime su Google Cloud

Come family-bot sta su Google Cloud per la fase M2: quali identità agiscono, dove vivono segreti e file, dove vengono pubblicate le immagini e dove girano i due servizi runtime. Le regole di autenticazione e di trattamento dei dati restano in [[architettura/sicurezza]]; qui c'è la configurazione concreta.

## Service account

Il service account di default di Compute Engine ha ruoli troppo ampi: non si usa. Si creano identità dedicate, una per compito.

| Principal | Ruoli | Perché |
|---|---|---|
| `family-bot-api` | `roles/datastore.user`, `roles/cloudtasks.enqueuer`, `roles/secretmanager.secretAccessor`, `roles/storage.objectAdmin` sul bucket documenti | Legge e scrive dati, accoda task, legge segreti, gestisce i file |
| `family-bot-worker` | `roles/datastore.user`, `roles/secretmanager.secretAccessor` | Esegue i promemoria e legge il token Telegram |
| `family-bot-tasks-invoker` | `roles/run.invoker` sul worker | Cloud Tasks chiama il worker con OIDC |
| `family-bot-scheduler-invoker` | `roles/run.invoker` sul worker | Cloud Scheduler chiama il worker con OIDC |
| `github-deployer` | `roles/run.admin`, `roles/artifactregistry.writer`, `roles/iam.serviceAccountUser` | CI/CD: push dell'immagine e deploy |
| Umano admin | Owner/Editor sul progetto di sviluppo | Solo apprendimento; in produzione gruppi e least privilege |

Dettagli che contano:

- Chi crea un task con token OIDC deve avere `iam.serviceAccounts.actAs` sull'invoker: `family-bot-api` riceve `roles/iam.serviceAccountUser` **sul** `family-bot-tasks-invoker`.
- `roles/datastore.user` è data plane: basta per il CRUD sui documenti, non per `drop()`, `createIndex()` o `dropDatabase()`.
- Creare il database richiede `roles/datastore.owner`; creare utenti SCRAM richiede `roles/datastore.userCredsAdmin`.
- Le migrazioni che creano indici richiedono `datastore.schemas.create`: le esegue un principal dedicato, non il service account di runtime.
- Nessuna chiave JSON (`gcloud iam service-accounts keys create` è da evitare): in locale ADC, in CI Workload Identity Federation.

## Segreti e file

### Secret Manager

I segreti nascono con replica gestita dall'utente e locazione `europe-west8`. Previsti: `telegram-bot-token`, `telegram-webhook-secret`, `opencode-api-key` e, più avanti, le credenziali OAuth di Calendar se serviranno.

L'accesso si concede **per singolo segreto** con `roles/secretmanager.secretAccessor`, non a livello di progetto.

- La password SCRAM è un segreto: non deve finire in git, nei log o nell'output di CI.
- La rotazione si fa disabilitando le versioni vecchie.
- In locale si usa `.env`; in cloud Secret Manager.
- Per segreti multi-riga o file grandi conviene il mount come volume invece delle variabili d'ambiente.

### Cloud Storage

Bucket dei documenti (menu del nido, allegati): nome globalmente unico, locazione `europe-west8`, uniform bucket-level access e public access prevention attivi. Nessun accesso pubblico.

- `roles/storage.objectAdmin` si concede **sul bucket**, non sul progetto.
- Versioning e lifecycle solo se si vogliono conservare le versioni, valutando i costi.
- Per file grandi o sensibili si usano URL firmati a scadenza.

## Artifact Registry

Repository Docker `family-bot` in `europe-west8`, con cleanup policy per non accumulare immagini vecchie. Il client Docker si configura con `gcloud auth configure-docker europe-west8-docker.pkg.dev`.

L'immagine vive in `europe-west8-docker.pkg.dev/PROJECT_ID/family-bot/api:TAG` (e `.../worker:TAG`). Il free tier è circa 0,5 GB; per il deploy in CI `github-deployer` ha `roles/artifactregistry.writer`.

## Cloud Run

Due servizi distinti, entrambi in `europe-west8`, con scale-to-zero e tetto di istanze.

### Servizio `api`

- Immagine `family-bot/api`, runtime service account `family-bot-api`.
- `--allow-unauthenticated`: Telegram non può presentare un token Google OIDC; l'autenticazione avviene con il secret token del webhook e la whitelist.
- `min-instances=0` (scale-to-zero, adatto a un bot familiare), `max-instances=2` (tetto di costo), `cpu=1`, `memory=512Mi`, `concurrency=40`, `timeout=60`.
- Il timeout corto è voluto: il webhook deve rispondere in fretta e il lavoro lungo va su Cloud Tasks, non in background dopo la risposta.
- Le variabili sensibili passano da `--set-secrets` (`TELEGRAM_BOT_TOKEN` dal segreto omonimo), così non finiscono nella configurazione del servizio.

### Servizio `worker`

- Immagine `family-bot/worker`, runtime service account `family-bot-worker`.
- `--no-allow-unauthenticated`: solo i principal con `roles/run.invoker` possono chiamarlo; Cloud Tasks e Scheduler usano OIDC.
- Stessi limiti di scala dell'api.
- Non si usa `--ingress=internal`: Cloud Tasks non sta nella VPC, quindi si resta su `all` + IAM, valutando networking privato più avanti.
- Il worker deve essere idempotente, perché i retry possono ripetere la stessa chiamata.

### Service identity e logging

- Cloud Run ha una *service identity* gestita da Google, distinta dal runtime service account: non vanno confuse.
- I log su `stdout`/`stderr` sono raccolti automaticamente da Cloud Logging; solo le librerie che scrivono direttamente su Cloud Logging richiedono `roles/logging.logWriter`.
- Per metriche custom/OTel, in M9 si concedono `roles/monitoring.metricWriter` e `roles/cloudtrace.agent`.
- Non si loggano token, password, contenuti sensibili o testi completi dei messaggi.

## Guardrail

Con un'organizzazione Google Cloud disponibile:

- disabilitare la creazione di chiavi service account (`iam.disableServiceAccountKeyCreation`);
- imporre `storage.publicAccessPrevention`;
- valutare `iam.allowedPolicyMemberDomains` per limitare i domini ammessi;
- tenere le API abilitate al minimo.

Su un progetto personale senza organizzazione questi vincoli non sono disponibili: si supplisce con disciplina e con l'inventario delle risorse.

## Fonte

Distillato da [[sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]], fasi B e C.
