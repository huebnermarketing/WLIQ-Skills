---
name: wiki-pdf-export
description: >
  Intercepts wiki knowledge base responses and offers to export them as a branded WLIQ PDF.
  Trigger this skill whenever a user searches or reads content from the WLIQ Notion wiki
  (WLIQ WIKI, WLIQ WIKI - 2026, or any wiki page) and the result is either:
  (a) longer than ~300 words / spans multiple sections, OR
  (b) contains structured data like tables, plans, pricing, SOPs, or step-by-step processes.
  After presenting the wiki answer in chat, proactively ask the user: "This is detailed content
  — would you like me to export this as a branded WLIQ PDF?" If they agree, generate the PDF
  immediately using WLIQ brand standards. Also trigger when the user says things like
  "export this as PDF", "make a PDF of this wiki page", "generate a PDF from the wiki",
  or "save this as a document". Always use only the content fetched from Notion — never
  hallucinate or fill in details not present in the source.
---

# Wiki → WLIQ PDF Export Skill

Turns Notion wiki content into polished, WLIQ-branded PDFs. Triggered after displaying
lengthy or structured wiki content in chat.

---

## Step 1 — Detect Export Opportunity

After returning a wiki answer, evaluate:

- **Word count > ~300 words**, OR
- **Contains**: tables, pricing tiers, SOPs, numbered processes, policy lists, plan comparisons

If either condition is met, append this prompt at the end of your chat response:

> 📄 **This is detailed content — would you like me to export it as a branded WLIQ PDF?**

Wait for user confirmation before proceeding.

---

## Step 2 — Re-fetch Source Content (if needed)

Before generating the PDF, ensure you have the **complete and accurate** Notion page content.
If the chat response was truncated or summarised, re-fetch the original page using:

```
Notion:notion-fetch → use the page ID from the earlier search result
```

**CRITICAL content rules:**
- Use ONLY content returned from Notion. Never invent, infer, or extrapolate.
- If a section is incomplete or ambiguous in the source, note it as-is. Do not fill gaps.
- Preserve all tables, lists, pricing figures, and process steps exactly as authored.
- Do not reorder sections — follow the original page structure.

---

## Step 3 — Plan the PDF Structure

Map the Notion page content to a document structure before writing code:

| Notion Element | PDF Treatment |
|---|---|
| Page title | Cover page headline |
| H2 headings | Section headers (bold, #F37022 accent rule) |
| H3 subheadings | Subsection headers |
| Tables | Full-width styled tables with header row in dark bg |
| Bullet/numbered lists | Indented list paragraphs |
| Blockquotes / callouts | Styled callout box (light orange bg, left border) |
| Process steps (1,2,3…) | Numbered step blocks |
| "Not included" sections | Warning-style box |

---

## Step 4 — Generate the PDF (ReportLab)

Use `reportlab` with WLIQ brand standards. Refer to `references/pdf-template.md` for the
full boilerplate — read it before writing code.

### Brand constants to always use:

```python
# Colors
BLACK       = "#000000"
WHITE       = "#FFFFFF"
ORANGE      = "#F37022"   # Accent — section rules, table headers, callout borders
DARK_BG     = "#1D1D20"   # Cover background, table header rows
BODY_DARK   = "#333333"   # Body copy
LIGHT_BG    = "#F7F6F5"   # Alternate row tint, callout backgrounds
BORDER      = "#EEEEEE"   # Row borders, dividers

# Typography (use Helvetica as Avenir fallback in ReportLab)
FONT_BOLD   = "Helvetica-Bold"      # → Avenir Black/Heavy
FONT_MEDIUM = "Helvetica"           # → Avenir Medium
FONT_LIGHT  = "Helvetica-Oblique"   # → Avenir Roman

# Page
PAGE_SIZE   = letter                # 8.5" × 11"
MARGIN_TB   = 0.75 * inch           # Top/bottom margin
MARGIN_LR   = 0.75 * inch           # Left/right margin
```

### Document structure (in order):

1. **Cover Page** — Dark bg (#1D1D20), WLIQ logo text top-left, document title centred in
   white Helvetica-Bold, subtitle line in #ABABAB, date bottom-right, orange accent line
   below title.

2. **Table of Contents** (if document has 3+ sections) — Auto-generated from H2 headings.

3. **Content Pages** — Each H2 becomes a new section with:
   - Orange (#F37022) 2pt rule above the heading
   - Section title in Helvetica-Bold 14pt #1D1D20
   - Body in Helvetica 10pt #333333, 14pt leading
   - Callout boxes for blockquotes/notes

4. **Footer** (every page except cover) — Left: "White Label IQ — Confidential",
   Centre: document title, Right: "Page X of Y", 8pt #ABABAB, thin top border #EEEEEE.

### Table styling rules:
- Header row: bg #1D1D20, text white, Helvetica-Bold 9pt
- Alternating rows: #FFFFFF and #F7F6F5
- Border: 0.5pt #EEEEEE grid lines
- Padding: 6pt vertical, 8pt horizontal

### Callout box rules (for blockquotes / notes / warnings):
- Background: #FFF4EC (light orange tint)
- Left border: 3pt solid #F37022
- Text: Helvetica-Oblique 9pt #333333
- Padding: 8pt all sides

---

## Step 5 — Save and Deliver

1. Save PDF to `/mnt/user-data/outputs/<slug>-wliq.pdf`
   - Slug = page title lowercased, spaces → hyphens, e.g. `wordpress-maintenance-wliq.pdf`
2. Call `present_files` with the output path.
3. In chat, confirm: "Here's your branded PDF — exported directly from the WLIQ wiki."
   Do NOT re-summarise the content in chat after delivering the file.

---

## Quality Checklist (run mentally before saving)

- [ ] All content sourced from Notion — nothing invented
- [ ] Cover page has title, subtitle, date
- [ ] Every H2 section has an orange rule + bold heading
- [ ] Tables are fully rendered (no truncated rows)
- [ ] Footer appears on all content pages
- [ ] File saved to `/mnt/user-data/outputs/`

---

## Reference Files

- `references/pdf-template.md` — Full ReportLab boilerplate with WLIQ styles, cover page
  builder, table builder, callout builder, footer builder — read this before writing code.
