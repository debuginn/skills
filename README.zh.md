# debuginn-skills

一个基于 [Claude Code](https://claude.ai/code) 插件市场发布的可复用 skill 合集。

## 安装

```bash
# 1. 添加插件市场
/plugin marketplace add debuginn/skills

# 2. 安装 skill
/plugin install promoflow@debuginn-skills
/plugin install noteflow@debuginn-skills

# 3. 重载生效
/reload-plugins
```

## 保持更新

新 skill 发布后，开启自动更新以获取最新版本：

```bash
/plugin marketplace auto-update debuginn-skills
```

或手动同步：

```bash
/plugin marketplace sync debuginn-skills
/reload-plugins
```

## Skills

| Skill | 调用方式 | 描述 |
|-------|---------|------|
| [promoflow](./plugins/promoflow/skills/promoflow/SKILL.md) | `/promoflow:promoflow` | 将 iPhone 截图制作成方形宣传图（App Store 风格），左侧截图、右侧标题副标题，内置 Web 编辑器支持预览和导出 PNG。 |
| [noteflow](./plugins/noteflow/skills/noteflow/SKILL.md) | `/noteflow:noteflow` | 将当前对话整理归纳为结构化 Markdown 文档，inline 预览后保存到本地。保存目录全局记忆于 `~/.config/noteflow/config.json`，跨项目跨工具复用。 |
