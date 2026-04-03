# skills

A collection of reusable [Claude Code](https://claude.ai/code) skills published as a plugin marketplace.

## Installation

```bash
# Add this marketplace
/plugin marketplace add debuginn/skills

# Install individual skills
/plugin install promoflow@debuginn-skills
/plugin install noteflow@debuginn-skills
```

## Skills

| Skill | Invoke | Description |
|-------|--------|-------------|
| [promoflow](./plugins/promoflow/skills/promoflow/SKILL.md) | `/promoflow:promoflow` | Turn an iPhone screenshot into a square promotional image (App Store style) with title and subtitle on a clean white background. Includes an interactive web editor for preview and PNG export. |
| [noteflow](./plugins/noteflow/skills/noteflow/SKILL.md) | `/noteflow:noteflow` | Organize and summarize the current conversation into a structured Markdown document, preview inline, then save to a configured directory. Global config shared across all tools via `~/.config/noteflow/config.json`. |

## Structure

```
skills/
  .claude-plugin/
    marketplace.json        ← marketplace entry point
  plugins/
    promoflow/
      .claude-plugin/
        plugin.json
      skills/promoflow/
        SKILL.md
        references/
        assets/
        web/
    noteflow/
      .claude-plugin/
        plugin.json
      skills/noteflow/
        SKILL.md
```
