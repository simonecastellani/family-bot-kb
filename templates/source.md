---
type: Source
title: "{{title}}"
x-template.description: One primary source the knowledge base has absorbed, with its ingest status
x-template.required_fields: [source_kind, ingest_status]
x-template.optional_fields: [locator, sha256, timestamp, ingested_at, source_shape, connector, recipe]
x-template.field_values.ingest_status: [pending, ingested, skipped]
x-template.field_values.source_shape: [document, collection, feed, system]
x-template.open_sections: true
---
# {{title}}
