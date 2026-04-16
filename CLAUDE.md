# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **Claude Code plugin marketplace** — a collection of skills published under `debuginn/skills` and installable via the Claude Code plugin system.

## Plugin Marketplace Structure

```
skills/
  .claude-plugin/
    marketplace.json          ← marketplace entry (lists all plugins)
  plugins/
    <plugin-name>/
      .claude-plugin/
        plugin.json           ← plugin manifest
      skills/
        <skill-name>/
          SKILL.md            ← skill definition with YAML frontmatter
          references/         ← design docs / spec files for Claude to read
          assets/             ← static visual files (SVG sketches, etc.)
          web/index.html      ← interactive HTML tool (optional)
          examples/           ← sample outputs for reference
```

### Naming Convention

The plugin directory name, `plugin.json` `name`, `marketplace.json` `name`, SKILL.md `name` frontmatter, and the `skills/<skill-name>/` subdirectory must **all be the same string** (kebab-case). When the skill and plugin share a name (the common case), `<plugin-name>` and `<skill-name>` are identical.

### SKILL.md Frontmatter

```yaml
---
name: skill-name
description: One-sentence description — used by Claude Code to match user intent to the skill.
---
```

The `description` is the primary signal Claude Code uses to route user intent to this skill — make it precise.

### marketplace.json

Lists all plugins under `plugins` array. Each entry requires `name`, `source` (relative path), and `description`. `version`, `author`, `license`, and `keywords` are recommended.

### plugin.json

Fields overlap with the `marketplace.json` entry for the same plugin. Keep both in sync when updating metadata. Minimal required field: `name`.

## Adding a New Skill

1. Create `plugins/<name>/.claude-plugin/plugin.json`
2. Create `plugins/<name>/skills/<name>/SKILL.md`
3. Add the plugin entry to `.claude-plugin/marketplace.json` (must match `plugin.json` metadata)
4. Update `README.md` skills table (one row per skill: link to SKILL.md + description)

## Web Tool Convention

Skills with interactive web tools start a local server from the **repo root**:

```bash
python3 -m http.server 8123
# access: http://127.0.0.1:8123/plugins/<name>/skills/<name>/web/index.html
```

Web tools are self-contained single-file HTML pages with no external dependencies. They use `localStorage` for state persistence and a `<canvas id="preview">` for rendering.
