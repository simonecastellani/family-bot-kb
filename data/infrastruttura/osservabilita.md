---
provenance: [sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]
status: active
timestamp: 2026-11-13
title: "Osservabilità, quote e verifica su Google Cloud"
type: Concept
---
# Osservabilità, quote e verifica su Google Cloud

Log, metriche, controllo della spesa e la checklist manuale che chiude la configurazione Google Cloud di family-bot. Identità e permessi concreti stanno in [[infrastruttura/identita-e-runtime]]; progetto, billing e API in [[infrastruttura/gcp-bootstrap]].

## Osservabilità

- **Cloud Logging**: i log di Cloud Run sono raccolti automaticamente; si scrive **JSON strutturato** su `stdout`/`stderr`.
- **Cloud Monitoring**: un uptime check su `/healthz` e un alert sui 5xx, da creare quando c'è il deploy. Gli endpoint di salute esistono già (`/healthz`, `/readyz`).
- **Audit Logs**: Admin Activity è sempre attivo; Data Access è a pagamento e può generare molto volume. **Non** va abilitato globalmente: l'audit è applicativo, nella collection `audit_events` (vedi [[architettura/modello-dati]] e la regola in [[architettura/sicurezza]]).
- **Costi**: Billing Reports per servizio, budget alert, `max-instances` su Cloud Run, cleanup policy su Artifact Registry, lifecycle su Cloud Storage.

### Sicurezza operativa

L'igiene richiesta al deploy: nessuna chiave JSON, nessun bucket pubblico, nessun segreto in git, ruoli runtime minimali, service account dedicati. Le regole sono in [[architettura/sicurezza]], la loro applicazione in [[infrastruttura/identita-e-runtime]].

## Quote

- Le quote si controllano da **IAM & Admin → Quotas**, insieme alle quote di Firestore.
- Le **quote Firestore sono giornaliere** e **non fermano la spesa** oltre il free tier: superarle produce addebito, non un blocco. Il budget alert resta l'unica protezione, e il budget avvisa senza bloccare il progetto ([[infrastruttura/gcp-bootstrap]]).
- I tetti di scala di Cloud Run (`max-instances`) e la cleanup policy di Artifact Registry sono il controllo di costo lato servizio.

## Checklist di verifica

Elenco spuntabile dei controlli manuali. Lo script read-only `scripts/verify_gcp.sh` in {{repo:family-bot}} ne esegue la maggior parte.

- [ ] `gcloud config list`
- [ ] `gcloud projects describe "$PROJECT_ID" --format='yaml(projectId,projectNumber)'`
- [ ] `gcloud billing projects describe "$PROJECT_ID"`
- [ ] `gcloud services list --enabled --format='value(config.name)' | sort`
- [ ] `gcloud firestore databases list`
- [ ] `gcloud iam service-accounts list`
- [ ] `gcloud secrets list`
- [ ] `gcloud storage buckets list`
- [ ] `gcloud artifacts repositories list --location=europe-west8`
- [ ] `gcloud run services list --region=europe-west8`
- [ ] `gcloud tasks queues list --location=europe-west8`
- [ ] `gcloud scheduler jobs list --location=europe-west8`

Per M0/M1 bastano:

- [ ] progetto e billing attivi;
- [ ] budget alert attivo;
- [ ] API Firestore abilitata;
- [ ] database `family-bot` Enterprise in `europe-west8`;
- [ ] utente SCRAM locale con IAM condition;
- [ ] connessione verificata con `mongosh`;
- [ ] inventario aggiornato.

## Decisioni verificate

Le decisioni che il documento dichiara verificate; per ciascuna il posto dove vive la conseguenza.

| # | Decisione | Conseguenza |
|---|---|---|
| 1 | Firestore MongoDB compatibility supporta **MongoDB 8.0** | il compose locale usa `mongo:8.0` ([[infrastruttura/gcp-bootstrap]]) |
| 2 | **PyMongo Async è stabile** (4.19, ottobre 2026); **Motor è deprecato/EOL da maggio 2026** | si usa `AsyncMongoClient`, non Motor ([[architettura/modello-dati]]) |
| 3 | Cloud Run → Firestore: service account + `roles/datastore.user` + `MONGODB-OIDC`, senza password statiche; in locale SCRAM | [[infrastruttura/identita-e-runtime]], [[infrastruttura/gcp-bootstrap]] |
| 4 | **Cloud Tasks dedupe**: il nome resta occupato fino a **24 ore** dopo la cancellazione; per gli spostamenti serve un nome task versionato | l'idempotenza vera resta il documento `reminders` ([[architettura/promemoria]]) |
| 5 | **`europe-west8` (Milano)** è tra le location supportate da Firestore MongoDB compatibility | location permanente del database ([[infrastruttura/gcp-bootstrap]]) |
| 6 | **Dimensione documento**: la documentazione della compatibilità parla di 16 MiB, il primer indica 4 MiB | si resta sotto **4 MiB** e si riconcilia in M1 ([[architettura/modello-dati]]) |
| 7 | **Permessi Firestore**: `roles/datastore.user` copre solo i dati; `drop()`, `createIndex()` e `dropDatabase()` richiedono permessi schema | in M1 `ensure_indexes()` avrà un principal di migrazione dedicato ([[infrastruttura/gcp-bootstrap]]) |

## Fonte

Distillato da [[sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud]], sezioni *Osservabilità, quote e sicurezza operativa*, *Checklist di verifica* e *Decisioni verificate*.
