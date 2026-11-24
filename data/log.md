# Log

## 2026-11-24T16:26:32Z

concept_patch: progetto/questioni-aperte

## 2026-11-24T16:26:20Z

concept_patch: architettura/integrazioni

## 2026-11-24T16:26:08Z

concept_patch: architettura/porte-e-fake

## 2026-11-24T16:25:56Z

concept_patch: progetto/roadmap

## 2026-11-24T16:25:21Z

concept_write: progetto/m7-1-link

## 2026-11-24T11:41:48Z

M6 chiusa il 2026-11-24 (`v0.9.0`): un vocale diventa un turno dell'agente. Cloud Speech-to-Text V2 `short` in multi-regione `eu`, audio mai conservato, limiti 60 s/10 MB, eco di debug. Prova dal telefono riuscita (vocale → trascrizione → proposta → task); i rifiuti restano coperti dai test automatici. Prossima: M7 — link e immagini.

## 2026-11-24T11:41:48Z

concept_batch (2 operation(s)):
- progetto/roadmap
- progetto/m6-vocali

## 2026-11-24T11:41:35Z

M6 chiusa il 2026-11-24 (`v0.9.0`): un vocale diventa un turno dell'agente. Cloud Speech-to-Text V2 `short` in multi-regione `eu`, audio mai conservato, limiti 60 s/10 MB, eco di debug. Prova dal telefono riuscita (vocale → trascrizione → proposta → task); i rifiuti restano coperti dai test automatici. Prossima: M7 — link e immagini.

## 2026-11-23T21:44:32Z

concept_patch: progetto/m6-vocali

## 2026-11-23T21:44:23Z

Incidente post-deploy v0.9.0: il bot non rispondeva a nessun messaggio, testo compreso. La modifica IAM dalla console aveva **sostituito** la binding `roles/datastore.user` (condizionata al DB) con `roles/speech.client`: il runtime api restava senza Firestore e il webhook rispondeva 500 con `pymongo OperationFailure` code 13. Ripristinato `datastore.user` con la condizione `family-bot-db-only`; i retry di Telegram hanno poi consegnato la risposta (200). Regola: i ruoli si aggiungono da «Grant access», mai con la matita sulla riga che si sta filtrando. Nota per M9: `/readyz` non prova il database, quindi lo smoke test del deploy passa anche con Firestore irraggiungibile.

## 2026-11-23T21:20:36Z

M6: `speech.googleapis.com` abilitata e `roles/speech.client` concesso al solo `family-bot-api` senza condizione; verifica con token impersonato sul multi-regione `eu` riuscita. Il primo tentativo dalla console aveva ereditato la condizione Firestore (403); regola: `--condition=None` per ogni nuovo ruolo a questi service account.

## 2026-11-23T21:20:36Z

concept_patch: progetto/m6-vocali

## 2026-11-23T16:32:24Z

M6 — vocali con STT: implementata su Cloud Speech-to-Text V2 (modello `short`, it-IT, multi-regione `eu`), sincrona; audio in memoria e mai conservato; limiti 60 s/10 MB; eco di debug `VOICE_SHOW_TRANSCRIPT`; run con `input_kind=voice`. Restano: abilitare `speech.googleapis.com`, `roles/speech.client` al runtime api, verifica con token impersonato, deploy e prova dal telefono. STT locale rimandato in coda alla roadmap.

## 2026-11-23T16:32:11Z

concept_batch (2 operation(s)):
- progetto/stack
- progetto/questioni-aperte

## 2026-11-23T16:31:53Z

concept_batch (2 operation(s)):
- architettura/integrazioni
- architettura/porte-e-fake

## 2026-11-23T16:30:48Z

concept_batch (2 operation(s)):
- progetto/m6-vocali
- progetto/roadmap

## 2026-11-23T15:17:55Z

2026-11-23 — M5 chiusa (v0.8.0). Login dal telefono verificato dopo il fix della CSP: mancava https://apis.google.com in script-src (l'SDK Firebase lo carica per il popup), aggiunti anche accounts.google.com in frame-src e la diagnostica dei codici auth/* nel client. Resta opzionale la prova con un account reale non registrato (il 403 e il log admin sono coperti dai test) e la restrizione della chiave browser. Prossima milestone: M6 — vocali con STT.

## 2026-11-23T15:17:49Z

concept_patch: progetto/roadmap

## 2026-11-23T15:17:41Z

concept_patch: progetto/m5-dashboard

## 2026-11-23T12:07:32Z

2026-11-23 — M5 in sola lettura rilasciata in v0.8.0: API /api/v1 (token Firebase, 401/403, CORS allowlist, nessuna scrittura) e SPA Vite+TS su Firebase Hosting (family-bot-dev.simonecastellani.com), deploy UI indipendente dall'API. 337 pytest + 22 vitest, ruff/pyright/eslint verdi; smoke Hosting+API verde. Inciampo IAM al primo deploy: ruoli firebase.viewer e firebasehosting.admin legati alla condizione Firestore family-bot-db-only e serviceUsageConsumer mancante; risolto con binding senza condizione. Resta la verifica dal telefono (membro, estraneo 403, log solo admin) e la restrizione della chiave browser.

