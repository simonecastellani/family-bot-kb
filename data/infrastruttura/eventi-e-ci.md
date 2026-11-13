---
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
status: active
timestamp: 2026-11-13
title: Eventi e CI/CD su GCP
type: Pagina
---
# Eventi e CI/CD su GCP

Come family-bot mette in fila il lavoro differito (Cloud Tasks), quello periodico (Cloud Scheduler) e come GitHub Actions si autentica su Google Cloud senza chiavi JSON (Workload Identity Federation). Configurazione manuale, fatta da `gcloud` una volta per progetto.

Fonte: [[sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]] (fase D e E1). Per il flusso applicativo dei promemoria vedi [[architettura/promemoria]]; per le scelte di stack [[progetto/stack]].

## Cloud Tasks

La coda dei promemoria si crea una volta, regionale:

```bash
gcloud tasks queues create reminders \
  --location=europe-west8 \
  --max-dispatches-per-second=5 \
  --max-concurrent-dispatches=5 \
  --max-attempts=5 \
  --min-backoff=10s \
  --max-backoff=300s
```

- La coda è **regionale** (`europe-west8`).
- Il rate limit (5 dispatch/s, 5 concorrenti) protegge Telegram e il `worker` da ondate di task.
- I retry sono configurabili (5 tentativi, backoff 10s–300s): il `worker` deve tollerarli.
- L'`api` che accoda ha bisogno di `roles/cloudtasks.enqueuer`.
- Per creare task con **OIDC** verso il `worker`, l'`api` deve avere anche `roles/iam.serviceAccountUser` sul service account invoker.

### Dedupe e nomi dei task

Cloud Tasks ricorda i nomi dei task fino a **24 ore dopo la cancellazione**: riusare lo stesso nome dentro quella finestra fa fallire la creazione.

- L'idempotenza vera resta `_id = dedupe_key` nella collection `reminders`, non il nome del task.
- Per uno **spostamento** non riusare lo stesso nome: usare un nome versionato oppure attendere la finestra.
- Il `worker` rilegge lo stato attuale dell'occorrenza prima di inviare, quindi un vecchio task rimasto in coda è innocuo.

## Cloud Scheduler

Cloud Scheduler ospita solo i job **periodici** (digest, sincronizzazione periodica, materializzazione delle ricorrenze, retention); i promemoria puntuali non passano di qui (la divisione è in [[architettura/promemoria]]).

```bash
gcloud scheduler jobs create http family-bot-digest \
  --location=europe-west8 \
  --schedule="0 7 * * *" \
  --time-zone="Europe/Rome" \
  --uri="https://WORKER_URL/internal/digest" \
  --http-method=POST \
  --oidc-service-account-email=family-bot-scheduler-invoker@PROJECT_ID.iam.gserviceaccount.com \
  --oidc-token-audience="https://WORKER_URL"
```

- Il token OIDC deve avere come **audience** l'URL del worker, altrimenti la chiamata prende 401/403.
- Il service account scheduler (`family-bot-scheduler-invoker`) deve avere `roles/run.invoker` sul `worker`.
- **3 job al mese sono gratuiti** per billing account: usarli solo per ciò che è davvero periodico.
- Niente Scheduler per promemoria puntuali: quelli vanno su Cloud Tasks.

## Workload Identity Federation

Il deploy da GitHub Actions non usa **chiavi JSON**: un pool e un provider OIDC federano l'identità del repository su un service account di deploy.

```bash
gcloud iam workload-identity-pools create github \
  --location=global \
  --display-name="GitHub Actions"

gcloud iam workload-identity-pools providers create-oidc github-oidc \
  --location=global \
  --workload-identity-pool=github \
  --display-name="GitHub OIDC" \
  --issuer-uri="https://token.actions.githubusercontent.com" \
  --attribute-mapping="google.subject=assertion.sub,attribute.repository=assertion.repository,attribute.ref=assertion.ref" \
  --attribute-condition="assertion.repository == 'OWNER/REPO'"
```

Autorizzazione del repository a impersonare il service account di deploy:

```bash
gcloud iam service-accounts add-iam-policy-binding \
  github-deployer@PROJECT_ID.iam.gserviceaccount.com \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/PROJECT_NUMBER/locations/global/workloadIdentityPools/github/attribute.repository/OWNER/REPO"
```

- Pool `github` e provider `github-oidc`, entrambi `global`.
- Issuer: `https://token.actions.githubusercontent.com`; gli attributi mappati sono `google.subject`, `attribute.repository`, `attribute.ref`.
- La `attribute-condition` limita la federazione al repository `OWNER/REPO`.
- Nel workflow si usa `google-github-actions/auth@v2` con `workload_identity_provider` e `service_account` (il deployer).
- In produzione va aggiunta una condizione sul branch, `assertion.ref == 'refs/heads/main'`, così solo i push su `main` possono federare.

## Punti aperti

- [ ] Verificare se la regola del nome versionato dopo uno spostamento convive con il nome deterministico del task descritto in [[architettura/promemoria]], e quale delle due adottare nel codice.
