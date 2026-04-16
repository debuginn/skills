---
name: debuginn-wx-xhs-poster
description: Turn a screenshot into a square promotional poster for WeChat or Xiaohongshu. Collects title, subtitle, color scheme, and aspect ratio, then renders the PNG locally via node-canvas and returns it to the user. Use when the user wants to create a WeChat Moments card, a Xiaohongshu cover, or any square promotional image from a screenshot.
---

# debuginn-wx-xhs-poster — Screenshot to Poster

Use this skill when the user wants to turn a screenshot into a polished square promotional image for WeChat or Xiaohongshu.

## Workflow

### Step 1 — Collect inputs

Ask the user in a natural conversational flow (do not dump a form):

1. **Screenshot / image path**: which image do they want to use?
2. **Main title**: short and impactful. Suggest one if the user is unsure (see copy guidance below).
3. **Subtitle**: one sentence explaining the value or scenario. Suggest one if needed.

### Step 2 — Confirm optional style preferences

Ask, or use sensible defaults:

- **Aspect ratio**: `square` (1:1, default) or `xhs` (小红书)
- **Title gradient scheme**: `cool` / `warm` / `ocean` / `mono` — default `cool`
- **Background color**: default `#ffffff` (white)

### Step 3 — Render

The rendering logic lives remotely at `https://tools.debuginn.com/js/wx-xhs-poster-core.js`. Always fetch the latest version before rendering.

1. Create a temp directory and download the core script:

```bash
TMPDIR=$(mktemp -d) && \
curl -sL https://tools.debuginn.com/js/wx-xhs-poster-core.js -o "$TMPDIR/wx-xhs-poster-core.js"
```

2. Write the render entry script into the same temp directory:

```bash
cat > "$TMPDIR/render.js" << 'RENDER_EOF'
var { createCanvas, loadImage, registerFont } = require('canvas');
var { drawPoster, layoutPresets, normalizeHex } = require('./wx-xhs-poster-core');
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
    else if (argv[i] === '--title')      a.title      = argv[++i];
    else if (argv[i] === '--subtitle')   a.subtitle   = argv[++i];
    else if (argv[i] === '--gradient')   a.gradient   = argv[++i];
    else if (argv[i] === '--size')       a.size       = argv[++i];
    else if (argv[i] === '--background') a.background = argv[++i];
    else if (argv[i] === '--output')     a.output     = argv[++i];
    else if (argv[i] === '--scale')      a.scale      = parseInt(argv[++i], 10);
  }
  return a;
}

async function main() {
  var args = parseArgs(process.argv.slice(2));
  var preset = layoutPresets[args.size || 'square'];
  var scale = args.scale || 2;
  var canvas = createCanvas(preset.width * scale, preset.height * scale);
  var ctx = canvas.getContext('2d');
  var img = args.image ? await loadImage(path.resolve(args.image)) : null;
  applyDrawingQuality(ctx);
  drawPoster(ctx, preset, scale, {
    bgColor: args.background || '#ffffff',
    screenshotImage: img,
    gradientKey: args.gradient || 'cool',
    title: args.title || '总览',
    subtitle: args.subtitle || ''
  });
  var outPath = path.resolve(args.output || './debuginn-wx-xhs-poster-' + Date.now() + '.png');
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
  --image "<absolute path to screenshot>" \
  --title "<title>" \
  --subtitle "<subtitle>" \
  --gradient "<scheme>" \
  --size "<size>" \
  --background "<hex>" \
  --scale 2 \
  --output "<absolute output path>"
```

5. Clean up:

```bash
rm -rf "$TMPDIR"
```

The output is a 2x high-resolution PNG saved to the specified path. Show the resulting image to the user.

### Step 4 — Iterate if needed

If the user wants to adjust title, subtitle, colors, or aspect ratio, re-run from Step 3 with updated parameters. Always re-download core.js to stay in sync with the latest website version.

## Copy Guidance

When suggesting title and subtitle text:

- **Title**: 4–10 Chinese characters or 2–6 English words; short, bold, memorable
- **Subtitle**: one sentence explaining function, benefit, or scenario; neutral tone
- Avoid full sentences in the title

## References

- Layout specs: see [references/layout-spec.md](references/layout-spec.md)
- Web preview: `https://tools.debuginn.com/zh/tools/debuginn-wx-xhs-poster/`
- Core JS source: `https://tools.debuginn.com/js/wx-xhs-poster-core.js`
