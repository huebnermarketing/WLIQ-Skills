# WLIQ PDF Template — ReportLab Boilerplate

Full copy-paste-ready boilerplate for generating WLIQ-branded PDFs from wiki content.
Read this file before writing any PDF generation code.

---

## Brand Rules (Critical — Read Before Coding)

- **Cover page is always dark** — `#000000` full-bleed background, white/grey text only
- **Orange (#F37022) is accent-only** — use for hyperlinks and maximum one accent element per page. Never as a background, thick bar, or repeated element
- **Primary black is `#000000`**, not `#000000`
- **Logo is always the official SVG** — never draw "WHITE LABEL IQ" as plain text
- **Inner pages are always light** — `#FFFFFF` or `#F7F6F5` background only
- **Section headers (H2)** use a thin `#777777` rule, not an orange bar

---

## Complete Boilerplate

```python
import os
from reportlab.lib.pagesizes import letter
from reportlab.lib.units import inch
from reportlab.lib import colors
from reportlab.lib.styles import ParagraphStyle, getSampleStyleSheet
from reportlab.platypus import (
    SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle,
    PageBreak, HRFlowable, KeepTogether
)
from reportlab.platypus.flowables import Flowable
from reportlab.graphics import renderPDF
from reportlab.graphics.shapes import Drawing, Path
from svglib.svglib import svg2rlg
from io import BytesIO
from datetime import date

# ─── Brand Constants ────────────────────────────────────────────────────────
C_PRIMARY   = colors.HexColor("#000000")   # Primary black — headings, logo on light
C_WHITE     = colors.HexColor("#FFFFFF")   # White — text/logo on dark
C_NEAR_BLK  = colors.HexColor("#1D1D20")  # Near black — dark section backgrounds
C_BODY      = colors.HexColor("#333333")   # Body copy on light backgrounds
C_SECONDARY = colors.HexColor("#777777")   # Secondary text, captions, dividers
C_MUTED     = colors.HexColor("#ABABAB")   # Body copy on dark backgrounds / captions
C_LIGHT_BG  = colors.HexColor("#F7F6F5")  # Light section backgrounds
C_BORDER    = colors.HexColor("#EEEEEE")   # Row borders
C_ORANGE    = colors.HexColor("#F37022")   # Accent only — hyperlinks, one CTA max

# Legacy alias (keep for backward compat)
C_BLACK    = C_PRIMARY
C_DARK_BG  = C_NEAR_BLK
C_CALLOUT  = C_LIGHT_BG   # Callout boxes use off-white, not orange-tinted

PAGE_W, PAGE_H = letter
MARGIN = 0.75 * inch
CONTENT_W = PAGE_W - 2 * MARGIN

# ─── Official WLIQ Logo SVG (white version — for dark backgrounds) ──────────
WLIQ_LOGO_SVG_WHITE = '''<?xml version="1.0" encoding="UTF-8"?>
<svg id="Layer_1" xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="0 0 1000 300">
  <defs><style>.st0 { fill: #fff; }</style></defs>
  <g>
    <path class="st0" d="M92.64,118.06h14.92l9.85,39.68h.28l12.95-39.68h12.66l12.95,40.81h.28l10.41-40.81h13.79l-18.01,61.91h-12.1l-13.51-42.78h-.28l-13.51,43.06h-12.38l-18.29-62.2Z"/>
    <path class="st0" d="M188.32,118.06h13.51v23.08h26.45v-23.08h13.51v61.91h-13.51v-26.74h-26.45v26.74h-13.51v-61.91Z"/>
    <path class="st0" d="M256.43,118.06h13.51v61.91h-13.51v-61.91Z"/>
    <path class="st0" d="M296.39,130.16h-17.73v-12.1h48.97v12.1h-17.73v49.81h-13.51c0,.28,0-49.81,0-49.81Z"/>
    <path class="st0" d="M336.91,118.06h42.21v12.66h-28.71v11.54h27.02v12.66h-27.02v12.66h30.11v12.66h-43.62v-62.2h0Z"/>
    <path class="st0" d="M421.34,118.06h13.79v49.25h25.33v12.66h-38.84l-.28-61.91h0Z"/>
    <path class="st0" d="M490.01,118.06h11.26l27.02,61.91h-15.48l-5.35-13.23h-24.2l-5.07,13.23h-15.2l27.02-61.91ZM495.36,136.07l-7.6,19.14h15.2l-7.6-19.14Z"/>
    <path class="st0" d="M536.44,118.06h23.08c10.98,0,22.8,2.25,22.8,15.76,0,7.04-4.22,11.54-10.69,13.51v.28c8.16,1.13,13.51,7.04,13.51,15.2,0,12.95-12.38,17.17-23.36,17.17h-25.33v-61.91h0ZM549.95,142.82h9.85c4.22,0,8.72-1.69,8.72-6.75s-4.78-6.47-9.29-6.47h-9.29v13.23ZM549.95,168.71h12.38c4.22,0,9.01-1.97,9.01-7.32,0-5.91-6.47-6.75-10.98-6.75h-10.41v14.07h0Z"/>
    <path class="st0" d="M595.26,118.06h42.21v12.66h-28.42v11.54h27.02v12.66h-27.02v12.66h30.11v12.66h-43.62l-.28-62.2h0Z"/>
    <path class="st0" d="M652.39,118.06h13.79v49.25h25.33v12.66h-38.84l-.28-61.91h0Z"/>
  </g>
  <path class="st0" d="M725.84,75v150h110.04v-37.71h36.3v-112.29h-146.34ZM773.69,170.97h-10.41v-49.81h10.41v49.81ZM836.16,170.97h-28.71c-14.63,0-25.61-9.57-25.61-25.61,0-15.2,11.26-25.61,25.33-25.61s25.33,10.69,25.33,25.61c0,7.04-2.81,14.07-9.29,16.89h13.23l-.28,8.72h0ZM807.18,130.16c-7.88,0-14.35,6.75-14.35,15.48s6.47,15.48,14.35,15.48,14.35-6.75,14.35-15.48-6.47-15.48-14.35-15.48Z"/>
  <g>
    <path class="st0" d="M883.44,77.53h-3.94v-2.53h10.69v2.53h-3.94v10.69h-2.81v-10.69h0Z"/>
    <path class="st0" d="M892.17,75h4.5l3.1,8.72h0l3.1-8.72h4.5v13.51h-2.81v-10.41h0l-3.66,10.13h-2.25l-3.1-10.13h0v10.13h-2.81v-13.23h-.56Z"/>
  </g>
  <polygon class="st0" points="841.23 221.62 869.93 192.92 841.23 192.92 841.23 221.62"/>
</svg>'''


def draw_wliq_logo(canvas, x, y, height=28):
    """
    Render the official WLIQ SVG logo at position (x, y) with given height.
    Uses svglib to convert SVG to ReportLab drawing.
    Falls back to styled text if svglib is unavailable.
    """
    try:
        from svglib.svglib import svg2rlg
        from reportlab.graphics import renderPDF
        from io import BytesIO
        import tempfile, os

        with tempfile.NamedTemporaryFile(suffix=".svg", delete=False, mode='w') as f:
            f.write(WLIQ_LOGO_SVG_WHITE)
            tmp_path = f.name

        drawing = svg2rlg(tmp_path)
        os.unlink(tmp_path)

        if drawing:
            # Scale to desired height
            scale = height / drawing.height
            drawing.width *= scale
            drawing.height *= scale
            drawing.transform = (scale, 0, 0, scale, 0, 0)
            renderPDF.draw(drawing, canvas, x, y)
            return

    except Exception:
        pass

    # Fallback: styled text (only if svglib unavailable)
    canvas.setFont("Helvetica-Bold", 9)
    canvas.setFillColor(C_WHITE)
    canvas.drawString(x, y, "WHITE LABEL IQ")


# ─── Styles ─────────────────────────────────────────────────────────────────
def get_styles():
    styles = getSampleStyleSheet()
    base = dict(fontName="Helvetica", textColor=C_BODY, leading=14)

    return {
        "h1": ParagraphStyle("h1", fontName="Helvetica-Bold", fontSize=20,
                              textColor=C_WHITE, leading=26, spaceAfter=6),
        "h2": ParagraphStyle("h2", fontName="Helvetica-Bold", fontSize=14,
                              textColor=C_PRIMARY, leading=18, spaceBefore=16, spaceAfter=6),
        "h3": ParagraphStyle("h3", fontName="Helvetica-Bold", fontSize=11,
                              textColor=C_BODY, leading=15, spaceBefore=10, spaceAfter=4),
        "body": ParagraphStyle("body", fontSize=10, leading=14, spaceAfter=6, **base),
        "bullet": ParagraphStyle("bullet", fontSize=10, leading=14, leftIndent=16,
                                  bulletIndent=6, spaceAfter=3, **base),
        "callout": ParagraphStyle("callout", fontName="Helvetica-Oblique", fontSize=9,
                                   textColor=C_BODY, leading=13, leftIndent=12, rightIndent=8),
        "footer": ParagraphStyle("footer", fontName="Helvetica", fontSize=8,
                                  textColor=C_MUTED, leading=10),
        "toc": ParagraphStyle("toc", fontSize=10, leading=14, textColor=C_BODY,
                               fontName="Helvetica"),
        "subtitle": ParagraphStyle("subtitle", fontName="Helvetica", fontSize=12,
                                    textColor=C_MUTED, leading=16),
        "label": ParagraphStyle("label", fontName="Helvetica-Bold", fontSize=9,
                                  textColor=C_MUTED, leading=12, spaceAfter=2),
    }


# ─── Cover Page ──────────────────────────────────────────────────────────────
def build_cover(canvas, doc, title, subtitle="WLIQ Document", category_label=None):
    """
    Fixed dark cover page layout — always #000000 background.
    - Top-right: decorative line art area (left as canvas space for SVG art if added)
    - Center-left: category label → title → subtitle → date
    - Optional: single thin #F37022 rule (1pt) above title
    - Bottom: WLIQ SVG logo
    """
    canvas.saveState()

    # ── Full-bleed dark background
    canvas.setFillColor(C_PRIMARY)
    canvas.rect(0, 0, PAGE_W, PAGE_H, fill=True, stroke=False)

    # ── Category label (e.g. "PROCESS DOCUMENT" / "WLIQ POLICY")
    label_y = PAGE_H * 0.56
    if category_label:
        canvas.setFont("Helvetica-Bold", 9)
        canvas.setFillColor(C_MUTED)
        canvas.drawString(MARGIN, label_y, category_label.upper())
        label_y -= 8

    # ── Optional single thin orange accent rule (1pt) — subtle, not dominant
    canvas.setStrokeColor(C_ORANGE)
    canvas.setLineWidth(1)
    canvas.line(MARGIN, label_y - 4, MARGIN + 80, label_y - 4)

    # ── Document title
    title_y = label_y - 40
    canvas.setFillColor(C_WHITE)
    canvas.setFont("Helvetica-Bold", 32)
    # Word-wrap manually for long titles
    words = title.split()
    line, lines = [], []
    for word in words:
        test = " ".join(line + [word])
        if canvas.stringWidth(test, "Helvetica-Bold", 32) > CONTENT_W:
            lines.append(" ".join(line))
            line = [word]
        else:
            line.append(word)
    if line:
        lines.append(" ".join(line))
    for i, ln in enumerate(lines[:3]):   # max 3 lines
        canvas.drawString(MARGIN, title_y - (i * 40), ln)

    # ── Subtitle
    subtitle_y = title_y - (len(lines) * 40) - 16
    canvas.setFont("Helvetica", 13)
    canvas.setFillColor(C_SECONDARY)
    canvas.drawString(MARGIN, subtitle_y, subtitle)

    # ── Date
    today = date.today().strftime("%B %d, %Y")
    canvas.setFont("Helvetica", 9)
    canvas.setFillColor(C_SECONDARY)
    canvas.drawString(MARGIN, subtitle_y - 20, today)

    # ── WLIQ Logo (bottom-right, white SVG version)
    logo_h = 28
    logo_x = PAGE_W - MARGIN - 300   # approx width of logo at this height
    logo_y = MARGIN
    draw_wliq_logo(canvas, logo_x, logo_y, height=logo_h)

    canvas.restoreState()


# ─── Footer (all content pages) ─────────────────────────────────────────────
def make_footer_handler(doc_title):
    def footer(canvas, doc):
        if doc.page == 1:
            return  # No footer on cover
        canvas.saveState()
        y = MARGIN * 0.55
        # Top border line
        canvas.setStrokeColor(C_BORDER)
        canvas.setLineWidth(0.5)
        canvas.line(MARGIN, y + 14, PAGE_W - MARGIN, y + 14)
        canvas.setFont("Helvetica", 8)
        canvas.setFillColor(C_MUTED)
        canvas.drawString(MARGIN, y, "White Label IQ — Confidential")
        canvas.drawCentredString(PAGE_W / 2, y, doc_title[:60])
        canvas.drawRightString(PAGE_W - MARGIN, y, f"Page {doc.page}")
        canvas.restoreState()
    return footer


# ─── Section Header (H2) ─────────────────────────────────────────────────────
def section_header(title, styles):
    """
    Returns a list of flowables: thin grey rule + bold heading.
    Uses #777777 divider — NOT orange. Orange is reserved for hyperlinks/CTAs only.
    """
    return [
        Spacer(1, 10),
        HRFlowable(width=CONTENT_W, thickness=0.75, color=C_SECONDARY, spaceAfter=4),
        Paragraph(title, styles["h2"]),
    ]


# ─── Callout Box ─────────────────────────────────────────────────────────────
class CalloutBox(Flowable):
    """
    Left-border callout box for notes/blockquotes.
    Uses #000000 left border on off-white (#F7F6F5) background.
    Orange left border used sparingly — pass accent=True only when necessary.
    """
    def __init__(self, text, width=None, accent=False):
        super().__init__()
        self.text = text
        self.box_width = width or CONTENT_W
        self.accent = accent  # True = orange border, False = #000000 border

    def wrap(self, avail_w, avail_h):
        self.width = self.box_width
        self.height = max(36, len(self.text) // 6 + 36)
        return self.width, self.height

    def draw(self):
        c = self.canv
        # Background — off-white, neutral
        c.setFillColor(C_LIGHT_BG)
        c.roundRect(0, 0, self.width, self.height, 4, fill=True, stroke=False)
        # Left accent border — #000000 by default, orange only if accent=True
        border_color = C_ORANGE if self.accent else C_PRIMARY
        c.setFillColor(border_color)
        c.rect(0, 0, 3, self.height, fill=True, stroke=False)
        # Text
        c.setFillColor(C_BODY)
        c.setFont("Helvetica-Oblique", 9)
        c.drawString(14, self.height - 16, self.text[:120])
        if len(self.text) > 120:
            c.drawString(14, self.height - 28, self.text[120:240])


# ─── Table Builder ───────────────────────────────────────────────────────────
def build_table(data, col_widths=None):
    """
    data: list of lists — first row is header.
    col_widths: list of floats in points. If None, auto-distributed.
    Header background: #000000 | Header text: white | Alt rows: #F7F6F5
    """
    if not col_widths:
        n_cols = len(data[0]) if data else 1
        col_widths = [CONTENT_W / n_cols] * n_cols

    table = Table(data, colWidths=col_widths, repeatRows=1)
    n_rows = len(data)

    style = TableStyle([
        # Header row
        ("BACKGROUND", (0, 0), (-1, 0), C_PRIMARY),
        ("TEXTCOLOR",  (0, 0), (-1, 0), C_WHITE),
        ("FONTNAME",   (0, 0), (-1, 0), "Helvetica-Bold"),
        ("FONTSIZE",   (0, 0), (-1, 0), 9),
        ("TOPPADDING", (0, 0), (-1, 0), 6),
        ("BOTTOMPADDING", (0, 0), (-1, 0), 6),
        # Body rows
        ("FONTNAME",   (0, 1), (-1, -1), "Helvetica"),
        ("FONTSIZE",   (0, 1), (-1, -1), 9),
        ("TEXTCOLOR",  (0, 1), (-1, -1), C_BODY),
        ("TOPPADDING", (0, 1), (-1, -1), 5),
        ("BOTTOMPADDING", (0, 1), (-1, -1), 5),
        ("LEFTPADDING", (0, 0), (-1, -1), 8),
        ("RIGHTPADDING", (0, 0), (-1, -1), 8),
        # Alternating row bg
        *[("BACKGROUND", (0, i), (-1, i), C_LIGHT_BG)
          for i in range(2, n_rows, 2)],
        # Grid
        ("GRID", (0, 0), (-1, -1), 0.5, C_BORDER),
        ("VALIGN", (0, 0), (-1, -1), "MIDDLE"),
    ])
    table.setStyle(style)
    return table


# ─── Main Builder ─────────────────────────────────────────────────────────────
def build_pdf(output_path, title, subtitle, sections, category_label=None):
    """
    sections: list of dicts, each with:
      - "heading": str (H2 title, or None for no heading)
      - "content": list of flowables
    category_label: optional string shown above title on cover (e.g. "PROCESS DOCUMENT")
    """
    styles = get_styles()
    footer_fn = make_footer_handler(title)

    doc = SimpleDocTemplate(
        output_path,
        pagesize=letter,
        leftMargin=MARGIN,
        rightMargin=MARGIN,
        topMargin=MARGIN,
        bottomMargin=MARGIN,
        title=title,
        author="White Label IQ",
    )

    story = []

    # ── Cover (rendered via canvas callback on page 1) ──
    story.append(Spacer(1, PAGE_H - 2 * MARGIN))
    story.append(PageBreak())

    # ── Content pages ──
    for sec in sections:
        if sec.get("heading"):
            story.extend(section_header(sec["heading"], styles))
        story.extend(sec["content"])

    def on_page(canvas, doc):
        if doc.page == 1:
            build_cover(canvas, doc, title, subtitle, category_label)
        footer_fn(canvas, doc)

    doc.build(story, onFirstPage=on_page, onLaterPages=on_page)
    return output_path
```

---

## Usage Pattern

```python
# Example: Employee Onboarding Policy → PDF
sections = [
    {
        "heading": "Overview",
        "content": [
            Paragraph("This document outlines WLIQ's onboarding process...", styles["body"]),
        ]
    },
    {
        "heading": "Week 1 Checklist",
        "content": [
            build_table([
                ["Task", "Owner", "Due"],
                ["Set up accounts", "IT", "Day 1"],
                ["Complete HR forms", "HR", "Day 1"],
                ["Meet the team", "Manager", "Day 2"],
            ])
        ]
    },
    {
        "heading": "Important Note",
        "content": [
            # Default border (#000000) — use accent=True only for critical warnings
            CalloutBox("All onboarding tasks must be completed within the first week."),
        ]
    },
]

build_pdf(
    output_path="/mnt/user-data/outputs/employee-onboarding-wliq.pdf",
    title="Employee Onboarding Policy",
    subtitle="WLIQ People Operations",
    category_label="POLICY DOCUMENT",
    sections=sections,
)
```

---

## Dependencies

```bash
pip install reportlab svglib --break-system-packages
```

`svglib` is required for rendering the WLIQ logo SVG. If unavailable, `draw_wliq_logo()` falls back to styled text automatically.

---

## Common Pitfalls

- Never use Unicode subscripts/superscripts — use `<sub>` and `<super>` in Paragraph XML
- `Paragraph` content must escape `&`, `<`, `>` → use `&amp;`, `&lt;`, `&gt;`
- For very long tables, set `repeatRows=1` so the header repeats across pages
- Always wrap `KeepTogether([header, first_paragraph])` so section headers don't orphan
- Orange (#F37022) should appear in code only for: hyperlinks, `CalloutBox(accent=True)` for critical notes, or a single CTA. Never for section headers or backgrounds
- Cover `category_label` helps Claude infer document type (e.g. "SOP", "POLICY DOCUMENT", "PROJECT PLAN") — always pass it
