# WLIQ Logo Guidelines

## Logo Anatomy
The WLIQ logo has three components:
1. **Brand Type**: "WHITE LABEL" — large, bold, all-caps wordmark (Avenir Black)
2. **Brand Icon**: Black square containing "IQ" text with a fold/tab detail in the bottom-right corner
3. **Trademark**: ™ symbol positioned top-right of the Brand Icon

The Brand Icon always appears to the RIGHT of the wordmark.

## Approved Color Versions

| Background | Logo Version |
|---|---|
| White / Light | Full black logo (wordmark black, icon black square with white IQ) |
| Black / Dark | Full white logo (wordmark white, icon white square with black IQ) |

**The icon also exists standalone** (IQ icon only, without wordmark):
- On black background: white square with black IQ
- On white background: black square with white IQ
- Standalone icon minimum size: **100px**

## Size Requirements

| Format | Minimum Size |
|---|---|
| Print (full logo) | 0.35 inch height |
| Digital (full logo) | 34px height |
| Digital (icon only) | 100px |
| Desktop nav | 46px height |
| Mobile nav | 34px height |
| Social posts | 60px height |
| Print materials | 0.35 inch height |

## Clear Space
Always maintain clear space around the logo equal to **at least the height of the Brand Icon** on all sides.
Never crowd the logo with other text, graphics, or edges.

## Logo Placement on Social Posts
Logo can be placed at:
- Artboard Top Center
- Artboard Top Left (with margin)
- Artboard Bottom Left
- Artboard Bottom Right

Social post margin: **100px** on all sides.

## Wrong Usage — NEVER Do These

| Rule | Violation |
|---|---|
| No opacity | Don't reduce logo to 50%, 25%, or any opacity |
| No shades | Don't use gray versions of the logo |
| No random colors | Don't apply orange, pink, blue, or any color to the logo |
| No stretched logo | Don't change the aspect ratio |
| No repositioned icon | The IQ icon must always be to the right of "WHITE LABEL" |
| No icon below wordmark | Don't stack the icon above or below |
| No shadows | Don't add drop shadows, glows, or emboss effects |
| No rotation | Don't angle or tilt the logo at any degree |
| No outlined logo | Don't use a stroked/outlined version |

## CSS/SVG Implementation Notes
When rendering the logo in code:
```css
/* Always preserve aspect ratio */
.wliq-logo {
  height: 46px; /* desktop nav */
  width: auto;  /* never set both width and height */
}

/* Mobile */
.wliq-logo-mobile {
  height: 34px;
  width: auto;
}
```
