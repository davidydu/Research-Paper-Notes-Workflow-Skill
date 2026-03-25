---
name: paper-notes-workflow
description: "Create standardized Obsidian paper-study notes from PDF or arXiv inputs, including full figure extraction and in-note placement. Use when a user asks to study a paper, summarize it section-by-section, create/update paper notes in Research, or extract and place all paper figures. Enforce required metadata (title, tags, date, source_web, source_pdf_local, source), include #Research, wikilink key concepts, include every figure in the note, and update the papers dashboard."
---

# Paper Notes Workflow

Use this workflow as the default baseline, then adapt format/depth to the user request.

## 1) Ingest Paper Under Research/tmp/pdfs

- Resolve a stable paper id from the URL or filename (for arXiv, use the arXiv id).
- Store working files in `Research/tmp/pdfs/`.
- If only an arXiv abstract URL is provided (e.g., `https://arxiv.org/abs/<id>`), derive the PDF URL as `https://arxiv.org/pdf/<id>`.
- Download the PDF to `Research/tmp/pdfs/<paper-id>.pdf`.
- Extract text with `pdftotext` to `Research/tmp/pdfs/<paper-id>.txt` for section mapping and note writing.
- Optionally run `pdfinfo` (metadata check) and `pdftoppm` (visual page checks) when structure/figures matter.

## 2) Capture Essential Metadata

Collect these fields before writing the note:

- `title`: official paper title.
- `date`: note creation date in `YYYY-MM-DD`.
- `tags`: include `Research` and `paper-notes` at minimum.
- `source_web`: canonical web URL (arXiv abs link or publisher URL).
- `source_pdf_local`: local vault path to the downloaded PDF (for example `Research/tmp/pdfs/<paper-id>.pdf`).
- `source`: include both links as a list (web + local PDF path) for compatibility.

Collect these fields when available:

- paper version (for arXiv, e.g., `v1`),
- submission/publication date,
- authors,
- venue/category.

## 3) Extract Figures and Build a Figure Map

Create figure assets under `Research/tmp/pdfs/<paper-id>_figures/`:

- `raw_png/`: direct `pdfimages -png` output.
- `paper_figures/`: cleaned figure files used by the note.
- `page_renders/`: page PNG renders for cropping non-embedded figures.

Workflow:

- Run `pdfimages -list Research/tmp/pdfs/<paper-id>.pdf` and retain only rows with `type=image` (exclude `smask` rows).
- Extract all images with `pdfimages -png` into `raw_png/`.
- Copy only true `type=image` streams into `paper_figures/`.
- Name files predictably, e.g., `figure-<NN>-p<page>-n<num>.png` when figure number is known.
- Detect expected figure numbers by scanning text for `Figure <N> |` in the extracted paper text.
- If some figures are missing from embedded images (common for appendix prompt-box figures), render the source pages with `pdftoppm -png` and crop the exact regions into `paper_figures/` as `figure-<NN>-p<page>-crop.png`.
- Visually verify cropped figures before embedding them.

Rule:

- Include every figure found in the paper (`Figure 1` through `Figure Nmax`).
- Do not skip appendix figures.

## 4) Create Note in Research Folder

- Default filename: `<Paper Title> - Paper Notes.md`.
- Place the note directly under `Research/` unless user requests a subfolder.
- Use frontmatter first, then note content.
- Ensure `#Research` appears in the note body near the top.

Use the template in `references/paper-note-template.md`.

## 5) Write Comprehensive Section Notes

- Produce notes for each major paper section in order.
- Separate claims that are evidence-backed from forecasts/opinions when relevant.
- Preserve concrete numbers and dates from the paper when present.
- Wrap key concepts in `[[...]]` (methods, metrics, bottlenecks, paradigms, systems, policy ideas).
- Keep writing study-oriented: clear, structured, and scannable.
- Add a `## Study Answer Key` section at the very end that answers each `## Study Checklist` question.
- In `## Study Answer Key`, repeat each checklist question verbatim as a numbered subheading (`### 1) <question>`) before its answer.

## 6) Place Figures in the Correct Note Locations

- Embed figures with Obsidian syntax: `![[Research/tmp/pdfs/<paper-id>_figures/paper_figures/<filename>|<width>]]`.
- Add a one-line italic caption directly below each embed (`*Figure N. ...*`).
- Place each main-text figure in the matching section where it is discussed.
- Place appendix figures in an appendix-focused area (for example, an appendix figure panel under appendix highlights).
- Keep figure numbering explicit and consistent (`Figure 1`, `Figure 2`, ...).

Coverage check:

- Confirm all figure numbers detected in step 3 are present in the note exactly once.
- If a figure is unreadable or cannot be isolated, document that explicitly in the note instead of silently omitting it.

## 7) Update Papers Dashboard

- Maintain the existing papers dashboard note.
- Prefer this resolution order:
  - `Research/Papers Dashboard.md`
  - `Research/_ Papers Dashboard.md`
- Keep a Dataview-only catalog (no manual table).
- Ensure the `Paper` column links to `source_pdf_local`.
- Keep the dashboard sorted newest-first (by note date).

## 8) Quality Gates

Before finishing, verify:

- Note includes required frontmatter: `title`, `tags`, `date`, `source_web`, `source_pdf_local`, and `source`.
- Note body includes `#Research`.
- Important concepts are wikilinked with `[[...]]`.
- All major sections from the paper are covered.
- Every figure in the paper is included and placed in a relevant location in the note.
- Figure embeds resolve to files under `Research/tmp/pdfs/<paper-id>_figures/paper_figures/`.
- Dashboard includes the paper note.
- `## Study Answer Key` is present at the end and answers every checklist item.
