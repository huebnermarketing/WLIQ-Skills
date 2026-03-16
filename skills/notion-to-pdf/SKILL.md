---
name: notion-to-pdf
description: >
  Converts any Notion page into a polished, WLIQ-branded PDF document. Trigger this skill
  whenever a user shares a Notion page URL and asks to export, save, convert, or generate
  a PDF from it — especially for policies, SOPs, process docs, plans, guidelines, handbooks,
  or reference material. Trigger phrases include: "make a PDF of this Notion page",
  "export this to PDF", "convert this Notion doc to PDF", "generate a WLIQ PDF from this",
  "turn this into a document", "save this as a PDF", or any time the user pastes a
  notion.so URL alongside intent to produce a document. Also trigger when the user says
  "notion to pdf", "export notion", or "PDF from notion". Always fetch live from Notion —
  never rely on pasted content alone. Restructure content blocks for readability without
  changing intent, meaning, or any sensitive data.
---

# Notion → WLIQ PDF Skill

Fetches a Notion page, intelligently restructures its content blocks for document layout,
and renders a polished WLIQ-branded PDF using ReportLab.

---

## Step 1 — Extract the Notion Page ID

The user will share a Notion URL. Extract the page ID from it.

**URL formats:**
```
https://www.notion.so/Page-Title-<PAGE_ID>
https://www.notion.so/<workspace>/<PAGE_ID>
https://notion.so/<PAGE_ID>
```
The page ID is the last 32-character alphanumeric string (with or without hyphens).

---

## Step 2 — Fetch the Page Content

Use `Notion:notion-fetch` with the extracted page ID.

```
Notion:notion-fetch → { id: "<page_id>" }
```

If the page has sub-pages or linked databases referenced, note them but do not auto-follow
unless the user explicitly asks. Capture:
- Page title
- All top-level blocks (headings, paragraphs, lists, tables, callouts, toggles, dividers)
- Any inline properties (owner, date, status, tags) from the page header

**If fetch fails:** Ask the user to confirm the page is shared with the Notion integration.

---

## Step 3 — Analyse & Restructure Content

Before writing any code, **plan the document structure in chat** — show the user a brief
outline so they can confirm or adjust before PDF generation begins.

### Content Restructuring Rules

| Notion Block Type | PDF Treatment | Restructuring Guidance |
|---|---|---|
| Page title | Cover page headline | Use as-is |
| Page properties (owner, date, status) | Cover subtitle / metadata block | Summarise as key-value pairs |
| H1 | Section break + H2 treatment | Treat as major section |
| H2 | Section header with grey rule | Use as-is |
| H3 | Subsection heading | Use as-is |
| Paragraph | Body text | Preserve, light cleanup only (fix double spaces, orphan line breaks) |
| Bulleted list | Indented bullet paragraphs | Group consecutive bullets under nearest heading |
| Numbered list | Numbered steps | Preserve numbering; wrap long items |
| Toggle | Expandable → flatten to H3 + body | Expand all toggle content into document |
| Callout / quote | Styled callout box | Preserve callout text exactly |
| Table | Full-width styled table | First row = header; preserve all cell values |
| Divider | Section page-break or spacer | Use spacer (not always a page break) |
| Code block | Monospace code block | Light grey background, Courier font |
| Image | Inline image if accessible | Attempt embed; note if unavailable |
| Sub-page link | Reference note | Note as "See: [page title]" callout |
| Database / linked view | Summary note | Note as "See Notion for live data" callout |

### Restructuring Principles
- **Never change intent** — reorder only for layout clarity, not to alter meaning
- **Never redact sensitive data** — preserve all figures, names, dates, IDs, costs as-is
- **Logical grouping** — if sibling paragraphs under a heading belong together, keep them together
- **Toggle flattening** — all toggle blocks must be expanded; no content should be hidden in PDF
- **Empty sections** — if a section heading has no body, include it with a placeholder note: *"No content added."*
- **Long tables** — split across pages with `repeatRows=1`; never truncate rows

