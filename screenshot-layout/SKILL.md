---
name: screenshot-layout
description: Create or edit iPhone screenshot promo images into a square composition with a white background, the uploaded screenshot or graphic on the left, and a main title plus subtitle on the right. Use when the user wants App Store style marketing cards, square social covers, screenshot reframing, or wants an iPhone screenshot turned into a clean title-and-subtitle layout.
---

# iPhone Screenshot Square Layout

Use this skill when the user wants a reusable workflow for turning an iPhone screenshot into a square cover image.

## Target Layout

- Canvas: `1:1` square
- Background: pure white
- Left side: uploaded screenshot, mockup, or graphic
- Right side: one strong title and one smaller subtitle
- Overall feel: clean, product-first, minimal, Apple-style

Default proportions:

- Outer padding: `72px`
- Left visual area: about `52%`
- Right text area: about `48%`
- Gap between visual and text: `40px`

## Workflow

1. Confirm or infer the main title and subtitle.
2. Keep the original screenshot readable. Do not stretch it.
3. Place the screenshot on the left inside a clean block.
4. Place the title and subtitle on the right with strong hierarchy.
5. Keep the background white unless the user explicitly wants another style.
6. Export or describe the result as a square marketing image.

## Style Rules

- Prefer black or near-black title text.
- Keep subtitle neutral gray.
- Avoid decorative shapes unless the user asks for them.
- Keep alignment simple and balanced.
- If the screenshot itself is busy, keep the typography even simpler.
- If no font is specified, use a modern sans serif with a premium feel.

## Prompt Template

When generating or editing, use a prompt close to this:

```text
Edit the uploaded iPhone screenshot into a square promotional image.
Use a pure white background.
Place the uploaded screenshot or graphic on the left side, large and clean, without distortion.
Place text on the right side:
Main title: "{TITLE}"
Subtitle: "{SUBTITLE}"
Use a minimal premium layout, strong title hierarchy, generous whitespace, and an Apple-style marketing composition.
Output should be a 1:1 square image.
```

## Missing Inputs

If the user does not provide all text, make a reasonable draft:

- Title: short and bold, usually `4-10` Chinese characters or `2-6` English words
- Subtitle: one sentence that explains the value

If the user asks for a preview first, provide a quick wireframe before generating the final prompt or asset.

## Resources

- Layout notes: see [references/layout-spec.md](references/layout-spec.md)
- Sketch preview: see [assets/sketch.svg](assets/sketch.svg)
- Interactive web tool: see [web/index.html](web/index.html)

## Web Tool Usage

Use the web tool when the user wants quick customization and visual feedback.

1. Start a local server in the skill root:
   `python3 -m http.server 8123`
2. Open:
   `http://127.0.0.1:8123/screenshot-layout/web/index.html`
3. Customize:
   - upload screenshot
   - edit title/subtitle
   - change background color
   - switch title gradient scheme
4. Export:
   - click `导出 PNG`
