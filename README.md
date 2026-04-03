# skills

A collection of reusable [Claude Code](https://claude.ai/code) skills published as a plugin marketplace.

## Installation

```bash
# 1. Add this marketplace
/plugin marketplace add debuginn/skills

# 2. Install skills
/plugin install promoflow@debuginn-skills
/plugin install noteflow@debuginn-skills

# 3. Reload to apply
/reload-plugins
```

## Skills

| Skill | Invoke | Description |
|-------|--------|-------------|
| [promoflow](./plugins/promoflow/skills/promoflow/SKILL.md) | `/promoflow:promoflow` | Turn an iPhone screenshot into a square promotional image (App Store style) with title and subtitle. Includes an interactive web editor for preview and PNG export. |
| [noteflow](./plugins/noteflow/skills/noteflow/SKILL.md) | `/noteflow:noteflow` | Organize and summarize the current conversation into a structured Markdown document, preview inline, then save locally. Save directory is stored globally in `~/.config/noteflow/config.json`. |
