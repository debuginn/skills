---
name: debuginn-skill-tool
description: Interactive builder for adding a new custom tool page to a hugo-theme-skills powered site. Use when the user wants to create a brand-new interactive tool (canvas-based image generator, form tool, etc.) that integrates with the hugo-theme-skills theme — generates all required files: content .md, Hugo controls partial, JavaScript (IIFE + canvas rendering + state persistence), and CSS (dual-theme tokens). Trigger on phrases like "新建工具"、"开发一个工具页"、"帮我做个工具"、"add a tool"、"create a new tool page".
---

# Hugo Theme Skills — 工具页开发引导

这个 skill 用于在基于 [hugo-theme-skills](https://github.com/debuginn/hugo-theme-skills) 主题的站点中，通过对话完整开发一个新的工具页。

## 背景知识

hugo-theme-skills 站点的工具页由以下文件共同构成：

| 文件 | 路径 | 作用 |
|------|------|------|
| 内容文件 | `content/tools/{slug}.md` | front matter 声明工具元信息，激活主题的工具页渲染 |
| 内容文件（中文） | `content/zh/tools/{slug}.md` | 中文版工具页，front matter 相同，title/desc 译为中文 |
| Controls Partial | `layouts/partials/tool-controls-{slug}.html` | Hugo 模板片段，定义左侧控件面板的 HTML 结构 |
| JavaScript | `static/js/{slug}.js` | 工具的全部 canvas 渲染、状态管理、事件绑定、PNG 导出逻辑 |
| CSS | `static/css/{slug}.css` | 工具专属样式（继承主题 CSS token，支持暗色/亮色双主题） |

主题的 `layouts/tools/single.html` 会根据 `toolType` 字段自动注入对应的 controls partial、加载对应 CSS/JS，并渲染固定尺寸的 `<canvas id="preview">`。

## 工作流

### Step 1 — 了解工具功能

请用户用自然语言描述这个工具要做什么，例如：
- "我想做一个工具，上传一张图片，加上标题文字，生成一张带渐变背景的封面图"
- "做一个名片生成器，输入姓名、职位、公司，导出 PNG"

根据描述推断：
- 需要哪些输入控件
- canvas 画布的用途和尺寸（横向 1600×900 还是方形 1080×1080）
- 核心渲染逻辑（背景、图片、文字、图形等图层）

### Step 2 — 收集基本信息

从对话中收集或推断（不要一次性问所有字段，按自然对话节奏逐步确认）：

- `title` — 工具显示名称，如 "Blog Cover Generator"
- `slug` — kebab-case 唯一标识，如 `my-cover-tool`；可从 title 自动推导，同时作为 `toolType` 的值
- `desc` — 卡片上的 1–2 句描述
- `eyebrow` — 可选，卡片顶部的 terminal 标签，如 `# > Image Tools`
- `tags` — 可选，如 `["Canvas API", "PNG Export"]`
- `weight` — 排序权重，默认 `0`（数字越小越靠前）
- `installCode` — 可选，安装命令；若有，主题会渲染卡片翻转动画和安装弹窗

`toolType` 始终等于 `slug`，无需单独询问。`cardPath` 默认为 `$ pwd: ~/tools/{slug}`，不需要询问除非用户要自定义。

### Step 3 — 确定控件清单

根据用户描述，整理工具需要的控件列表，并与用户确认。控件映射规则：

| 用户描述 | 控件类型 | HTML 结构 | JS 变量名 |
|---------|---------|-----------|---------|
| 图片/图标上传 | file input | `<input type="file" id="imageInput" accept="image/*">` + `<label class="upload-trigger">` | `imageInput` |
| 标题文字 | text input | `<input type="text" id="titleInput">` | `titleInput` |
| 副标题/说明文字 | text input | `<input type="text" id="subtitleInput">` | `subtitleInput` |
| 颜色 + 预设色板 | color picker + 预设按钮 | hidden `<input type="color" id="accentInput">` + `.scheme-grid` buttons with `data-accent="#hex"` | `accentInput` |
| 背景色 + 预设色板 | 同上 | hidden `<input type="color" id="bgInput">` + 预设按钮 | `bgInput` |
| 发光强度 | range slider | `<input type="range" id="glowInput" min="20" max="100">` | `glowInput` |
| 字间距 | range slider | `<input type="range" id="trackingInput" min="0" max="72">` | `trackingInput` |
| 尺寸/比例切换 | button group | hidden `<select id="sizeSelect">` + `.scheme-grid` buttons with `data-size` | `sizeSelect` |
| 渐变方案 | button group | hidden `<select id="gradientSelect">` + `.scheme-grid` buttons with `data-scheme` | `gradientSelect` |
| 普通文本输入 | text input | `<input type="text" id="{name}Input">` | `{name}Input` |
| 数字输入 | number input | `<input type="number" id="{name}Input">` | `{name}Input` |

### Step 4 — 生成全部文件

确认控件和基本信息后，生成以下所有文件。**每个文件完整生成，不留占位符。**

---

#### 4a. `layouts/partials/tool-controls-{slug}.html`

结构规范，严格遵守：

```html
{{- /* Controls for {Title} tool */ -}}

{{- /* 图片上传（如需要） */ -}}
<div class="field">
  <label for="imageInput">{{ i18n "center_logo" | default "Image" }}</label>
  <div class="upload-control">
    <input id="imageInput" class="sr-only-input" type="file" accept="image/*" />
    <label for="imageInput" class="upload-trigger">{{ i18n "choose_logo_file" | default "Choose File" }}</label>
    <span id="imageFileName" class="upload-file-name">{{ i18n "no_file" | default "No file chosen" }}</span>
  </div>
</div>

{{- /* 文字输入（如需要） */ -}}
<div class="field">
  <label for="titleInput">{{ i18n "title_label" | default "Title" }}</label>
  <input id="titleInput" type="text" value="" />
</div>

{{- /* 颜色预设（如需要） */ -}}
<div class="field">
  <label>{{ i18n "accent_color" | default "Color" }}</label>
  <input id="accentInput" class="sr-only-input" type="color" value="#3a66ff" />
  <div class="scheme-grid" aria-label="{{ i18n "accent_color" | default "Color" }}">
    <button class="scheme-btn accent-btn is-active" type="button" data-accent="#3a66ff" aria-label="Blue">
      <span class="scheme-swatch scheme-swatch-accent-blue"></span>
    </button>
    {{- /* 更多预设按钮… */ -}}
    <button id="pickAccentBtn" class="scheme-btn accent-btn accent-picker-btn" type="button" aria-label="{{ i18n "custom_color" | default "Custom" }}">
      <span id="customAccentSwatch" class="scheme-swatch scheme-swatch-custom"></span>
    </button>
  </div>
</div>

{{- /* Range slider（如需要） */ -}}
<div class="field">
  <label for="glowInput">{{ i18n "glow_intensity" | default "Glow" }}</label>
  <input id="glowInput" type="range" min="20" max="100" value="68" />
</div>
```

关键规则：
- 每个控件组用 `<div class="field">` 包裹，内含 `<label>`
- 所有用户可见字符串使用 `{{ i18n "key" | default "Fallback" }}`
- 文件上传使用 hidden input + visible `<label class="upload-trigger">` 的组合
- 颜色预设使用 `.scheme-grid` > `.scheme-btn[data-accent]` 结构
- 不需要 `<button id="downloadBtn">` —— 主题 `single.html` 已统一渲染导出按钮

---

#### 4b. `static/js/{slug}.js`

完整 IIFE 结构，**生成可运行的完整代码**：

```js
(function () {
  "use strict";

  // ── Constants ────────────────────────────────────────────────────────────
  const STATE_KEY = "debuginn_{slug_underscore}_state";  // hyphens → underscores
  const CANVAS_W  = /* 1600 for landscape, 1080 for square */;
  const CANVAS_H  = /* 900  for landscape, 1080 for square */;

  // ── DOM refs ─────────────────────────────────────────────────────────────
  const canvas = document.getElementById("preview");
  const ctx    = canvas.getContext("2d");
  // 每个控件对应一个 const，名称与 partial 中的 id 完全一致
  const titleInput   = document.getElementById("titleInput");
  // … 其他控件

  // ── State ────────────────────────────────────────────────────────────────
  // 存储所有可变值，键名与控件语义对应
  let state = {
    title:   "",
    accent:  "#3a66ff",
    glow:    68,
    // … 其他字段
    imageData: "",
    imageName: ""
  };

  // ── Utilities ────────────────────────────────────────────────────────────
  // 按需引入：normalizeHex / hexToRgba / fitContain / roundedRect / wrapText

  function normalizeHex(value) {
    const v = (value || "").trim();
    return /^#[0-9a-fA-F]{6}$/.test(v) ? v.toLowerCase() : "#3a66ff";
  }

  function hexToRgba(hex, alpha) {
    const c = normalizeHex(hex).slice(1);
    const n = parseInt(c, 16);
    return `rgba(${(n >> 16) & 255}, ${(n >> 8) & 255}, ${n & 255}, ${alpha})`;
  }

  function fitContain(srcW, srcH, dstW, dstH) {
    const r = Math.min(dstW / srcW, dstH / srcH);
    return { w: srcW * r, h: srcH * r };
  }

  // ── Draw functions ───────────────────────────────────────────────────────
  // 每个视觉图层一个函数，按渲染顺序命名：drawBackground / drawImage / drawTitle / …
  // 根据工具的实际功能完整实现每个函数

  function drawBackground() {
    ctx.clearRect(0, 0, CANVAS_W, CANVAS_H);
    // … 实际绘制逻辑
  }

  // ── Sync UI ──────────────────────────────────────────────────────────────
  // 维护按钮 is-active 状态
  function syncAccentButtons() {
    const cur = normalizeHex(state.accent);
    document.querySelectorAll(".accent-btn[data-accent]").forEach(btn => {
      btn.classList.toggle("is-active", btn.dataset.accent === cur);
    });
  }

  // ── Render pipeline ──────────────────────────────────────────────────────
  function render() {
    // 1. 从控件读取最新值到 state
    state.title  = titleInput ? titleInput.value : state.title;
    // … 其他控件

    // 2. 持久化
    try {
      localStorage.setItem(STATE_KEY, JSON.stringify(state));
    } catch (_) {}

    // 3. 调整 canvas 尺寸（仅调整显示尺寸，不改变绘制分辨率）
    canvas.width  = CANVAS_W;
    canvas.height = CANVAS_H;

    // 4. 依次调用各图层绘制函数
    drawBackground();
    // drawImage();
    // drawTitle();

    // 5. 同步 UI 状态
    syncAccentButtons();
  }

  // ── Image upload ─────────────────────────────────────────────────────────
  // 若有图片上传控件
  let uploadedImage = null;

  if (document.getElementById("imageInput")) {
    document.getElementById("imageInput").addEventListener("change", function (e) {
      const file = e.target.files[0];
      if (!file) { uploadedImage = null; render(); return; }
      const reader = new FileReader();
      reader.onload = function () {
        const img = new Image();
        img.onload = function () { uploadedImage = img; render(); };
        img.src = reader.result;
        state.imageData = reader.result;
        state.imageName = file.name;
      };
      reader.readAsDataURL(file);
    });
  }

  // ── Event listeners ──────────────────────────────────────────────────────
  // 每个控件绑定 input/change 事件，更新 state 后调用 render()
  [titleInput /*, 其他控件 */].forEach(function (el) {
    if (el) el.addEventListener("input", render);
  });

  // 颜色预设按钮
  document.querySelectorAll(".accent-btn[data-accent]").forEach(function (btn) {
    btn.addEventListener("click", function () {
      state.accent = btn.dataset.accent;
      if (document.getElementById("accentInput")) {
        document.getElementById("accentInput").value = btn.dataset.accent;
      }
      render();
    });
  });

  // ── Download ─────────────────────────────────────────────────────────────
  const downloadBtn = document.getElementById("downloadBtn");
  if (downloadBtn) {
    downloadBtn.addEventListener("click", function () {
      // 高清导出：先以 2x 分辨率绘制，再触发下载
      const scale = 2;
      const offscreen = document.createElement("canvas");
      offscreen.width  = CANVAS_W * scale;
      offscreen.height = CANVAS_H * scale;
      const offCtx = offscreen.getContext("2d");
      offCtx.scale(scale, scale);
      // … 在 offscreen 上重绘（调用与 render 相同的 draw 函数但传入 offCtx）
      const link = document.createElement("a");
      link.download = "{slug}-" + Date.now() + ".png";
      link.href = canvas.toDataURL("image/png");
      link.click();
    });
  }

  // ── Init ─────────────────────────────────────────────────────────────────
  (function init() {
    // 从 localStorage 恢复状态
    try {
      const saved = JSON.parse(localStorage.getItem(STATE_KEY) || "null");
      if (saved) {
        Object.assign(state, saved);
        if (titleInput && saved.title)   titleInput.value  = saved.title;
        if (saved.imageData) {
          const img = new Image();
          img.onload = function () { uploadedImage = img; render(); };
          img.src = saved.imageData;
        }
      }
    } catch (_) {}

    render();
  })();

})();
```

关键规则：
- Canvas id 固定为 `"preview"`，不可更改
- STATE_KEY 格式：`debuginn_{slug_with_underscores}_state`
- 不引入任何外部依赖（无 CDN、无 npm）
- 使用 ES5 兼容写法（`var`/`function` 均可，但保持风格一致）
- 导出文件名格式：`{slug}-{Date.now()}.png`

---

#### 4c. `static/css/{slug}.css`

完整 CSS，继承主题 `portal.css` 已有的 class（`.field`、`.scheme-grid`、`.scheme-btn`、`.canvas-shell`、`.upload-trigger` 等），只定义差异化的 token 和工具专属样式：

```css
/* ── {Title} — color tokens & tool-specific overrides ── */
/* Shared layout rules (shell, app, panel, field, scheme-btn, etc.)
   are defined in portal.css                                          */

:root {
  --bg: /* 深色背景 */;
  --panel: /* 面板背景 */;
  --line: /* 边框颜色 */;
  --text: /* 主文字颜色 */;
  --muted: /* 次要文字 */;
  --accent: /* 强调色 */;
  /* … 其他 token */
}

html[data-theme="light"] {
  /* 所有 token 的亮色覆盖，必须与 :root 中的 token 名称完全对应 */
  --bg: /* 亮色背景 */;
  /* … */
}

body {
  background: /* 渐变背景 */;
}

html[data-theme="light"] body {
  background: /* 亮色渐变背景 */;
}

/* ── 工具专属布局调整（如有） ── */
.shell { width: min(1180px, calc(100% - 32px)); }
.app   { grid-template-columns: minmax(320px, 380px) minmax(0, 1fr); }

/* ── 颜色色板（如有预设颜色） ── */
.scheme-swatch-{name-1} { background: #hex; }
.scheme-swatch-{name-2} { background: #hex; }

/* ── canvas 样式 ── */
.canvas-shell {
  width: 100%;
  height: calc(min(56vh, 640px) + 48px);
}

#preview {
  max-width: 100%;
  max-height: min(56vh, 640px);
  box-shadow: var(--preview-canvas-shadow);
}
```

---

#### 4d. `content/tools/{slug}.md`（英文）

```markdown
---
title: "{title}"
slug: "{slug}"
desc: "{desc}"
toolType: "{slug}"
toolCSS: "/css/{slug}.css"
toolJS: "/js/{slug}.js"
eyebrow: "{eyebrow}"
cardPath: "$ pwd: ~/tools/{slug}"
installCode: "{installCode，若无则省略此字段}"
tags: {tags 数组}
weight: {weight}
---

{工具的简短英文描述，1–3 句话}
```

`toolType` 的值必须与 `layouts/partials/tool-controls-{toolType}.html` 中的 `{toolType}` 完全一致。

---

#### 4e. `content/zh/tools/{slug}.md`（中文，可选）

front matter 与英文版相同，仅 `title`、`desc`、`installCode` 的文字内容译为中文，其余字段（slug、toolType、toolCSS、toolJS、cardPath、tags、weight）保持不变。

---

### Step 5 — 写入文件

生成完所有文件内容后，询问用户 Hugo 站点的根目录路径：

> "请告诉我你的 hugo-theme-skills 站点根目录路径（例如 `~/Code/tools`），我来写入文件。"

确认路径后，逐一写入所有文件，每写完一个文件确认一次。

如果用户的站点尚未完成 hugo-theme-skills 改造，告知用户当前可以先生成文件内容供预览，等改造完成后再写入。

---

## 硬性约束（必须遵守）

- `<canvas id="preview">` 的 id 固定为 `"preview"`，不可更改
- `toolType` front matter 值必须与对应 partial 文件名中的部分完全一致
- STATE_KEY 格式严格为 `debuginn_{slug}_state`（slug 中的 `-` 替换为 `_`）
- CSS 必须同时定义 `:root`（暗色）和 `html[data-theme="light"]`（亮色）两套 token
- JavaScript 必须用 IIFE `(function(){ ... })()` 包裹，不使用 ES modules
- 不引入任何外部 JS/CSS 依赖（无 CDN、无 npm）
- controls partial 不包含 `downloadBtn`，该按钮由主题 `single.html` 统一渲染
- CSS 只定义差异 token 和专属样式，不重复定义 `portal.css` 已有的布局类

## 缺失输入的处理

- `slug`：从 `title` 自动推导（小写、空格转连字符、去除特殊字符）
- `eyebrow`：省略（不写入 front matter）
- `tags`：省略
- `weight`：默认 `0`
- `desc`：询问一次，若仍未提供则用 `"{title} — 工具"` 作占位

## 关联资源

- 字段速查：[references/schema.md](references/schema.md)
- 卡片预览工具：[web/index.html](web/index.html)

## Web Tool 使用方式

在 skills 仓库根目录启动本地服务器：

```bash
python3 -m http.server 8123
```

打开：`http://127.0.0.1:8123/plugins/debuginn-skill-tool/skills/debuginn-skill-tool/web/index.html`

在左侧填写工具信息，右侧实时预览卡片外观，点击 **复制 Front Matter** 获取生成的 `.md` 内容。
