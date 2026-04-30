# Clearup Mess Room

A Claude Code plugin for uninstalling plugins and skills.

## Description

**Clearup Mess Room** helps you safely remove unused Claude Code plugins and skills from your system. It provides intelligent search across both standalone skills and bundled plugin directories.

## Features

- **Smart Search**: First searches `~/.claude/skills` for standalone skills, then `~/.claude/plugins` for bundled plugins
- **Safe Deletion**: Protects `cache`, `data`, and `marketplaces` directories from accidental deletion
- **Plugin Detection**: When a skill is found inside a plugin, warns you and asks for confirmation to remove the entire plugin
- **Partial Match**: Supports case-insensitive partial name matching
- **Clear Feedback**: Shows all matched candidates before deletion

## How It Works

```
User: "/plugin-uninstall gstack"

Step 1: Search ~/.claude/skills for "gstack"
        → Found: ~/.claude/skills/gstack

Step 2: If found in skills → Delete directly
        If found in plugin → Ask: "Delete entire plugin?"

Step 3: Confirm and execute deletion
```

## Usage

Invoke the skill by asking Claude to uninstall a plugin or skill:

```
/plugin-uninstall <name>
```

Example:
```
/plugin-uninstall gstack
/plugin-uninstall lark-calendar
/plugin-uninstall some-unused-plugin
```

## Directory Structure

```
~/.claude/
├── skills/                    # Standalone skills (searched first)
│   ├── gstack/
│   ├── lark-xxx/
│   └── ...
│
└── plugins/                   # Plugin bundles (searched second)
    ├── cache/                # PROTECTED
    ├── data/                  # PROTECTED
    ├── marketplaces/          # PROTECTED
    └── some-plugin/
        └── skills/
            └── some-skill/    # Cannot delete individually
```

## Safety

- `cache`, `data`, `marketplaces` top-level directories are protected
- Always shows confirmation before deletion
- When a skill is part of a plugin, requires explicit consent to delete the entire plugin

## Files

```
clearup-mess-room/
├── package.json           # Plugin metadata
├── conductor.json         # Claude Code plugin manifest
├── LICENSE               # MIT License
├── README.md             # English documentation
├── README_zh.md          # Chinese documentation
└── skills/
    └── plugin-uninstall/
        └── SKILL.md      # Skill definition
```

## License

MIT
