# How to use this KB

Reply in **English** unless the operator writes in another language.

## Status convention

- `active` means the page is valid and current, not that work is open.
- `open`: work accepted or queued, not started.
- `in-progress`: execution has started; set it in the same write that records the first progress.
- `blocked`: progress cannot continue; declare `waiting_on` and `review_after`.
- A finished task is `done`; a reference that stays valid is `reference`.

## Write discipline

- Act on every finding a write returns: fix or acknowledge it before moving on.
- Run `gate_check` at the end of a session and resolve what it reports.
- Every write is a git commit, revertible. Close sessions with `log_append`.

## Skills and where their artifacts live

The KB ships thirteen skills, adapted from addyosmani/agent-skills: `code-review-and-quality`, `code-simplification`, `context-engineering`, `documentation-and-adrs`, `doubt-driven-development`, `idea-refine`, `impeccable`, `incremental-implementation`, `interview-me`, `source-driven-development`, `spec-driven-development`, `test-driven-development`, `observability-and-instrumentation`.

Their outputs are KB knowledge, never a documentation directory inside the repository:

- architecture decisions: `architettura/adr-NNNN-<slug>` with the `adr` template; a replaced decision is linked with `supersede`;
- ideas and intents: `progetto/idea-<slug>` and `progetto/intento-<slug>`;
- specs and plans: the milestone page `progetto/m<n>-<slug>`;
- sources: `sources/` with `source_register`; test and review outcomes: `log_append`.

The full table is the page `sviluppo/skill-di-sviluppo`. The reply-language rule above is unchanged.
