---
name: wliq-brand
description: >
  Apply White Label IQ (WLIQ) brand guidelines ONLY when the user explicitly instructs it.
  Trigger this skill ONLY when the user directly says phrases like: "apply WLIQ branding",
  "use the WLIQ brand guidelines", "make it on-brand for WLIQ", "follow WLIQ brand",
  "use our brand guidelines", or "apply the brand skill". Do NOT trigger this skill
  automatically for general design, coding, document, or content tasks — even if they
  involve WLIQ as a topic. Only activate when there is a clear, explicit instruction to
  apply WLIQ brand standards to the output.
---

# WLIQ Brand Guidelines Skill

This skill ensures every WLIQ output is pixel-perfect to the official brand guidelines.
Before generating any branded output, read the relevant section from the references below.

## Reference Files

- `references/colors.md` — Full color palette with HEX, RGB, CMYK, and usage context
- `references/typography.md` — Font system, sizes, weights, and line heights
- `references/logo.md` — Logo rules, clear space, sizing, variations, wrong usage
- `references/web.md` — Website-specific design system (grid, buttons, components)
- `references/social.md` — Social post specs, layout system, fold usage, post types
- `references/print.md` — Print material specs, margins, bleed, typography hierarchy
- `references/illustration.md` — Illustration and graphic style guidelines (read for all output types)

---

## Color Hierarchy (Critical — Apply Everywhere)

The primary brand identity is **black and white**. Orange is a rare accent only.

| Priority | Role | HEX | Usage |
|---|---|---|---|
| 1 | Primary Black | #000000 | Headings, backgrounds, logo on light |
| 1 | Primary White | #FFFFFF | Text on dark, logo on dark |
| 2 | Near Black | #1D1D20 | Dark section/page backgrounds |
| 2 | Dark Gray | #333333 | Body copy on light backgrounds |
| 2 | Mid Gray | #777777 | Secondary text, captions, dividers |
| 2 | Light Gray | #ABABAB | Body copy on dark backgrounds |
| 2 | Off White | #F7F6F5 | Light section backgrounds |
| Accent | Orange | #F37022 | Hyperlinks, one key CTA, accent line only — SPARINGLY |

