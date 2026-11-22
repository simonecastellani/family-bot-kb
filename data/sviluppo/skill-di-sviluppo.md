---
timestamp: 2026-11-21
title: Skill di sviluppo della KB
type: Pagina
status: active
---
# Skill di sviluppo della KB

Dodici skill di processo, adattate da [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) (MIT) all'uso della KB come memoria del progetto: l'originale presuppone una directory di documentazione dentro il repository, qui l'artefatto vive in una pagina della KB. Il corpo delle skill resta in inglese, perché è un'istruzione per l'agente; questa pagina è il contratto su dove finiscono i loro risultati.

## Regole comuni

- niente directory di documentazione locale (docs/adr/, docs/ideas/, docs/intent/, SPEC.md) come deposito: la KB è la fonte di verità;
- scritture con `concept_write`/`concept_batch`, con l'entry d'indice nello stesso batch; chiusura della sessione con `log_append`; stato `open` → `in-progress` → `done`;
- nessun comando git nel clone della KB: ogni scrittura è già un commit; il repository del progetto si cita con `{{repo:family-bot}}`;
- nel repository resta solo la documentazione del codice (README, reference delle API, docs/gcp-inventory.md); quando diverge dalla pagina KB che la descrive si aggiorna la pagina e si registra il drift;
- la risposta all'operatore è nella lingua in cui scrive.

## Le dodici skill

| Skill | Quando si carica | Artefatto in KB |
|---|---|---|
| `code-review-and-quality` | prima di unire una modifica | verdetto e follow-up in `log_append`; il confronto con la spec legge la pagina milestone |
| `code-simplification` | refactor senza cambio di comportamento | rationale in `log_append`; convenzioni in [[sviluppo/convenzioni-agenti]] |
| `context-engineering` | inizio sessione, qualità in calo | `search`/`graph_context` prima di leggere; budget da `kb_status.read_cost`; le regole stanno in instructions.md |
| `documentation-and-adrs` | decisione architetturale, documentazione divergente | `architettura/adr-NNNN-<slug>` col template `adr`, sostituzione con `supersede`; drift in `log_append` |
| `doubt-driven-development` | decisione non banale da mettere in discussione | dubbi in [[progetto/questioni-aperte]]; controllo di `open_contradictions` |
| `idea-refine` | idea grezza da affilare | `progetto/idea-<slug>`, solo su conferma |
| `incremental-implementation` | modifica grande da spezzare | stato della milestone in `progetto/m<n>-<slug>`; un `log_append` per incremento |
| `interview-me` | requisito ambiguo | `progetto/intento-<slug>` |
| `source-driven-development` | decidere su API o servizi esterni | `sources/` con `source_register`; contraddizioni in [[progetto/questioni-aperte]] |
| `spec-driven-development` | feature o milestone senza spec | pagina milestone `progetto/m<n>-<slug>` e [[progetto/roadmap]] |
| `test-driven-development` | implementare logica, correggere un bug | ciclo nel repository; esiti in `log_append`, gate in [[sviluppo/qualita]] |
| `observability-and-instrumentation` | strumentazione, allarmi, incidente difficile da diagnosticare | baseline in [[infrastruttura/osservabilita]], runbook in `infrastruttura/runbook-<alert>`; esito in `log_append` |

## Reference spedite con le skill

Le checklist e i cataloghi di `references/` del repo upstream viaggiano accanto al `SKILL.md` che li cita, e sono letti solo quando servono.

| Skill | File | Nota |
|---|---|---|
| `code-review-and-quality` | `security-checklist.md`, `performance-checklist.md` | checklist generiche di review; la regola del progetto (`architettura/sicurezza`, `sviluppo/qualita`) vince |
| `incremental-implementation` | `definition-of-done.md` | barra generica; il DoD del progetto è in [[sviluppo/convenzioni-agenti]] |
| `test-driven-development` | `testing-patterns.md` | JavaScript/TypeScript: si prende la forma dei pattern, non la sintassi; il Python del progetto è in [[sviluppo/locale-e-test]] |
| `idea-refine` | `frameworks.md`, `refinement-criteria.md`, `examples.md` | lenti di ideazione, rubrica di valutazione, esempi |
| `observability-and-instrumentation` | `observability-checklist.md` | lista at-a-glance; la baseline è [[infrastruttura/osservabilita]] |

Non spedite, con motivo: `orchestration-patterns.md` descrive persona e slash command del repository upstream, mentre qui l'orchestrazione è in `instructions.md`; `accessibility-checklist.md` non è citata da nessuna delle dodici skill.

## Decisioni architetturali

Un ADR è una pagina di `architettura/` con id `adr-NNNN-<slug>`, numero sequenziale a quattro cifre, template `adr` (`concept_new` con `template: adr`). Stati: `proposed`, `accepted`, `superseded`, `deprecated`. Un ADR non si riscrive: se la decisione cambia se ne scrive un altro e il vecchio si chiude con `supersede`, che lascia il collegamento nei due versi. L'entry nell'indice di `architettura/` va nello stesso `concept_batch` della pagina.

## Provenienza

Adattate dal ramo `main` di [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills), licenza MIT, copyright (c) 2025 Addy Osmani: la nota di provenienza è in coda a ogni `SKILL.md`. Le modifiche rispetto all'originale sono elencate nella sezione finale "In this KB" di ciascuna skill.
