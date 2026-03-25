---
title: Papers Dashboard
tags:
  - Research
  - papers-dashboard
date: 2026-03-09
last_updated: 2026-03-25
---
# Papers Dashboard

#Research

Central index for paper-study notes in `Research/`.

## Catalog (Dataview)

```dataview
TABLE WITHOUT ID link(source_pdf_local, title) as "Paper", date as "Note Date", source_web as "Source", file.link as "Note"
FROM "Research"
WHERE contains(tags, "paper-notes")
SORT date desc
```

## Maintenance Rule

- Keep required paper-note metadata: `title`, `tags`, `date`, `source_web`, `source_pdf_local`, `source`.
- Keep `#Research` in each paper note body.
- Wrap important concepts in `[[...]]` inside each paper note.
- Keep this dashboard Dataview-driven so new paper notes appear automatically via metadata.
