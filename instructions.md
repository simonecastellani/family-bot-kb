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
