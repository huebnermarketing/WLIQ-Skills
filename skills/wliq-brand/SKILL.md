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
- `references/illustration.md` — Illustration and graphic style guidelines

## Quick Reference — Most Commonly Needed

### Brand Colors at a Glance
| Role | HEX | Use |
|---|---|---|
| Primary Black | #000000 | Headings, logo, backgrounds |
| Primary White | #FFFFFF | Headings, logo, backgrounds |
| **Accent Orange** | **#F37022** | Buttons, hover states, highlights |
| Dark BG | #1D1D20 | Dark section backgrounds |
| Body (light bg) | #333333 | Body copy, menu items |
| Body (dark bg) | #ABABAB | Body copy on dark |
| Border | #EEEEEE | Row borders |
| Light BG | #F7F6F5 | Section backgrounds |

### Typography at a Glance
- **Font Family**: Avenir (Black, Heavy, Medium, Roman)
- **H1**: Avenir Black, 75px / 85px line height
- **H2**: Avenir Black, 65px / 75px
- **H3**: Avenir Heavy, 40px / 50px
- **H4–H6**: Avenir Medium, 22–30px
- **Body P1**: Avenir Medium, 24px / 34px
- **Body P2**: Avenir Medium, 20px / 28px
- **Body P4 (small)**: Avenir Roman, 16px / 24px
- **Buttons**: Avenir Medium, height 54px, color #F37022

### Logo Rules (Critical)
- Only use **pure black (#000000) or pure white (#FFFFFF)** — never colors, never opacity
- Never stretch, rotate, add shadow, or reposition the IQ icon
- Minimum digital size: **34px** height | Minimum print size: **0.35 inch**
- Always maintain clear space around the logo equal to the height of the IQ icon
- Logo on dark bg: white version | Logo on light bg: black version

## Workflow by Output Type

### When creating a **Website component or HTML artifact**:
1. Read `references/web.md` and `references/colors.md`
2. Use Avenir (or system fallback: `'Avenir', 'Nunito', sans-serif`)
3. Apply the desktop grid: 12 columns, 92.5px columns, 30px gutters, 240px margins
4. Buttons: #F37022 background, white text, Avenir Medium, 54px height
5. Hyperlinks: default #000000, hover #F37022, Avenir Medium 16–18px
6. Line drawings: 2px stroke, #333333 (light bg) or #FFFFFF (dark bg)

### When creating a **Social media post**:
1. Read `references/social.md`
2. Post size: **1080px × 1350px**, margin: 100px, logo height: 60px
3. Light post: white bg, black title, #333333 copy, 2px #333333 line art
4. Dark post: black bg, white title, #ABABAB copy, 2px #777777 line art
5. Carousel posts: add WLIQ icon fold (100px × 100px, bottom-right corner)
6. Logo position options: Top Center, Top Left, Bottom Left, Bottom Right

### When creating a **Document or PDF**:
1. Read `references/print.md`
2. Page: US Letter (8.5" × 11"), Margin: 0.75"/0.50", Bleed: 0.125"
3. Logo height in print: 0.35 inch minimum
4. Title: Avenir Black | SubTitle: Avenir Heavy | Body: Avenir Heavy/Roman
5. Line art: 0.5pt stroke, CMYK 0 0 0 80 (light) or CMYK 0 0 0 53 (dark)

### When creating a **Presentation**:
1. Use WLIQ colors + Avenir typography system
2. Dark slides: #000000 or #1D1D20 background, white text, #F37022 accents
3. Light slides: #FFFFFF or #F7F6F5 background, black text, #F37022 accents
4. One-line hand-drawn illustrations as section graphics (2px stroke)

## Illustration Style
- **Website sections**: Hand-drawn one-line drawings, minimal, monochromatic
- **Blog covers**: Colorful flat illustrations with pastel backgrounds
- **Social (LinkedIn)**: Pastel background full-color illustrations OR black/white with one colored accent
- **Social (Instagram)**: Black background with white one-line art, bold typography
- **Print covers**: Colorful flat illustrations with WLIQ logo bottom-right

## Brand Voice & Positioning
- **Audience**: Small and mid-sized marketing agencies across the US
- **Positioning**: The expert white-label execution partner — design, dev, SaaS, PPC
- **Tone**: Professional, confident, agency-savvy, no fluff
- **Mission**: Empower agencies to focus on strategy by handling execution
- **Tagline direction**: "Born from an Agency. Made for an Agency."

## What NOT to Do
- ❌ Never use random colors or brand colors at reduced opacity
- ❌ Never use a colorized version of the logo
- ❌ Never stretch or skew the logo
- ❌ Never add drop shadows or effects to the logo
- ❌ Never angle or rotate the logo
- ❌ Never reposition the IQ icon relative to the "WHITE LABEL" wordmark
- ❌ Never use fonts other than Avenir for branded materials
- ❌ Never use the pink/magenta color seen in the Core Values section — that was placeholder styling