## 2026-11-23T12:07:24Z

concept_patch: progetto/m5-dashboard

## 2026-11-23T10:36:13Z

2026-11-23 — Importata la skill `impeccable` v4.10.0 (Apache-2.0) come sola documentazione: `skills/impeccable/SKILL.md` + 50 file `reference/` (playbook, degraded, mode). I richiami al launcher sono riscritti su `npx impeccable`, `DESIGN.md` → pagina `sviluppo/design-system`, `PRODUCT.md` → `progetto/prodotto`; nessuno script vendored, nessun hook. Contratto in [[sviluppo/skill-di-sviluppo]] aggiornato a tredici skill. Lint 0.

## 2026-11-23T10:35:32Z

concept_patch: sviluppo/skill-di-sviluppo

## 2026-11-22T22:25:36Z

M5 avviata, M4.5 e M4.6 chiuse: l'operatore ha dichiarato chiuso tutto ciò che precede M5. - `progetto/m5-dashboard` creata (`in-progress`, `v0.8.0`): spec dal primer §10 — `GET /api/v1`, login Google OIDC con mapping email → `members`, log agente solo `admin`, nessuna route di scrittura. Le quattro decisioni ancora aperte sono elencate nella pagina (forma della UI, Firebase Auth o OIDC diretto, viste del minimo, sede dell'OpenAPI). - `progetto/m4-6-eventi-esistenti`: `done`, con Esito (rilascio `v0.6.0`, revisione `family-bot-api-00026-qal`); registrato che la verifica reale dal telefono prevista da W6 non è stata fatta. - `progetto/m4-5-routing-locale`: `done` per decisione dell'operatore, **non** con il gate: M4.5a deployata in shadow (`v0.5.0`, baseline zero-shot 68,3%), M4.5b (dataset ≥ 1.000, fine-tune, eval, canary) non eseguita. Il debito resta scritto nell'Esito e nell'Handoff, non è un criterio raggiunto. - `progetto/roadmap`: corretto lo «Stato corrente», che ripeteva tre volte la frase sullo slittamento di M5 e accorpava M4.6, M4.7 e M5 in un solo punto elenco. M4.5 ora ha anche il segno di chiusura nella tabella. Lint 0, review 0; l'unica pagina in lavorazione è M5.

## 2026-11-22T22:25:24Z

concept_batch (3 operation(s)):
- progetto/m4-5-routing-locale
- progetto/m4-6-eventi-esistenti
- progetto/roadmap

## 2026-11-22T22:25:11Z

concept_write: progetto/m5-dashboard

## 2026-11-22T21:36:03Z

Skill di sviluppo: la KB ne spedisce dodici (con observability-and-instrumentation), ma due punti dicevano undici. Corretti il titolo di [[sviluppo/skill-di-sviluppo]] (Le dodici skill) e la riga 7 di [[sviluppo/convenzioni-agenti]]. instructions.md era gia' a dodici. Lint 0, review 0. Resta la nota della sessione precedente: nessun'altra occorrenza di "undici" fuori dai log storici.

## 2026-11-22T21:35:43Z

concept_batch (2 operation(s)):
- sviluppo/skill-di-sviluppo
- sviluppo/convenzioni-agenti

## 2026-11-22T09:53:18Z

M4.7 chiusa (`done`, 2026-11-22, `v0.7.3`): serie in calendario, cancellazione di occorrenza/serie con bottoni etichettati, più date in una conferma (max 5). Test funzionale dal telefono superato. Prossima milestone: M5 — dashboard (`v0.8.0`); il fine-tuning e il canary di M4.5 restano aperti.

## 2026-11-22T09:53:17Z

concept_batch (1 operation(s)):
- progetto/roadmap

## 2026-11-22T09:53:09Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-22T08:15:03Z

concept_batch (1 operation(s)):
- progetto/roadmap

## 2026-11-22T08:15:03Z

M4.7 `v0.7.3`: la cancellazione di più date è una sola proposta (max 5). `delete_event` prende una lista di id, il servizio fa un DELETE per data, i master si deduplicano con `scope=all`, i fallimenti parziali si riportano nel messaggio e il payload vecchio con `event_id` resta valido per le proposte in sospeso.

## 2026-11-22T08:14:57Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-21T23:41:09Z

concept_batch (1 operation(s)):
- progetto/roadmap

## 2026-11-21T23:41:04Z

M4.7 `v0.7.2`: la verifica reale ha trovato il difetto dei quattro bottoni (due date × due alternative) e della tastiera che spariva dopo il primo tap. `delete_event` ora prende `scope: one|all|ask`, le alternative condividono un gruppo e dopo un tap il messaggio tiene i bottoni delle proposte ancora aperte.

## 2026-11-21T23:41:03Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-21T23:40:57Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-21T23:16:55Z

