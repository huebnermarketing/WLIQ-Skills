# WLIQ Website Design System

## Colors for Web
See `colors.md` for full palette. Key web usages:
- **#000000** — Headings, subheadings, section backgrounds
- **#FFFFFF** — Headings, subheadings, section backgrounds
- **#F37022** — Buttons (default + hover), active hyperlinks
- **#1D1D20** — Dark section backgrounds
- **#333333** — Body copy and menu items (light bg)
- **#ABABAB** — Body copy and menu items (dark bg)
- **#EEEEEE** — Row borders
- **#F7F6F5** — Light section backgrounds

## Logo on Website
- Desktop nav height: **46px**
- Mobile nav height: **34px**
- Light background: black logo
- Dark background: white logo

## Desktop Grid System
- **Columns**: 12
- **Column width**: 92.5px
- **Gutter**: 30px
- **Margin (left/right)**: 240px
- Total max-width: ~1440px

```css
.container {
  max-width: 1440px;
  margin: 0 auto;
  padding: 0 240px;
  display: grid;
  grid-template-columns: repeat(12, 92.5px);
  gap: 30px;
}
```

## Buttons

### Filled Button (Primary)
- Background: #F37022
- Text: #FFFFFF, Avenir Medium
- Height: 54px
- Hover: slightly lighter orange

### Outlined Button (Dark Background)
- Border: white
- Text: white, Avenir Medium
- Height: 54px
- Hover: white fill, black text

### Hyperlinks
| Context | Default | Hover | Size |
|---|---|---|---|
| Light bg (large) | #000000 | #F37022 | 18px |
| Light bg (small) | #000000 | #F37022 | 16px |
| Dark bg | #ABABAB | #F37022 | 16px |

All hyperlinks: Avenir Medium

## Graphic / Illustration Style

### Line Drawings (One-line, hand-drawn style)
Used for section graphics and UI decoration.

| Context | Stroke Size | Stroke Color |
|---|---|---|
| Desktop light bg | 2px | #333333 |
| Desktop dark bg | 2px | #FFFFFF |
| Mobile light bg | 1px | #333333 |
| Mobile dark bg | 1px | #FFFFFF |

SVG stroke implementation:
```css
.line-drawing path {
  stroke: #333333;
  stroke-width: 2;
  fill: none;
  stroke-linecap: round;
  stroke-linejoin: round;
}
```

## Section Layout Patterns
WLIQ website uses alternating light/dark sections:
- Light: #FFFFFF or #F7F6F5 background
- Dark: #000000 or #1D1D20 background
- Sections contain: headline (H1/H2) + body copy + CTA button + one-line illustration

## Typography on Web
See `typography.md` for the full scale. Key rules:
- H1, H2: all-caps, Avenir Black
- H3 and below: title or sentence case, Avenir Heavy/Medium
- Body: Avenir Medium (P1, P2) or Roman (P3, P4)
- Buttons and nav: Avenir Medium

## Component: Typical Hero Section
```
[WLIQ Logo — 46px, top-left]

[Subheading — H3, "Born from an Agency"]
[HEADLINE — H1, "MADE FOR AN AGENCY"]
[Body copy — P2, #333333]
[CTA Button — "Why WLIQ", #F37022, 54px]

[One-line illustration — right side of hero]
```
