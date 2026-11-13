---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot, sources/2026-11-13-agents-md-e-readme-md-convenzioni-e-comandi-del-repo]
status: active
timestamp: 2026-11-13
title: "Convenzioni per l'agente"
type: Pagina
---
# Convenzioni per l'agente

Istruzioni operative per chi lavora sul repository (§13). Le regole architetturali non negoziabili sono un soggetto a sé e non sono ripetute qui: queste sono le convenzioni di lavoro, non i vincoli di disegno. Fonte: [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]].

## Come lavorare

1. **Una milestone alla volta**: non anticipare le milestone successive (l'ordine e i criteri sono in [[progetto/roadmap]]).
2. **Piano prima della modifica ampia**: se una modifica tocca il modello dati, l'infrastruttura o più di circa 3 file, proporre prima un piano breve.
3. **Unità complete**: ogni nuovo servizio include DTO, Actor, audit e test; ogni nuovo repository include contract test.
4. **Dipendenze giustificate**: non aggiungere dipendenze senza motivarle in una riga.
5. **Verifica le API esterne**: per librerie e servizi esterni (PydanticAI, PyMongo, libreria Telegram, Cloud Tasks, API Firestore-MongoDB) controllare documentazione e versione installata; non inventare API né presumere compatibilità.
6. **Chiedi invece di assumere**: in caso di ambiguità, porre la domanda.

## Stato della milestone

- Milestone corrente: **M0 — fondamenta**; l'elenco delle milestone e i criteri di chiusura sono in [[progetto/roadmap]].
- Infrastruttura: **manuale su GCP**, niente Terraform per ora.
- Database: **Firestore Enterprise** con compatibilità MongoDB; **MongoDB 8.0** locale per lo sviluppo.

## Regole rapide del repo

Convenzioni operative del repository. Le dieci regole architetturali non negoziabili non sono ripetute qui: sono in [[architettura/regole]].

- Non committare segreti: `.env` resta locale, `.env.example` è il modello.
- Firestore: `roles/datastore.user` copre i dati; `drop()` e `createIndex()` richiedono permessi schema e un principal di migrazione dedicato.
- Aggiornare `docs/gcp-inventory.md` quando si crea o modifica una risorsa GCP.
- Se una decisione del primer cambia, aggiornare il primer o segnalarlo.

## Definition of done

Una modifica è conclusa quando:

- test e contract test passano;
- lint e type-check sono puliti;
- indici e migrazioni sono aggiornati;
- l'IaC è aggiornata se cambia l'infrastruttura;
- `AGENTS.md` è aggiornato se cambia una decisione.

Una milestone si chiude quando la sua definizione di done e i criteri di accettazione in [[progetto/roadmap]] sono soddisfatti.
