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
          [supporting files]  ← references/, assets/, web/, examples/
```

### SKILL.md Frontmatter

```yaml
---
name: skill-name
description: One-sentence description — used by Claude Code to match user intent to the skill.
---
```

### marketplace.json

Lists all plugins under `plugins` array. Each entry requires `name`, `source` (relative path), and `description`.

### plugin.json

Minimal required field: `name`. Recommended: `version`, `description`, `author`, `keywords`.

## Adding a New Skill

1. Create `plugins/<name>/.claude-plugin/plugin.json`
2. Create `plugins/<name>/skills/<name>/SKILL.md`
3. Add the plugin entry to `.claude-plugin/marketplace.json`
4. Update README.md skills table

## Web Tool Convention

Skills with interactive web tools start a local server from the repo root:

```bash
python3 -m http.server 8123
# access: http://127.0.0.1:8123/plugins/<name>/skills/<name>/web/index.html
```
