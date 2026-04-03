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
/plugin install debuginn-promo@debuginn-skills
/plugin install debuginn-note@debuginn-skills

# 3. 重载生效
/reload-plugins
```

## Skills

| Skill | 描述 |
|-------|------|
| [debuginn-promo](./plugins/debuginn-promo/skills/debuginn-promo/SKILL.md) | 将 iPhone 截图制作成方形宣传图（App Store 风格），左侧截图、右侧标题副标题，内置 Web 编辑器支持预览和导出 PNG。 |
| [debuginn-note](./plugins/debuginn-note/skills/debuginn-note/SKILL.md) | 将当前对话整理归纳为结构化 Markdown 文档，inline 预览后保存到本地。保存目录全局记忆于 `~/.config/noteflow/config.json`，跨项目跨工具复用。 |

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