kb-doctor: review 22 -> 0, findings 0 -> 0, fixable 0 -> 0. Sessione assisted su family-bot-kb: 22 decisioni, tutte scelte dall'operatore. **Template (7 artefatti, 5 mappe)** - `templates/ingestunit.md` e `templates/ingestplan.md`; mappa ingest: templates [ingestplan, ingestunit], default ingestunit. - `templates/pagina.md` e `templates/overview.md`; mappa progetto: [overview, pagina], default pagina. - `templates/architecture.md` e `templates/concept.md`; mappa architettura: [architecture, concept], default architecture. - `templates/runbook.md`; mappa infrastruttura: [concept, pagina, runbook], default pagina. - `templates/pagina.md`: `provenance` declassato a facoltativo, perche' `progetto/m1-task-core` e' un resoconto senza fonte. - `sviluppo/skill-di-sviluppo`: tipo Concept -> Pagina. Il legame template e' per tipo e la mappa sviluppo accetta solo `pagina`; la pagina non ha ne' fonte ne' sezione Fonte. La variante `concept-sviluppo` e' stata creata e poi rimossa: non si legava. - Aggiunti `overview`, `ingestplan` e `runbook` per i tipi che le proposte lasciavano scoperti; senza, ogni mappa avrebbe guadagnato un template_missing. **Regole delle unita' (4 repeated_fact chiusi)** - Collocazione, conflitti e lease stanno in `ingest/piano-family-bot-docs` §Regole delle unita'. Le 16 unita' non li ricopiano piu'; ognuna chiude con le pagine che ha prodotto. **Grafo e indici** - Isola chiusa: la tabella §Fonti coperte del piano linka le 5 Source che la campagna ha consumato. - `index: generated` su ingest, progetto, sviluppo, architettura, infrastruttura; sources resta curato e le 5 voci passano sotto "## Fonti registrate". - `cut_concept` chiuso: ogni unita' linka i propri target, quindi il piano non e' l'unico ponte verso il grafo. **Contratti e glossario** - `progetto/_map`: `field_values.Pagina.status` = active, done, in-progress. - `sources/_map`: `field_values.Source.source_kind` = codebase, document. - `glossary.yaml` con MongoDB, GCP, OIDC, M0, M1, M2 e gli alias (Mongo; Google Cloud Platform, Google Cloud; OpenID Connect). **Nota, fuori dai 22 item** - `instructions.md` dice "eleven development skills" e `sviluppo/skill-di-sviluppo` parla di undici skill, ma la KB ne spedisce dodici (`observability-and-instrumentation`). Da allineare in una prossima sessione.

## 2026-11-21T23:16:20Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-21T23:16:10Z

map_update: infrastruttura

## 2026-11-21T23:15:41Z

index_patch: sources

## 2026-11-21T23:15:40Z

map_update: sviluppo

## 2026-11-21T23:15:34Z

concept_patch: architettura/modello-dati

## 2026-11-21T23:15:34Z

concept_patch: sviluppo/skill-di-sviluppo

## 2026-11-21T23:15:19Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-21T23:15:11Z

concept_patch: ingest/piano-family-bot-docs

## 2026-11-21T23:14:42Z

map_update: sources

## 2026-11-21T23:14:42Z

map_update: infrastruttura

## 2026-11-21T23:14:42Z

map_update: architettura

## 2026-11-21T23:14:39Z

map_update: sviluppo

## 2026-11-21T23:14:39Z

map_update: progetto

## 2026-11-21T23:14:39Z

map_update: ingest

## 2026-11-21T23:11:06Z

concept_batch (1 operation(s)):
- progetto/roadmap

## 2026-11-21T23:11:01Z

M4.7 rilasciata (`v0.7.1`, revisione `family-bot-api-00030-peh`): serie in calendario con `rrule`, master mai in cache, sync best-effort dopo la scrittura, cancellazione di una occorrenza o della serie con due bottoni etichettati. Trovati strada facendo: id delle azioni che collidevano (timestamp della UUIDv7) e `BYMONTH` fuori dal sottoinsieme. Resta la verifica reale dal telefono.

## 2026-11-21T23:11:01Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-21T23:10:56Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-21T23:10:50Z

concept_batch (1 operation(s)):
- progetto/m4-7-eventi-ricorrenti

## 2026-11-21T23:01:54Z

2026-11-22 — Verificate le reference upstream (`references/` del repo agent-skills) per le dodici skill. Spediti `security-checklist.md` e `performance-checklist.md` accanto a code-review-and-quality, `definition-of-done.md` a incremental-implementation, `testing-patterns.md` (JS/TS) a test-driven-development; link corretti da `../../references/` a sibling. Non spedite `orchestration-patterns.md` (persona/slash command upstream) e `accessibility-checklist.md` (non citata): motivo registrato in [[sviluppo/skill-di-sviluppo]] §Reference spedite. Lint 0, gate verde.

## 2026-11-21T23:01:49Z

concept_patch: sviluppo/skill-di-sviluppo

## 2026-11-21T22:58:56Z

