---
name: debuginn-blog-cover
description: Generate a 16:9 blog hero cover with title, center logo, accent color, and glow effect. Renders a PNG locally via node-canvas and returns it to the user. Use when the user wants to create a blog post hero image, article cover, or banner with a glowing background.
---

# debuginn-blog-cover — Blog Hero Cover Generator

Use this skill when the user wants to generate a 16:9 blog hero cover image with a glowing background, title text, and optional center logo.

## Workflow

### Step 1 — Collect inputs

Ask the user in a natural conversational flow (do not dump a form):

1. **Title**: the text displayed large on the cover. Suggest one if the user is unsure (see copy guidance below).
2. **Logo / center image**: a path to an image to place in the center. This is required — ask for it explicitly.

### Step 2 — Confirm optional style preferences

Ask, or use sensible defaults:

- **Accent color**: hex color for background fill and glow. Presets: `#3a66ff` (blue, default), `#6d4aff` (violet), `#12b3a8` (teal), `#ff7a45` (orange). Custom hex accepted.
- **Glow strength**: 0–100, default `68`. Controls the radial glow radius and opacity.
- **Letter spacing (tracking)**: extra pixels between characters, default `0`.

### Step 3 — Render

The rendering logic lives remotely at `https://tools.debuginn.com/js/blog-cover-core.js`. Always fetch the latest version before rendering.

1. Create a temp directory and download the core script:

```bash
TMPDIR=$(mktemp -d) && \
curl -sL https://tools.debuginn.com/js/blog-cover-core.js -o "$TMPDIR/blog-cover-core.js"
```

2. Write the render entry script into the same temp directory:

```bash
cat > "$TMPDIR/render.js" << 'RENDER_EOF'
var { createCanvas, loadImage, registerFont } = require('canvas');
var { drawCover, normalizeHex, COVER_SIZE } = require('./blog-cover-core');
var path = require('path'), fs = require('fs');

var pingFangPath = '/System/Library/Fonts/PingFang.ttc';
if (fs.existsSync(pingFangPath)) {
  registerFont(pingFangPath, { family: 'PingFang SC' });
}

function applyDrawingQuality(c) {
  c.imageSmoothingEnabled = true;
  c.imageSmoothingQuality = 'high';
}

function parseArgs(argv) {
  var a = {};
  for (var i = 0; i < argv.length; i++) {
    if (argv[i] === '--image')      a.image      = argv[++i];
    else if (argv[i] === '--title')    a.title      = argv[++i];
    else if (argv[i] === '--accent')   a.accent     = argv[++i];
    else if (argv[i] === '--glow')     a.glow       = parseInt(argv[++i], 10);
    else if (argv[i] === '--tracking') a.tracking   = parseInt(argv[++i], 10);
    else if (argv[i] === '--output')   a.output     = argv[++i];
    else if (argv[i] === '--scale')    a.scale      = parseInt(argv[++i], 10);
  }
  return a;
}

async function main() {
  var args = parseArgs(process.argv.slice(2));
  var scale = args.scale || 2;
  var canvas = createCanvas(COVER_SIZE.width * scale, COVER_SIZE.height * scale);
  var ctx = canvas.getContext('2d');
  var img = args.image ? await loadImage(path.resolve(args.image)) : null;
  applyDrawingQuality(ctx);
  drawCover(ctx, scale, {
    title: args.title || 'BLOG',
    accentColor: args.accent || '#3a66ff',
    glowStrength: typeof args.glow === 'number' ? args.glow : 68,
    tracking: typeof args.tracking === 'number' ? args.tracking : 0,
    centerImage: img
  });
  var outPath = path.resolve(args.output || './debuginn-blog-cover-' + Date.now() + '.png');
  fs.writeFileSync(outPath, canvas.toBuffer('image/png'));
  console.log(outPath);
}

main().catch(function (e) { console.error(e); process.exit(1); });
RENDER_EOF
```

3. Ensure `canvas` is installed (skip if globally available):

```bash
cd "$TMPDIR" && npm init -y > /dev/null 2>&1 && npm install canvas > /dev/null 2>&1
```

4. Execute the render:

```bash
cd "$TMPDIR" && \
node render.js \
  --title "<title>" \
  --accent "<hex>" \
  --glow <0-100> \
  --tracking <px> \
  --image "<absolute path to logo>" \
  --output "<absolute output path>"
```

5. Clean up:

```bash
rm -rf "$TMPDIR"
```

The output is a 2x high-resolution PNG (3200x1800) saved to the specified path. Show the resulting image to the user.

### Step 4 — Iterate if needed

If the user wants to adjust title, colors, glow, or logo, re-run from Step 3 with updated parameters. Always re-download core.js to stay in sync with the latest website version.

## Copy Guidance

When suggesting title text:

- **Title**: 1–3 words; short, bold, impactful
- Prefer English words like "BLOG", "CODE", "DESIGN", "NOTES" or Chinese equivalents
- Avoid long sentences

## References

- Web preview: `https://tools.debuginn.com/zh/tools/debuginn-blog-cover/`
- Core JS source: `https://tools.debuginn.com/js/blog-cover-core.js`
