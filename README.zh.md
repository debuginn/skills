# debuginn-skills

[English](./README.md) · [中文](./README.zh.md)

[![License](https://img.shields.io/github/license/debuginn/skills?style=flat-square)](./LICENSE)
[![Last Commit](https://img.shields.io/github/last-commit/debuginn/skills?style=flat-square)](https://github.com/debuginn/skills/commits/main)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-插件市场-blueviolet?style=flat-square)](https://claude.ai/code)

基于 [Claude Code](https://claude.ai/code) 插件市场发布的可复用 skill 合集，安装一次，所有项目通用。

## 安装

```bash
# 1. 添加插件市场
/plugin marketplace add debuginn/skills

# 2. 安装 skill
/plugin install debuginn-wx-xhs-poster@debuginn-skills
/plugin install debuginn-note@debuginn-skills
/plugin install debuginn-skill-tool@debuginn-skills

# 3. 重载生效
/reload-plugins
```

## Skills

| Skill | 描述 |
|-------|------|
| [debuginn-wx-xhs-poster](./plugins/debuginn-wx-xhs-poster/skills/debuginn-wx-xhs-poster/SKILL.md) | 将截图制作成微信或小红书方形宣传图，引导用户通过在线工具选择标题、副标题、配色方案和比例，导出 PNG。 |
| [debuginn-note](./plugins/debuginn-note/skills/debuginn-note/SKILL.md) | 将当前对话整理归纳为结构化 Markdown 文档，inline 预览后保存到本地。保存目录全局记忆于 `~/.config/noteflow/config.json`，跨项目跨工具复用。 |
| [debuginn-skill-tool](./plugins/debuginn-skill-tool/skills/debuginn-skill-tool/SKILL.md) | hugo-theme-skills 工具页引导创建器——根据用户描述生成 `.md` front matter、Hugo controls partial、canvas JS 和双主题 CSS，内置卡片可视化预览工具。 |

## 保持更新

开启自动更新，新 skill 发布后自动获取：

```bash
/plugin marketplace auto-update debuginn-skills
```

或手动同步：

```bash
/plugin marketplace sync debuginn-skills
/reload-plugins
```

## License

[MIT](./LICENSE)