2026-11-21 — Alla skill `observability-and-instrumentation` si aggiunge il riferimento upstream `observability-checklist.md` (file accanto a SKILL.md, provenienza MIT): lista generica at-a-glance; la baseline del progetto resta [[infrastruttura/osservabilita]]. Lint 0, gate verde.

## 2026-11-21T22:57:09Z

2026-11-21 — Aggiunta la dodicesima skill, `observability-and-instrumentation` (addyosmani/agent-skills, MIT), adattata alla KB: baseline di telemetria in [[infrastruttura/osservabilita]], runbook come pagine `infrastruttura/runbook-<alert>` di tipo Runbook, igiene segreti/PII secondo [[architettura/sicurezza]]. Pagina-contratto [[sviluppo/skill-di-sviluppo]] e `instructions.md` aggiornati a dodici skill. Lint 0, gate verde.

## 2026-11-21T22:57:02Z

concept_patch: sviluppo/skill-di-sviluppo

## 2026-11-21T22:39:35Z

concept_batch (1 operation(s)):
- progetto/questioni-aperte

## 2026-11-21T22:39:29Z

concept_batch (2 operation(s)):
- progetto/m4-7-eventi-ricorrenti (+ index entry)
- progetto/roadmap

## 2026-11-21T14:54:08Z

concept_batch (1 operation(s)):
- progetto/roadmap

## 2026-11-21T14:54:08Z

