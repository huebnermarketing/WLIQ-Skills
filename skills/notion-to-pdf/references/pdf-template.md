# WLIQ PDF Template — ReportLab Boilerplate

Full copy-paste-ready boilerplate for generating WLIQ-branded PDFs from wiki content.
Read this file before writing any PDF generation code.

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
from datetime import date

# ─── Brand Constants ────────────────────────────────────────────────────────
C_BLACK    = colors.HexColor("#000000")
C_WHITE    = colors.HexColor("#FFFFFF")
C_ORANGE   = colors.HexColor("#F37022")
C_DARK_BG  = colors.HexColor("#1D1D20")
C_BODY     = colors.HexColor("#333333")
C_MUTED    = colors.HexColor("#ABABAB")
C_LIGHT_BG = colors.HexColor("#F7F6F5")
C_BORDER   = colors.HexColor("#EEEEEE")
C_CALLOUT  = colors.HexColor("#FFF4EC")

PAGE_W, PAGE_H = letter
MARGIN = 0.75 * inch
CONTENT_W = PAGE_W - 2 * MARGIN

# ─── Styles ─────────────────────────────────────────────────────────────────
def get_styles():
    styles = getSampleStyleSheet()
    base = dict(fontName="Helvetica", textColor=C_BODY, leading=14)

    return {
        "h1": ParagraphStyle("h1", fontName="Helvetica-Bold", fontSize=20,
                              textColor=C_WHITE, leading=26, spaceAfter=6),
        "h2": ParagraphStyle("h2", fontName="Helvetica-Bold", fontSize=14,
                              textColor=C_DARK_BG, leading=18, spaceBefore=16, spaceAfter=6),
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
    }


# ─── Cover Page ──────────────────────────────────────────────────────────────
def build_cover(canvas, doc, title, subtitle="WLIQ Knowledge Base"):
    canvas.saveState()
    # Dark background
    canvas.setFillColor(C_DARK_BG)
    canvas.rect(0, 0, PAGE_W, PAGE_H, fill=True, stroke=False)
    # Orange accent bar
    canvas.setFillColor(C_ORANGE)
    canvas.rect(MARGIN, PAGE_H * 0.48, CONTENT_W, 3, fill=True, stroke=False)
    # Logo text (top-left)
    canvas.setFillColor(C_WHITE)
    canvas.setFont("Helvetica-Bold", 11)
    canvas.drawString(MARGIN, PAGE_H - MARGIN - 10, "WHITE LABEL IQ")
    # Title
    canvas.setFont("Helvetica-Bold", 28)
    # Word-wrap title manually if long
    max_w = CONTENT_W
    title_y = PAGE_H * 0.52
    canvas.setFillColor(C_WHITE)
    canvas.drawString(MARGIN, title_y, title[:55])  # truncate safety
    if len(title) > 55:
        canvas.drawString(MARGIN, title_y - 34, title[55:110])
    # Subtitle
    canvas.setFont("Helvetica", 13)
    canvas.setFillColor(C_MUTED)
    canvas.drawString(MARGIN, PAGE_H * 0.44, subtitle)
    # Date (bottom right)
    canvas.setFont("Helvetica", 9)
    canvas.setFillColor(C_MUTED)
    today = date.today().strftime("%B %d, %Y")
    canvas.drawRightString(PAGE_W - MARGIN, MARGIN, today)
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
    """Returns a list of flowables: orange rule + bold heading."""
    return [
        Spacer(1, 10),
        HRFlowable(width=CONTENT_W, thickness=2, color=C_ORANGE, spaceAfter=4),
        Paragraph(title, styles["h2"]),
    ]


# ─── Callout Box ─────────────────────────────────────────────────────────────
class CalloutBox(Flowable):
    """Orange-accent left-border callout box for notes/blockquotes."""
    def __init__(self, text, width=None):
        super().__init__()
        self.text = text
        self.box_width = width or CONTENT_W

    def wrap(self, avail_w, avail_h):
        self.width = self.box_width
        self.height = max(36, len(self.text) // 6 + 36)
        return self.width, self.height

    def draw(self):
        c = self.canv
        # Background
        c.setFillColor(C_CALLOUT)
        c.roundRect(0, 0, self.width, self.height, 4, fill=True, stroke=False)
        # Left accent border
        c.setFillColor(C_ORANGE)
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
    """
    if not col_widths:
        n_cols = len(data[0]) if data else 1
        col_widths = [CONTENT_W / n_cols] * n_cols

    table = Table(data, colWidths=col_widths, repeatRows=1)
    n_rows = len(data)

    style = TableStyle([
        # Header row
        ("BACKGROUND", (0, 0), (-1, 0), C_DARK_BG),
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
def build_pdf(output_path, title, subtitle, sections):
    """
    sections: list of dicts, each with:
      - "heading": str (H2 title, or None for no heading)
      - "content": list of flowables
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
    # Use a dummy flowable to trigger cover on page 1
    story.append(Spacer(1, PAGE_H - 2 * MARGIN))
    story.append(PageBreak())

    # ── Content pages ──
    for sec in sections:
        if sec.get("heading"):
            story.extend(section_header(sec["heading"], styles))
        story.extend(sec["content"])

    def on_page(canvas, doc):
        if doc.page == 1:
            build_cover(canvas, doc, title, subtitle)
        footer_fn(canvas, doc)

    doc.build(story, onFirstPage=on_page, onLaterPages=on_page)
    return output_path
```

---

## Usage Pattern

```python
# Example: WordPress Maintenance → PDF
sections = [
    {
        "heading": "Plan Comparison",
        "content": [
            build_table([
                ["Feature", "Starter", "Essential", "Growth", "Premium"],
                ["Monthly Price", "$99", "$199", "$369", "$999+"],
                # ... more rows
            ])
        ]
    },
    {
        "heading": "What's Not Included",
        "content": [
            Paragraph("The following are quoted separately:", styles["body"]),
            Paragraph("• PHP version upgrades", styles["bullet"]),
            Paragraph("• Theme updates", styles["bullet"]),
        ]
    },
    {
        "heading": "Process & Timeline",
        "content": [
            Paragraph("1. Backup on Staging", styles["h3"]),
            Paragraph("Fresh backup of live site restored on staging...", styles["body"]),
            CalloutBox("Typical cycle: 2–4 business days from staging to deployment."),
        ]
    },
]

build_pdf(
    output_path="/mnt/user-data/outputs/wordpress-maintenance-wliq.pdf",
    title="WordPress Maintenance Plans",
    subtitle="WLIQ Service Documentation",
    sections=sections,
)
```

---

## Common Pitfalls

- Never use Unicode subscripts/superscripts — use `<sub>` and `<super>` in Paragraph XML.
- `Paragraph` content must escape `&`, `<`, `>` → use `&amp;`, `&lt;`, `&gt;`.
- For very long tables, set `repeatRows=1` so the header repeats across pages.
- Always wrap `KeepTogether([header, first_paragraph])` so section headers don't orphan.
- Install deps if missing: `pip install reportlab --break-system-packages`
