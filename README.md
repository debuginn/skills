# debuginn-skills

[English](./README.md) · [中文](./README.zh.md)

[![GitHub Stars](https://img.shields.io/github/stars/debuginn/skills?style=flat-square&logo=github)](https://github.com/debuginn/skills/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/debuginn/skills?style=flat-square&logo=github)](https://github.com/debuginn/skills/forks)
[![License](https://img.shields.io/github/license/debuginn/skills?style=flat-square)](./LICENSE)
[![Last Commit](https://img.shields.io/github/last-commit/debuginn/skills?style=flat-square)](https://github.com/debuginn/skills/commits/main)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin%20Marketplace-blueviolet?style=flat-square)](https://claude.ai/code)

A collection of reusable [Claude Code](https://claude.ai/code) skills published as a plugin marketplace. Install once, use across all your projects.

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

## Keeping up to date

Enable auto-update to receive new skills automatically:

```bash
/plugin marketplace auto-update debuginn-skills
```

Or sync manually:

```bash
/plugin marketplace sync debuginn-skills
/reload-plugins
```

## License

[MIT](./LICENSE)