M4.6 rilasciata: tag `v0.6.0`, revisione `family-bot-api-00026-qal` con traffico al 100% dopo canary e smoke test. Corretti prima del rilascio i difetti trovati da una revisione ostile della diff (durata da muro invece che assoluta, fine dell'evento di tutta la giornata, test del doppio tap, sync che riscriveva la riga). Resta la verifica reale dal telefono.

## 2026-11-21T14:54:03Z

concept_batch (1 operation(s)):
- progetto/m4-6-eventi-esistenti

## 2026-11-21T14:34:56Z

M4.6 — eventi esistenti: `update_event`/`delete_event` con conferma. Scrittura condizionale (PATCH parziale con `If-Match`), durata conservata quando cambia solo l'inizio, tipo dell'evento invariato, serie toccata per istanza, cancellazione idempotente. Le etichette di routing contano i nuovi tool, così il dataset shadow di M4.5 non perde i turni di correzione. Restano tag `v0.6.0`, deploy e verifica reale.

## 2026-11-21T14:34:50Z

concept_batch (1 operation(s)):
- progetto/roadmap

## 2026-11-21T14:34:40Z

concept_batch (1 operation(s)):
- progetto/m4-6-eventi-esistenti

## 2026-11-21T12:54:27Z

[progetto] M4.6 — Eventi esistenti: piano scritto in `progetto/m4-6-eventi-esistenti` (status `open`) e aggiunto alla roadmap. Rilascio `v0.6.0`; M5 slitta a `v0.7.0`. Contenuto: `update_event` con If-Match e 412, `delete_event` idempotente, servizi con audit/cache, azioni agente con conferma, id evento in `list_calendar_events`, prompt per le correzioni, test e verifica reale sul «Cambio gomme». Handoff: M4 e M4.5a sono in produzione (`v0.5.2`), M4.6 non è iniziata; si riparte da W1 in build mode.

## 2026-11-21T12:53:17Z

concept_batch (2 operation(s)):
- progetto/m4-6-eventi-esistenti (+ index entry)
- progetto/roadmap

## 2026-11-21T09:54:10Z

[progetto] v0.5.2 — qualità delle risposte: `list_occurrences` restituisce il titolo del task, policy senza capacità di UI né Markdown, sanitizer sull'output e sull'outcome. Deploy revisione 00024-foc. Shadow verificato su tre messaggi reali: previsioni tutte sbagliate (chat/task_write/ingest) con `llm_used=true`, etichette corrette dai tool; il dataset cresce. M4.6 (modifica/cancellazione eventi) è la prossima, v0.6.0, M5 → v0.7.0.

## 2026-11-21T09:54:05Z

concept_batch (1 operation(s)):
- progetto/m4-5-routing-locale

## 2026-11-21T01:37:59Z

2026-11-21 — Importate undici skill di sviluppo (addyosmani/agent-skills, MIT) adattate alla KB: code-review-and-quality, code-simplification, context-engineering, documentation-and-adrs, doubt-driven-development, idea-refine, incremental-implementation, interview-me, source-driven-development, spec-driven-development, test-driven-development. Aggiunti il template `adr` e la pagina [[sviluppo/skill-di-sviluppo]]; ADR in `architettura/adr-NNNN-<slug>`, idee/intenti/spec in `progetto/`. Lint 0, gate verde, client sincronizzati.

## 2026-11-21T01:31:59Z

concept_patch: sviluppo/convenzioni-agenti

## 2026-11-21T01:31:46Z

concept_write: sviluppo/skill-di-sviluppo

## 2026-11-21T01:27:17Z

[progetto] M4.5a — shadow routing deployato (v0.5.0, poi v0.5.1): client MCP generico, LayaClassifier su `agent_runs` (route/confidence/modello/llm_used), export JSONL e set congelato con eval. Baseline zero-shot 68,3% su 60 frasi. Verificato da Cloud Run con un job one-off (308 ms, task_write per «che task ho oggi?»); il primo tentativo ha scoperto un newline finale nel secret `laya-mcp-token`, corretto e documentato. Prossimo: raccogliere ≥1.000 esempi dalle conversazioni reali, poi fine-tuning e canary delle sole query.

## 2026-11-21T01:27:11Z

concept_batch (1 operation(s)):
- progetto/m4-5-routing-locale

## 2026-11-21T01:16:51Z

concept_batch (2 operation(s)):
- progetto/m4-5-routing-locale (+ index entry)
- progetto/roadmap

## 2026-11-19T12:53:15Z

[progetto] M4 chiusa il 2026-11-19: prova dal telefono confermata (proposta `create_event` + bottone, evento su Google Calendar), sync idempotente, cancellati gestiti, creazione reale verificata. Rilascio `v0.4.1`, revisione `00017-zef`. Prossima milestone: M5 — dashboard in sola lettura.

## 2026-11-19T12:52:14Z

concept_batch (2 operation(s)):
- progetto/m4-google-calendar
- progetto/roadmap

## 2026-11-19T09:24:55Z

[progetto] M4 — dopo v0.4.1 verificati in cloud: sync full e incrementale dal job Scheduler (200), creazione reale di un evento via `CreateCalendarEventService`, sync (upsert 1), cancellazione su Google (204), sync incrementale (`deleted: 1`) e cache vuota. Resta solo la prova dal telefono (proposta `create_event` + bottone). Rilascio `v0.4.1`, revisione `00017-zef`.

## 2026-11-19T09:16:18Z

[progetto] M4 — deploy v0.4.0 riuscito (revisione 00015-nol, `/livez` 0.4.0, tre job Scheduler), ma il primo sync reale è fallito con 500: Google non restituisce `nextSyncToken` a una listing con `timeMin`/`timeMax`/`orderBy`. Fix in v0.4.1: la full sync legge l'intero calendario e applica la finestra [-7, +90] alla cache; verificato con due sync reali consecutivi (full + incrementale) contro l'API. Il token Service Account funziona su Cloud Run: la chiamata a `events.list` era 200.

## 2026-11-19T09:16:10Z

concept_patch: progetto/m4-google-calendar

## 2026-11-19T00:06:52Z

[progetto] M4 — Calendar: l'app OAuth non si poteva pubblicare (Google richiede un dominio autorizzato e verificato nella Branding page). Scelta del 2026-11-19: ADC del service account di runtime con scope `calendar.events`, agenda «Famiglia» condivisa con `family-bot-api` e `family-bot-worker`. W0 verificato con token impersonato: `events.list` 200 (`accessRole: writer`), `events.insert` 200, `DELETE` 204; nessun secret OAuth. Concesso `serviceAccountTokenCreator` all'operatore sui due SA per le verifiche. Resta il deploy v0.4.0.

## 2026-11-19T00:06:33Z

concept_batch (2 operation(s)):
- progetto/m4-google-calendar
- progetto/roadmap

## 2026-11-18T17:11:49Z

[progetto] M4 — Google Calendar: implementati W1–W5 (dominio, cache `calendar_events`, `calendar_sync_state`, adapter Google su httpx, sync con 410 e full resync, creazione con id deterministico e conferma, tool `create_event`/`list_calendar_events`/`get_week_overview`). Suite verde (lint, pyright, contract su MongoDB reale). Restano W0 (API Calendar, agenda «Famiglia», app OAuth pubblicata, refresh token, tre secret) e il deploy v0.4.0. Debito provider LLM rimandato a milestone dedicata con classificatore locale.

## 2026-11-18T17:11:32Z

concept_batch (2 operation(s)):
- progetto/m4-google-calendar (+ index entry)
- progetto/roadmap

## 2026-11-18T14:04:02Z

[progetto/m3-promemoria-event-driven] Contract sul Firestore reale eseguito (2026-11-18) col consenso dell'operatore: 41 test verdi, transazioni incluse. Guard ALLOW_REMOTE_CONTRACT per non svuotare per sbaglio un database remoto; TEST_MONGODB_DATABASE punta a family-bot. Gli indici sopravvivono allo svuotamento. Dati ricreati (membro + task nido) e promemoria ripianificato. Suite notturna rinviata alla pre-produzione.

## 2026-11-18T14:04:01Z

concept_write: progetto/m3-promemoria-event-driven

## 2026-11-18T14:03:45Z

concept_write: progetto/questioni-aperte

## 2026-11-18T12:57:10Z

[progetto/m3-promemoria-event-driven] M3 rilasciata come v0.3.0 (2026-11-18): coda Cloud Tasks reminders, worker privato con /internal/* verificato via OIDC, job Scheduler da deploy/scheduler.json, bottoni Fatto/Domani, run.invoker stretto al worker. Scoperti in cloud: limite 720h di Cloud Tasks, $elemMatch non supportato da Firestore, worker senza chiave LLM. Primo promemoria reale in coda per il 5 dicembre. Aperto: contract test sul Firestore reale.

## 2026-11-18T12:57:09Z

concept_write: progetto/roadmap

## 2026-11-18T12:57:09Z

concept_write: progetto/questioni-aperte

## 2026-11-18T12:56:53Z

concept_write: progetto/m3-promemoria-event-driven

## 2026-11-18T11:21:04Z

concept_write: progetto/m3-promemoria-event-driven

## 2026-11-17T14:43:22Z

[progetto/m2-deploy-telegram-agente] M2 chiusa (2026-11-17): release v0.2.0 da tag semver attraverso la pipeline (guardia, test, immagine, migrazioni, canary senza traffico, smoke, spostamento traffico). In produzione la revisione 00007-lin riporta 0.2.0. Aggiunti WIF con condizione su ref_type=tag, job di migrazione indici, /livez con versione e /readyz con ping al database. Scoperti e corretti: CI rossa dal 13 novembre (--replSet nei services), indici parziali non supportati da Firestore, createIndex sotto condizione IAM.

## 2026-11-17T14:43:17Z

concept_write: infrastruttura/eventi-e-ci

## 2026-11-17T14:43:17Z

concept_write: progetto/roadmap

## 2026-11-17T14:43:17Z

concept_write: progetto/questioni-aperte

## 2026-11-17T14:40:55Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-17T12:44:49Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-17T08:30:28Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-17T00:32:39Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-16T00:19:40Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-15T23:57:39Z

concept_write: infrastruttura/identita-e-runtime

## 2026-11-15T23:57:38Z

concept_write: progetto/stack

## 2026-11-15T23:57:38Z

concept_write: progetto/questioni-aperte

## 2026-11-15T23:57:38Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-15T23:29:37Z

[progetto/m2-deploy-telegram-agente] Pianificazione M2 (2026-11-16): approvato il piano di deploy Cloud Run + webhook Telegram + agente. Decisioni: python-telegram-bot >=20 con Bot e non Application/Dispatcher; porta AgentEngine davanti a PydanticAI; conferme deterministiche in agent_runs.actions[]; indici applicati dal job di deploy con family-bot-migrator. Spostati a M3 il servizio worker e i contract test sul Firestore Enterprise reale. Nessun codice scritto: la milestone resta open.

## 2026-11-15T23:29:25Z

concept_write: architettura/agente

## 2026-11-15T23:29:25Z

concept_write: progetto/stack

## 2026-11-15T23:29:11Z

concept_write: progetto/questioni-aperte

## 2026-11-15T23:29:02Z

concept_write: progetto/roadmap

## 2026-11-15T23:28:49Z

concept_write: progetto/m2-deploy-telegram-agente

## 2026-11-15T22:42:38Z

concept_patch: infrastruttura/inventario

## 2026-11-15T22:42:38Z

concept_patch: infrastruttura/identita-e-runtime

## 2026-11-15T22:42:28Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:12:52Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:10:35Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:10:22Z

concept_patch: infrastruttura/inventario

## 2026-11-15T00:09:45Z

concept_patch: infrastruttura/eventi-e-ci

## 2026-11-15T00:09:36Z

concept_patch: infrastruttura/identita-e-runtime

## 2026-11-14T17:24:59Z

[progetto] Chiusura M1: riverifica locale del 2026-11-14 (74 test verdi su MongoDB locale, ruff e pyright puliti). `local-admin` e il contract test sul database Enterprise reale restano debito di M2, allineati in `docs/gcp-inventory.md` e `docs/gcp-setup.md` del repo (commit `61939ac` e `d16b519`). Nessuna risorsa GCP modificata.

## 2026-11-14T17:24:52Z

concept_patch: progetto/m1-task-core

## 2026-11-14T17:24:40Z

concept_patch: infrastruttura/inventario

## 2026-11-13T18:28:22Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T18:26:59Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T18:22:26Z

[progetto] M1 — task core implementata in {{repo:family-bot}}: dominio e RRULE (dateutil, whitelist, DST), porte + adapter MongoDB (UUIDv7, indice unico task_id+occurrence_key, transazioni per mutazione+audit), servizi comando/query, creazione con materializzazione della prima finestra, 72 test (unit + contract + integration). MongoDB locale e CI ora usano un replica set a nodo singolo. Criteri 1–7 coperti.

## 2026-11-13T18:22:16Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T18:21:48Z

concept_patch: sviluppo/convenzioni-agenti

## 2026-11-13T18:21:42Z

concept_patch: progetto/roadmap

## 2026-11-13T18:21:37Z

concept_patch: progetto/m1-task-core

## 2026-11-13T18:21:23Z

concept_patch: architettura/task-e-occorrenze

## 2026-11-13T17:38:57Z

concept_batch (4 operation(s)):
- architettura/task-e-occorrenze (+ index entry)
- progetto/m1-task-core (+ index entry)
- progetto/roadmap
- progetto/questioni-aperte

## 2026-11-13T10:29:34Z

Contraddizioni risolte (2026-11-13): nome task Cloud Tasks versionato vs dedupe_key (idempotenza su reminders._id); soglie budget canoniche 50/80/100; limite documento 16 MiB con regola di progetto <4 MiB; Motor escluso (EOL 14/05/2026). Pagine: architettura/promemoria, architettura/modello-dati, infrastruttura/eventi-e-ci, infrastruttura/gcp-bootstrap, infrastruttura/inventario, progetto/stack, progetto/questioni-aperte (9→7 aperte), progetto/roadmap. Stesse correzioni applicate a primer.md e docs/gcp-setup.md nel repo family-bot (non committate).

## 2026-11-13T10:28:09Z

concept_batch (8 operation(s)):
- architettura/promemoria
- infrastruttura/eventi-e-ci
- architettura/modello-dati
- infrastruttura/gcp-bootstrap
- infrastruttura/inventario
- progetto/stack
- progetto/questioni-aperte
- progetto/roadmap

## 2026-11-13T09:39:11Z

Ingest family-bot completato: 5 fonti, 16 unità (10 any / 6 large / 0 human), 19 pagine attive. Revisione campione: U01, U04, U07, U09, U11, U13 — gate puliti, redazione di email/billing corretta, nessun contenuto inventato. Aperti due conflitti tra fonti lasciati come checkbox: nome task Cloud Tasks (dedupe) e soglie budget 50/90/100% vs 50/80/100%.

## 2026-11-13T09:39:11Z

concept_batch (6 operation(s)):
- sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot
- sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud
- sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp
- sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo
- sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci
- ingest/piano-family-bot-docs

## 2026-11-13T09:38:22Z

concept_patch: ingest/u16-codice-qualita

## 2026-11-13T09:38:14Z

concept_patch: sviluppo/locale-e-test

## 2026-11-13T09:38:14Z

concept_patch: sviluppo/qualita

## 2026-11-13T09:36:45Z

concept_patch: ingest/u13-inventario

## 2026-11-13T09:36:33Z

concept_patch: ingest/u12-gcp-osservabilita-integrazioni

## 2026-11-13T09:36:25Z

concept_batch (3 operation(s)):
- infrastruttura/osservabilita (+ index entry)
- architettura/integrazioni (+ index entry)
- architettura/sicurezza

## 2026-11-13T09:35:52Z

concept_batch (2 operation(s)):
- infrastruttura/inventario (+ index entry)
- infrastruttura/gcp-bootstrap

## 2026-11-13T09:35:49Z

concept_patch: ingest/u16-codice-qualita

## 2026-11-13T09:35:31Z

concept_patch: ingest/u14-agents-readme

## 2026-11-13T09:35:25Z

concept_patch: sviluppo/convenzioni-agenti

## 2026-11-13T09:35:25Z

concept_patch: sviluppo/qualita

## 2026-11-13T09:35:16Z

concept_patch: ingest/u11-gcp-eventi-ci

## 2026-11-13T09:35:10Z

concept_patch: infrastruttura/eventi-e-ci

## 2026-11-13T09:35:10Z

concept_patch: ingest/u15-codice-struttura

## 2026-11-13T09:35:04Z

concept_batch (2 operation(s)):
- architettura/porte-e-fake
- architettura/regole

## 2026-11-13T09:34:59Z

concept_write: infrastruttura/eventi-e-ci

## 2026-11-13T09:34:50Z

concept_patch: ingest/u13-inventario

## 2026-11-13T09:34:40Z

concept_patch: ingest/u10-gcp-identita-runtime

## 2026-11-13T09:34:31Z

concept_write: infrastruttura/identita-e-runtime

## 2026-11-13T09:34:21Z

concept_patch: ingest/u09-gcp-bootstrap

## 2026-11-13T09:34:16Z

concept_patch: ingest/u14-agents-readme

## 2026-11-13T09:34:09Z

concept_write: infrastruttura/gcp-bootstrap

## 2026-11-13T09:34:02Z

concept_patch: ingest/u12-gcp-osservabilita-integrazioni

## 2026-11-13T09:33:53Z

concept_patch: ingest/u08-primer-roadmap

## 2026-11-13T09:33:48Z

concept_patch: ingest/u15-codice-struttura

## 2026-11-13T09:33:39Z

concept_patch: ingest/u11-gcp-eventi-ci

## 2026-11-13T09:33:39Z

concept_patch: ingest/u07-primer-sviluppo

## 2026-11-13T09:33:30Z

concept_batch (2 operation(s)):
- sviluppo/locale-e-test (+ index entry)
- sviluppo/qualita (+ index entry)

## 2026-11-13T09:33:29Z

concept_patch: ingest/u10-gcp-identita-runtime

## 2026-11-13T09:33:27Z

concept_batch (3 operation(s)):
- progetto/roadmap (+ index entry)
- progetto/questioni-aperte (+ index entry)
- sviluppo/convenzioni-agenti (+ index entry)

## 2026-11-13T09:33:20Z

concept_patch: ingest/u03-primer-regole

## 2026-11-13T09:33:11Z

concept_batch (2 operation(s)):
- architettura/regole (+ index entry)
- architettura/porte-e-fake (+ index entry)

## 2026-11-13T09:33:06Z

concept_patch: ingest/u05-primer-promemoria-agente

## 2026-11-13T09:33:06Z

concept_patch: ingest/u06-primer-sicurezza

## 2026-11-13T09:32:57Z

concept_write: architettura/sicurezza

## 2026-11-13T09:32:54Z

concept_batch (2 operation(s)):
- architettura/promemoria
- architettura/agente

## 2026-11-13T09:32:23Z

concept_patch: ingest/u09-gcp-bootstrap

## 2026-11-13T09:32:21Z

concept_patch: ingest/u08-primer-roadmap

## 2026-11-13T09:32:10Z

concept_patch: ingest/u07-primer-sviluppo

## 2026-11-13T09:32:00Z

concept_batch (2 operation(s)):
- architettura/promemoria (+ index entry)
- architettura/agente (+ index entry)

## 2026-11-13T09:31:58Z

concept_patch: ingest/u02-primer-stack

## 2026-11-13T09:31:53Z

concept_patch: ingest/u01-primer-overview

## 2026-11-13T09:31:51Z

concept_patch: ingest/u04-primer-modello-dati

## 2026-11-13T09:31:49Z

concept_write: progetto/stack

## 2026-11-13T09:31:47Z

concept_write: progetto/overview

## 2026-11-13T09:31:42Z

concept_batch (1 operation(s)):
- architettura/modello-dati (+ index entry)

## 2026-11-13T09:31:01Z

concept_patch: ingest/u05-primer-promemoria-agente

## 2026-11-13T09:31:00Z

concept_patch: ingest/u04-primer-modello-dati

## 2026-11-13T09:31:00Z

concept_patch: ingest/u03-primer-regole

## 2026-11-13T09:31:00Z

concept_patch: ingest/u01-primer-overview

## 2026-11-13T09:31:00Z

concept_patch: ingest/u02-primer-stack

## 2026-11-13T09:30:59Z

concept_patch: ingest/u06-primer-sicurezza

## 2026-11-13T09:30:16Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:46Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:36Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:24Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:26:13Z

concept_batch (16 operation(s)):
- ingest/u01-primer-overview
- ingest/u02-primer-stack
- ingest/u03-primer-regole
- ingest/u04-primer-modello-dati
- ingest/u05-primer-promemoria-agente
- ingest/u06-primer-sicurezza
- ingest/u07-primer-sviluppo
- ingest/u08-primer-roadmap
- ingest/u09-gcp-bootstrap
- ingest/u10-gcp-identita-runtime
- ingest/u11-gcp-eventi-ci
- ingest/u12-gcp-osservabilita-integrazioni
- ingest/u13-inventario
- ingest/u14-agents-readme
- ingest/u15-codice-struttura
- ingest/u16-codice-qualita

## 2026-11-13T09:25:24Z

concept_batch (17 operation(s)):
- ingest/piano-family-bot-docs (+ index entry)
- ingest/u01-primer-overview (+ index entry)
- ingest/u02-primer-stack (+ index entry)
- ingest/u03-primer-regole (+ index entry)
- ingest/u04-primer-modello-dati (+ index entry)
- ingest/u05-primer-promemoria-agente (+ index entry)
- ingest/u06-primer-sicurezza (+ index entry)
- ingest/u07-primer-sviluppo (+ index entry)
- ingest/u08-primer-roadmap (+ index entry)
- ingest/u09-gcp-bootstrap (+ index entry)
- ingest/u10-gcp-identita-runtime (+ index entry)
- ingest/u11-gcp-eventi-ci (+ index entry)
- ingest/u12-gcp-osservabilita-integrazioni (+ index entry)
- ingest/u13-inventario (+ index entry)
- ingest/u14-agents-readme (+ index entry)
- ingest/u15-codice-struttura (+ index entry)
- ingest/u16-codice-qualita (+ index entry)

## 2026-11-13T09:22:35Z

source_register: sources/2026-11-13-codice-di-family-bot-struttura-porte-test-e-ci

## 2026-11-13T09:22:35Z

source_register: sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo

## 2026-11-13T09:22:35Z

source_register: sources/2026-11-13-docs-gcp-inventory-md-inventario-delle-risorse-gcp

## 2026-11-13T09:22:34Z

source_register: sources/2026-11-13-docs-gcp-setup-md-configurazione-manuale-di-google-cloud

## 2026-11-13T09:22:34Z

source_register: sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot

## 2026-11-13T09:22:25Z

map_create: ingest

## 2026-11-13T09:22:25Z

map_create: sviluppo

## 2026-11-13T09:22:25Z

map_create: infrastruttura

## 2026-11-13T09:22:25Z

map_create: architettura

## 2026-11-13T09:22:25Z

map_create: progetto

## 2026-11-13T09:22:25Z

map_create: sources

