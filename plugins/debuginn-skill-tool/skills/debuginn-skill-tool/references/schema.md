# Hugo Theme Skills — Front Matter Schema Reference

## Content File Front Matter

| Field | Type | Required | Default | Notes |
|-------|------|----------|---------|-------|
| `title` | string | Yes | — | Display name on card and page header |
| `slug` | string | No | File slug | URL-safe kebab-case identifier |
| `desc` | string | No | `.Summary` | 1–2 sentences shown on card |
| `toolType` | string | No | `""` | Must match `tool-controls-{toolType}.html` partial name |
| `toolCSS` | string | No | — | e.g. `/css/{slug}.css` — required when toolType is set |
| `toolJS` | string | No | — | e.g. `/js/{slug}.js` — required when toolType is set |
| `eyebrow` | string | No | — | Terminal-style label above title, e.g. `# > Image Tools` |
| `cardPath` | string | No | `$ pwd: ~/tools/{slug}` | Terminal-style path on card |
| `cardIcon` | string (SVG) | No | Type-based | Inline SVG string rendered as card badge |
| `installCode` | string | No | — | Multi-line bash/plugin commands; enables card flip + install modal |
| `tags` | []string | No | `[]` | Tag chips shown on card |
| `weight` | int | No | `0` | Sort order — lower numbers appear first |

## toolType Values

| Value | Canvas Size | Controls Partial |
|-------|------------|-----------------|
| `""` (empty) | No canvas | None — info-only page |
| `blog-cover` | 1600 × 900 | `tool-controls-blog-cover.html` |
| `wx-xhs-poster` | 1080 × 1080 | `tool-controls-wx-xhs-poster.html` |
| `{custom-slug}` | Defined in JS | `tool-controls-{custom-slug}.html` |

## Control ID Conventions

| Control | HTML id | JS state key |
|---------|---------|-------------|
| Image upload | `imageInput` | `imageSrc` |
| Title text | `titleInput` | `title` |
| Subtitle text | `subtitleInput` | `subtitle` |
| Accent color | `accentInput` | `accent` |
| Background color | `bgInput` | `bg` |
| Glow intensity | `glowInput` | `glow` |
| Letter-spacing | `trackingInput` | `tracking` |
| Size selector | `sizeSelect` | `size` |
| Gradient scheme | `gradientSelect` | `gradient` |

## JavaScript File Structure

```
(function () {
  "use strict";
  const STATE_KEY = "debuginn_{slug}_state";   // hyphens → underscores
  const CANVAS_W  = /* number */;
  const CANVAS_H  = /* number */;

  const state = { /* all mutable values with defaults */ };

  // Utilities: normalizeHex, hexToRgba, fitContain, roundedRect, wrapText
  // Draw functions: one per visual layer (drawBackground, drawImage, drawTitle, …)
  // render(): save state → clear canvas → call draw*()
  // sync*Buttons(): keep .is-active class in sync
  // Event listeners: one per control
  // Download: canvas.toDataURL("image/png"), filename = "{slug}-{Date.now()}.png"
  // Init: restore from localStorage → attach listeners → render()
})();
```

## CSS File Structure

```css
/* ── Tokens (dark, default) ── */
:root {
  --bg: …;
  --panel: …;
  --text: …;
  --accent: …;
}

/* ── Light theme override ── */
html[data-theme="light"] {
  --bg: …;
  --panel: …;
  --text: …;
  --accent: …;
}

/* ── Color swatches (if presets used) ── */
.scheme-swatch-blue   { --swatch: #3b82f6; }
.scheme-swatch-violet { --swatch: #8b5cf6; }

/* ── Component styles ── */
/* Reuse theme classes: .field, .scheme-grid, .scheme-btn, .canvas-shell */
```
