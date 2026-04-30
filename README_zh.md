# Clearup Mess Room

Claude Code 插件卸载工具。

## 描述

**Clearup Mess Room** 帮助您安全地删除系统中未使用的 Claude Code 插件和 skills。它会在独立 skills 和插件包目录中进行智能搜索。

## 功能特点

- **智能搜索**：优先搜索 `~/.claude/skills` 中的独立 skills，再搜索 `~/.claude/plugins` 中的插件包
- **安全删除**：保护 `cache`、`data`、`marketplaces` 目录免受意外删除
- **插件检测**：当 skill 存在于插件内部时，警告并询问是否删除整个插件
- **模糊匹配**：支持大小写不敏感的部分名称匹配
- **清晰反馈**：删除前显示所有匹配的候选项

## 安装

```bash
/plugin marketplace add https://github.com/DaoYoung/clearup-mess-room
/plugin install clearup-mess-room
```

## 工作原理

```
用户: "/plugin-uninstall gstack"

步骤 1: 在 ~/.claude/skills 中搜索 "gstack"
        → 找到: ~/.claude/skills/gstack

步骤 2: 如果在 skills 中找到 → 直接删除
        如果在插件中找到 → 询问: "删除整个插件?"

步骤 3: 确认并执行删除
```

## 使用方法

通过以下方式调用 skill：

```
/plugin-uninstall <名称>
```

示例：
```
/plugin-uninstall gstack
/plugin-uninstall lark-calendar
/plugin-uninstall 某个不用的插件
```

## 目录结构

```
~/.claude/
├── skills/                    # 独立 skills（优先搜索）
│   ├── gstack/
│   ├── lark-xxx/
│   └── ...
│
└── plugins/                   # 插件包（次优先搜索）
    ├── cache/                # 受保护
    ├── data/                  # 受保护
    ├── marketplaces/          # 受保护
    └── some-plugin/
        └── skills/
            └── some-skill/    # 不可单独删除
```

## 安全机制

- `cache`、`data`、`marketplaces` 顶级目录受保护
- 删除前始终显示确认提示
- 当 skill 是插件的一部分时，需要明确同意才能删除整个插件

## 文件结构

```
clearup-mess-room/
├── package.json           # 插件元数据
├── conductor.json         # Claude Code 插件清单
├── LICENSE               # MIT 许可证
├── README.md             # 英文文档
├── README_zh.md          # 中文文档
└── skills/
    └── plugin-uninstall/
        └── SKILL.md      # Skill 定义
```

## 许可证

MIT