---

## Step 4 — Confirm Outline with User

Present a compact outline before generating:

```
📄 **Document outline — does this look right?**

Cover: <page title>
Subtitle: <inferred doc type, e.g. "WLIQ Process Document">
Date: <today's date>

Sections:
1. <H2 heading>
2. <H2 heading>
   2.1 <H3>
3. <H2 heading>
   [Table]
...

Ready to generate? Or any adjustments?
```

Wait for a green light (or adjustments) before proceeding.

---

## Step 5 — Generate the PDF

Use `ReportLab` with WLIQ brand standards. Read `references/pdf-template.md` for the full
boilerplate before writing any code.

### Additional elements beyond base template

#### Code Block Style
```python
from reportlab.platypus import Preformatted
from reportlab.lib.styles import ParagraphStyle

code_style = ParagraphStyle(
    "code",
    fontName="Courier",
    fontSize=8,
    leading=12,
    textColor=C_BODY,
    backColor=colors.HexColor("#F5F5F5"),
    leftIndent=12,
    rightIndent=8,
    spaceBefore=4,
    spaceAfter=4,
)
# Use: Paragraph(escaped_code_text, code_style)
```

#### Metadata Block (Page Properties)
Render page-level properties (owner, date, version, status) as a two-column key-value
table immediately after the cover, before the first content section:

```python
meta_data = [["Property", "Value"]]
for key, val in page_properties.items():
    meta_data.append([key, str(val)])
build_table(meta_data, col_widths=[CONTENT_W * 0.3, CONTENT_W * 0.7])
```

#### Table of Contents
Auto-generate if the document has **3 or more H2 sections**. List section title + dotted
leader line + page number. Use `Paragraph` with tab stops for alignment.

#### "Last Updated" Footer Note
If the Notion page has a `last_edited_time` property, include it in the footer alongside
the page number: `"White Label IQ — Confidential | Last updated: <date>"`

---

## Step 6 — Save and Deliver

1. Slug = page title → lowercase, spaces to hyphens, strip special chars
   e.g. `employee-onboarding-policy-wliq.pdf`
2. Save to `/mnt/user-data/outputs/<slug>.pdf`
3. Call `present_files` with the output path
4. In chat: `"Here's your PDF — exported from Notion with WLIQ branding."`

Do NOT re-summarise content after delivery.

---

## Edge Cases

| Situation | Handling |
|---|---|
| Page is a database (not a doc) | Ask user: "This looks like a Notion database, not a document page. Would you like a summary table PDF instead?" |
| Page has no headings at all | Treat entire content as one section titled after the page title |
| Page is very short (<200 words) | Generate PDF anyway; cover + single content page is fine |
| Page contains only images | Attempt to embed; warn user if images can't be fetched |
| Nested sub-pages linked | Include a "Related Documents" section at the end listing sub-page titles |
| Page has password / not shared | Inform user: "The Notion integration can't access this page. Please share it with the WLIQ Notion integration first." |

---

## Quality Checklist

- [ ] Content sourced entirely from Notion — nothing invented
- [ ] All toggles expanded, no hidden content
- [ ] Sensitive data (names, figures, IDs) preserved exactly
- [ ] Cover page: dark `#000000` bg, white title, subtitle, category label, date, WLIQ SVG logo
- [ ] TOC present if 3+ sections
- [ ] Every H2 has thin `#777777` rule + bold heading (NOT orange)
- [ ] Tables fully rendered, no truncated rows
- [ ] Footer on all content pages
- [ ] Orange (#F37022) used only for hyperlinks — not section rules, not backgrounds
- [ ] `category_label` passed to `build_pdf()` (e.g. "SOP", "POLICY DOCUMENT", "PROJECT PLAN")
- [ ] File saved to `/mnt/user-data/outputs/`

---

## Reference Files

- `references/pdf-template.md` — Full ReportLab boilerplate with WLIQ styles, cover,
  table builder, callout builder, footer — **read before writing any PDF code**
