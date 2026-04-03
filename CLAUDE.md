# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **Claude Code skills repository** — a collection of reusable skill definitions that extend Claude Code's capabilities. Skills are invocable via `/skill-name` in Claude Code sessions.

## Skill Structure

Each skill lives in its own subdirectory and must contain a `SKILL.md` file with YAML frontmatter:

```
skill-name/
  SKILL.md              # Required: skill definition with frontmatter
  references/           # Optional: supporting spec/reference docs
  assets/               # Optional: static assets (SVG, images)
  examples/             # Optional: example outputs
  web/                  # Optional: interactive web tools
```

### SKILL.md Frontmatter

```yaml
---
name: skill-name
description: One-sentence description — this is used by Claude Code to match user intent to the skill.
---
```

The `description` field is critical: it determines when the skill is triggered. Make it precise and action-oriented.

## Web Tool Convention

Skills with interactive web tools follow this pattern:
- Tool lives at `skill-name/web/index.html`
- Start a local server from the repo root: `python3 -m http.server 8123`
- Access at: `http://127.0.0.1:8123/skill-name/web/index.html`

## Adding a New Skill

1. Create a new directory at the repo root named after the skill.
2. Write `SKILL.md` with frontmatter (`name`, `description`) and the full skill instructions.
3. Add any supporting files (`references/`, `assets/`, `web/`) as needed.
4. The skill is immediately usable — no build or registration step required.