**Orange (#F37022) rules:**
- Never use as background color
- Never use on more than one element per page/slide
- Use ONLY for: hyperlinks, a single accent rule/divider, or one CTA button
- Preferred alternative accent: white rule or #777777 divider lines

---

## Official WLIQ Logo SVG

**Always use this SVG code** when placing the WLIQ logo. Never type "White Label IQ" as text. This is the **white version** for dark backgrounds.

```svg
<?xml version="1.0" encoding="UTF-8"?>
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
</svg>
```

**For light backgrounds:** Change `fill: #fff` to `fill: #000000` in the `<style>` block.

**Logo rules:**
- Minimum digital height: 34px | Minimum print height: 0.35 inch
- Always maintain clear space equal to the height of the IQ mark on all sides
- Never stretch, rotate, recolor, or add effects
- Never type the brand name as plain text — always use the SVG

---

## Cover Page — When to Use + Layout Rules

### When a cover page IS required
- Process documents, SOPs, policies
- Plans (project plan, roadmap, strategic plan)
- Reports, white papers
- Proposals, scope of work documents
- Any formal exportable document for stakeholder or client distribution

### When a cover page is NOT used
- Web layouts, dashboards, HTML artifacts
- Social media graphics
- Presentations (use a title slide instead)
- Quick internal notes or email drafts

---

### Cover Page — Internal Documents (SOPs, Policies, Plans, Process Docs)

Dark background. Always.

```
Background:     #000000 (full bleed)
Illustration:   One-line SVG art (topic-relevant), white/grey stroke, top-right area
                Stroke: 1–1.5px, color #FFFFFF or #777777

Text block (center-left):
  Category label — Avenir Heavy, #ABABAB, 14–16pt, UPPERCASE + letter-spacing
  Document Title — Avenir Black, #FFFFFF, 48–60pt
  Subtitle       — Avenir Medium, #777777, 18–20pt
  Date/version   — Avenir Roman, #777777, 14pt

Accent:         Optional single thin #F37022 rule (1pt), one only — or omit
Logo:           WLIQ SVG white version, bottom-left or bottom-right, 0.35 inch min
```

---

### Cover Page — Client-Facing Documents (Proposals, Reports, White Papers)

Light background. Per brand guide default.

```
Background:     #FFFFFF or #F7F6F5
Illustration:   Colorful flat character illustration, bottom-right or right side
                Contextually relevant to document topic
                Muted/natural tones — greens, blues, warm skin tones, orange accents

Text block (left/top area):
  Category label — Avenir Heavy, #777777, 14–16pt, UPPERCASE
  Document Title — Avenir Black, #000000, 48–60pt
  Subtitle       — Avenir Medium, #777777, 18–20pt
  Date/version   — Avenir Roman, #ABABAB, 14pt

Logo:           WLIQ SVG black version, bottom-right corner, 0.35 inch min
```

---

## Inner Pages — Document / PDF

- Background: always `#FFFFFF` or `#F7F6F5` (light — for both internal and client docs)
- Section dividers: `#EEEEEE` or `#777777` rules, 0.5pt
- Running header (optional): thin `#000000` bar, document title in `#ABABAB`
- One-line art: 0.5pt stroke, `#333333`, integrated alongside text columns or as watermark at 10–15% opacity

### Typography Hierarchy
| Level | Font | Size | Color |
|---|---|---|---|
| Page Title / H1 | Avenir Black | 28–36px | #000000 |
| Section Header / H2 | Avenir Heavy | 22–26px | #000000 |
| Subsection / H3 | Avenir Heavy | 18–20px | #333333 |
| Body Copy | Avenir Medium | 14–16px | #333333 |
| Captions / Labels | Avenir Roman | 12–13px | #777777 |
| Hyperlinks | Avenir Medium | body size | #F37022 |

### Orange Usage on Inner Pages
- Hyperlinks: always `#F37022`
- Maximum one additional orange element per page (e.g., left border on a callout box)
- Never as background, fill, or repeated decoration

---

## Line Art Stroke Specs — By Context

| Context | Background | Stroke | Color |
|---|---|---|---|
| Website | Light | 2px | #333333 |
| Website | Dark | 2px | #FFFFFF |
| Mobile | Light | 1px | #333333 |
| Mobile | Dark | 1px | #FFFFFF |
| Socials | Light | 2px | #333333 |
| Socials | Dark | 2px | #777777 |
| Print (light bg) | Light | 0.5pt | CMYK 0 0 0 80 |
| Print (dark bg) | Dark | 0.5pt | CMYK 0 0 0 53 |

Read `references/illustration.md` for full illustration style guidance per output type.

---

## Presentations / Slides

- No separate cover page — use a **title slide** instead
- Title slide: `#000000` or `#1D1D20` bg, `#FFFFFF` title, WLIQ logo SVG bottom-right
- Dark slides: `#000000` bg, `#FFFFFF` titles, `#ABABAB` body
- Light slides: `#FFFFFF` or `#F7F6F5` bg, `#000000` titles, `#333333` body
- `#F37022`: one element per slide max (divider line, one icon, or one CTA)
- Section divider slides: full `#000000` background with white section title + optional one-line art
- Line art on slides: 2px stroke, `#FFFFFF` on dark / `#333333` on light

---

## Web Layouts & HTML Artifacts

- No cover page
- Grid: 12 columns, 92.5px columns, 30px gutters, 240px margins (desktop)
- Buttons: `#F37022` background, `#FFFFFF` text, Avenir Medium, 54px height
- Hyperlinks: default `#000000`, hover `#F37022`
- Dark sections: `#000000` or `#1D1D20` bg, `#FFFFFF` / `#ABABAB` text
- Light sections: `#FFFFFF` or `#F7F6F5` bg, `#000000` / `#333333` text
- Line art: **2px stroke**, `#333333` (light bg) or `#FFFFFF` (dark bg) — continuous one-line style

---

## Social Media Posts

Read `references/social.md` for full specs. Quick reference:

**LinkedIn:**
- Style A: Pastel bg, full-color flat character illustration, minimal text
- Style B: White bg, bold Avenir Black headline + one-line figure art (2px, #333333)

**Instagram:**
- Black bg (#000000)
- White one-line continuous art, 2px stroke, #FFFFFF
- Bold Avenir Black headline, white
- High contrast, typographically dominant

---

## Brand Voice & Positioning

- **Audience**: Small and mid-sized marketing agencies across the US
- **Positioning**: The expert white-label execution partner — design, dev, SaaS, PPC
- **Tone**: Professional, confident, agency-savvy, no fluff
- **Tagline direction**: "Born from an Agency. Made for an Agency."

---

## What NOT to Do

- Never use orange (#F37022) as a background or as a repeated element
- Never place more than one orange accent per page/slide/section
- Never type "White Label IQ" as plain text — always use the SVG logo
- Never use a colorized, opacity-reduced, or recolored logo
- Never stretch, rotate, or add effects to the logo
- Never use fonts other than Avenir (fallback: `'Avenir', 'Nunito', sans-serif`)
- Never use colors outside the defined palette
- Never simulate brand colors with opacity/transparency — use the defined gray shades
- Never use wrong stroke weight — 2px web, 1px mobile, 0.5pt print
- Never add a cover page to web layouts, dashboards, social graphics, or presentations
- Never use colorful flat illustrations on inner pages — one-line art only
