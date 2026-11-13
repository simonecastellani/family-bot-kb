---
type: IngestPlan
maps: [progetto, architettura, infrastruttura, sviluppo]
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud, sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp, sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo, sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci]
status: in-progress
timestamp: 2026-11-13
title: Ingest — documentazione family-bot
---
# Ingest — documentazione family-bot

Campagna unica che porta nella KB la documentazione del repository `family-bot` e la struttura del suo codice. La campagna è divisa in unità autonome: ogni esecutore legge una sola unità e la sua Source.

## Fonti coperte

| Source | Forma | Cosa porta |
|---|---|---|
| `sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot` | document | obiettivo, decisioni, architettura, modello dati, roadmap, questioni aperte |
| `sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud` | document | procedura manuale GCP, fasi A-E |
| `sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp` | document | risorse GCP realmente create |
| `sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo` | collection | convenzioni del repo e comandi |
| `sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci` | system | struttura dei moduli, porte e fake, test e CI |

Fuori campagna: `.env`, `.venv`, cache, `uv.lock` e `opencode.json` (contiene un token: non va nella KB). La cronologia git non è una fonte: le decisioni si prendono dai documenti.

## Map

| Map | Contenuto |
|---|---|
| `progetto` | obiettivo, use case, stack, roadmap, questioni aperte |
| `architettura` | flusso, regole, modello dati, promemoria, agente, sicurezza, integrazioni, porte |
| `infrastruttura` | bootstrap GCP, identità e runtime, eventi e CI, osservabilità, inventario |
| `sviluppo` | ambiente locale, test, qualità, convenzioni agenti |

## Proprietà per soggetto

Un solo owner per soggetto: due unità non creano mai la stessa pagina.

| Soggetto | Owner | Creata da |
|---|---|---|
| Obiettivo, use case, fuori scope | `progetto/overview` | U01 |
| Decisioni tecnologiche | `progetto/stack` | U02 |
| Milestone e stato | `progetto/roadmap` | U08 |
| Questioni aperte | `progetto/questioni-aperte` | U08 |
| Flusso, struttura, regole | `architettura/regole` | U03 |
| Porte e fake | `architettura/porte-e-fake` | U03 (scheletro), U15 (reale) |
| Modello dati | `architettura/modello-dati` | U04 |
| Promemoria | `architettura/promemoria` | U05 |
| Agente | `architettura/agente` | U05 |
| Sicurezza | `architettura/sicurezza` | U06, U12 (login) |
| Integrazioni | `architettura/integrazioni` | U12 |
| Ambiente locale e test | `sviluppo/locale-e-test` | U07, U16 |
| Qualità e CI | `sviluppo/qualita` | U07, U14, U16 |
| Convenzioni agenti | `sviluppo/convenzioni-agenti` | U08, U14 |
| Bootstrap GCP | `infrastruttura/gcp-bootstrap` | U09, U13 (produzione) |
| Identità e runtime | `infrastruttura/identita-e-runtime` | U10 |
| Eventi e CI/CD | `infrastruttura/eventi-e-ci` | U11 |
| Osservabilità | `infrastruttura/osservabilita` | U12 |
| Inventario | `infrastruttura/inventario` | U13 |

## Regole di estrazione

1. Riporta i fatti del sorgente; non dedurre, non completare, non promuovere un [Default] a [Confermato].
2. In una pagina cita un fatto con la sua sezione di origine; nei testi delle unità gli ID sono code span, non link a pagine non ancora esistenti.
3. Redazione: email dell'account proprietario e billing account diventano `<owner-email>` e `<billing-account>`; i valori reali restano nel file sorgente.
4. Mai credenziali, token, chiavi o segreti in una pagina, in un'unità, in un piano o in un `reason`.
5. Un'unità tocca solo i propri target. Un fatto di un altro soggetto diventa una riga del digest, non una pagina nuova.
6. Se due fonti si contraddicono, vale la fonte più recente e la contraddizione resta aperta finché una persona non decide.
7. I path di macchina non compaiono mai: si usa il placeholder `{{repo:family-bot}}`.

## Unità

16 unità: 10 `any`, 6 `large`, 0 `human`. Le unità che condividono un target girano una dopo l'altra, mai insieme.

| Unità | Titolo | Executor | Prerequisiti |
|---|---|---|---|
| [U01](u01-primer-overview) | Obiettivo, use case e fuori scope | `any` | unità autonoma, nessun prerequisito |
| [U02](u02-primer-stack) | Decisioni tecnologiche e stato | `any` | indipendente da U01 |
| [U03](u03-primer-regole) | Architettura e regole non negoziabili | `large` | prima di U15, che riempie `porte-e-fake` con il codice reale |
| [U04](u04-primer-modello-dati) | Modello dati documentale | `large` | indipendente; U09 e U13 possono citarla solo dopo che esiste |
| [U05](u05-primer-promemoria-agente) | Promemoria event-driven e agente | `any` | indipendente |
| [U06](u06-primer-sicurezza) | Sicurezza, auth e dati verso il LLM | `large` | prima di U12, che aggiunge il login dashboard |
| [U07](u07-primer-sviluppo) | Sviluppo locale, test e qualità | `any` | prima di U16, che aggiunge CI e test reali |
| [U08](u08-primer-roadmap) | Roadmap, questioni aperte e istruzioni agente | `any` | prima di U14, che aggiunge stato M0 e comandi reali |
| [U09](u09-gcp-bootstrap) | Bootstrap GCP: progetto, billing, API, Firestore | `large` | prima di U13, che aggiunge le risorse reali e la distinzione dev/prod |
| [U10](u10-gcp-identita-runtime) | Identità, segreti, storage e runtime | `large` | indipendente da U09, ma non scrivere sulla pagina di U09 |
| [U11](u11-gcp-eventi-ci) | Eventi e CI/CD su GCP | `any` | indipendente |
| [U12](u12-gcp-osservabilita-integrazioni) | Osservabilità, checklist e integrazioni | `large` | dopo U06 (patch su `architettura/sicurezza`); indipendente da U11 |
| [U13](u13-inventario) | Inventario GCP reale e ambienti | `any` | dopo U09 (patch su gcp-bootstrap) |
| [U14](u14-agents-readme) | Convenzioni del repo e comandi correnti | `any` | dopo U08 (patch su convenzioni-agenti) |
| [U15](u15-codice-struttura) | Struttura del codice e porte reali | `any` | dopo U03 (patch su due pagine create da U03) |
| [U16](u16-codice-qualita) | Test, CI e script del repo | `any` | dopo U07 e dopo U14 (patch sulle stesse pagine) |

## Esecuzione

- Lease: **1 giorno** per unità. L'esecutore fa il claim con `if_match`; il rifiuto `stale_write` significa che qualcun altro la tiene, quindi passa avanti e non ritenta.
- L'esecutore non decide proprietà né collocazione: sono fissate qui e nel testo dell'unità.
- Un'unità che non riesce a leggere la sua fonte va `blocked` con `waiting_on` e `review_after`, mai saltata in silenzio.
- Una fonte aggiunta dopo l'inizio è una nuova Source e nuove unità sotto questo stesso piano, non un piano nuovo.
