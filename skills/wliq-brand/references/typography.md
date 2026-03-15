# WLIQ Typography System

## Font Family
**Avenir** — the sole typeface for all WLIQ branded materials.

Weights used:
- Avenir Black (heaviest — headlines)
- Avenir Heavy (subheadings, subtitles)
- Avenir Medium (body, buttons, hyperlinks)
- Avenir Roman (small body, captions)

Download: Avenir.ttc (stored in brand assets)

## Website Typography Scale

### Headings
| Level | Weight | Size | Line Height |
|---|---|---|---|
| H1 / HEADLINE 1 | Avenir Black | 75px | 85px |
| H2 / HEADLINE 2 | Avenir Black | 65px | 75px |
| H3 / Headline 3 | Avenir Heavy | 40px | 50px |
| H4 | Avenir Medium | 30px | 40px |
| H5 | Avenir Medium | 24px | 34px |
| H6 | Avenir Medium | 22px | 32px |

### Body Copy
| Level | Weight | Size | Line Height | Notes |
|---|---|---|---|---|
| P1 | Avenir Medium | 24px | 34px | Large body, intros |
| P2 | Avenir Medium | 20px | 28px | Standard body |
| P3 | Avenir Roman | 20px | 28px | Secondary body |
| P4 | Avenir Roman | 16px | 24px | Small text, captions |

### Buttons & Hyperlinks
| Element | Default Color | Hover Color | Font | Size | Height |
|---|---|---|---|---|---|
| Button (light bg) | #F37022 bg, white text | Slightly lighter orange | Avenir Medium | — | 54px |
| Button (dark bg) | White border/text | #F37022 | Avenir Medium | — | 54px |
| Hyperlink (light bg) | #000000 | #F37022 | Avenir Medium | 18px | — |
| Hyperlink (light bg small) | #000000 | #F37022 | Avenir Medium | 16px | — |
| Hyperlink (dark bg) | #ABABAB | #F37022 | Avenir Medium | 16px | — |

## Print Typography Scale

| Usage | Font |
|---|---|
| TITLE (cover, main) | Avenir Black |
| SubTitle | Avenir Heavy |
| Inner Titles | Avenir Heavy |
| Main Copy | Avenir Medium or Roman |
| Inner Copy | Avenir Roman |

## Social Typography
Social posts use bold, high-contrast type:
- **Titles**: Avenir Black, white or black depending on background
- **Subtitles**: Avenir Heavy, #777777
- **Copy**: Avenir Medium/Roman, #333333 (light) or #ABABAB (dark)

## CSS Implementation (Web)
```css
/* Font stack fallback if Avenir not available */
font-family: 'Avenir', 'Nunito', 'Century Gothic', 'Gill Sans', sans-serif;

/* H1 */
font-family: 'Avenir', sans-serif;
font-weight: 900; /* Black */
font-size: 75px;
line-height: 85px;

/* Body P2 */
font-family: 'Avenir', sans-serif;
font-weight: 500; /* Medium */
font-size: 20px;
line-height: 28px;
color: #333333;

/* Button */
font-family: 'Avenir', sans-serif;
font-weight: 500;
background-color: #F37022;
color: #FFFFFF;
height: 54px;
padding: 0 32px;
```

## Typography Rules
- Never use any font other than Avenir for branded materials
- Never use italic or decorative styles — Avenir's weights handle all hierarchy
- All-caps headlines are standard for H1 and H2 on website
- Sentence case or title case for H3 and below
