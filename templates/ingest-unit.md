---
type: IngestUnit
title: "{{title}}"
x-template.description: One claimable unit of ingest work: a source, the concepts it feeds, what to take and how to check it
x-template.required_fields: [source, targets, extract, verify, executor, status]
x-template.optional_fields: [plan, claimed_by, claimed_at, review_after, waiting_on, timestamp]
x-template.field_values.status: [draft, open, in-progress, done, skipped, blocked]
x-template.field_values.executor: [any, large, human]
x-template.open_sections: true
---
# {{title}}

Write here the brief an executor needs without reading the plan.
