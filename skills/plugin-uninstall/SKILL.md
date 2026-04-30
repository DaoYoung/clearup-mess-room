---
name: plugin-uninstall
description: "Uninstall a Claude Code plugin or skill by name. First searches $HOME/.claude/skills, then $HOME/.claude/plugins. Deleting a skill from a plugin requires removing the entire plugin. Protects cache, data, and marketplaces top-level directories."
---

# Plugin Uninstall

## Overview

Uninstall (remove) a Claude Code plugin or skill by name. This skill:
1. First searches `$HOME/.claude/skills` for standalone skills
2. If not found, searches `$HOME/.claude/plugins` subdirectories
3. If found in plugins, asks whether to delete the entire plugin
4. Performs safe directory deletion with protected directory checks

## When To Use

- User says "uninstall plugin", "remove plugin", "delete plugin", "delete skill"
- User provides a plugin or skill name to remove

## Protected Directories

The following **top-level** directories in `$HOME/.claude/plugins` are **protected** and must NOT be deleted:

- `cache`
- `data`
- `marketplaces`

Subdirectories **within** these protected directories **can** be deleted.

## Workflow

### Step 1: Resolve plugin/skill name

The user provides a name (can be partial, case-insensitive match is supported).

### Step 2: Search in skills directory

First, scan `$HOME/.claude/skills` for directories matching the plugin name.

- If found in skills: present for deletion directly
- If not found: proceed to Step 3

### Step 3: Search in plugins directory

If not found in skills, traverse `$HOME/.claude/plugins` at all directory levels and collect directories whose **basename** matches the plugin name (case-insensitive).

Exclude the protected top-level directories (`cache`, `data`, `marketplaces`) themselves from deletion candidates, but allow matching subdirectories within them.

### Step 4: Present matches to user

**If found in skills (`~/.claude/skills/<name>`):**
- Show the directory path
- Ask user to confirm deletion

**If found in plugins (`~/.claude/plugins/<plugin>/skills/<name>`):**
- Show the containing plugin path
- Inform user that the skill is part of a plugin
- Ask: "Delete the entire plugin `<plugin>`? (y/n)"
  - If yes: proceed to delete the entire containing plugin directory
  - If no: inform user that individual skills cannot be deleted from a plugin without breaking it, and stop

**If multiple matches found:**
- Display all matches with their full paths
- Ask user to confirm which one(s) to delete

**If no matches found:**
- Report that no matching plugin or skill was found
- List available skills in `$HOME/.claude/skills` as reference

### Step 5: Confirm deletion

Show the directory path(s) to be deleted and require user confirmation before proceeding.

### Step 6: Execute deletion

For each confirmed directory:

1. Verify it's not a protected top-level directory
2. Remove the directory recursively (`rm -rf`)

Report success or failure for each directory.

## Directory Scanning Logic

```
~/.claude/
├── skills/                    # First search location
│   ├── gstack/              # CAN be deleted directly
│   ├── lark-xxx/            # CAN be deleted directly
│   └── ...
│
└── plugins/                   # Second search location
    ├── cache/                # PROTECTED - cannot delete this directory itself
    │   └── subdir/          # subdir CAN be deleted if it matches plugin name
    ├── data/                 # PROTECTED
    │   └── subdir/
    ├── marketplaces/         # PROTECTED
    │   └── subdir/
    └── some-plugin/         # plugin directory
        └── skills/          # skills bundled in plugin
            └── some-skill/  # skill is part of plugin, cannot delete alone
```

### Matching Algorithm

- Match against directory **basename** (not full path)
- Case-insensitive comparison
- Supports partial match (e.g., "code" matches "code-review-expert")
- If plugin name is exact basename match, prefer it over partial matches

## Execution Flow

```
User input: "gstack"
    │
    ├─► Search ~/.claude/skills for "*gstack*"
    │       │
    │       ├─► FOUND in skills → Delete ~/.claude/skills/gstack
    │       │
    │       └─► NOT FOUND
    │               │
    │               └─► Search ~/.claude/plugins for "*gstack*"
    │                       │
    │                       ├─► FOUND in plugin:
    │                       │   "gstack is part of plugin <name>. Delete entire plugin? (y/n)"
    │                       │       │
    │                       │       ├─► y → Delete ~/.claude/plugins/<name>
    │                       │       │
    │                       │       └─► n → "Cannot delete individual skill. Stop."
    │                       │
    │                       └─► NOT FOUND → "No matching plugin or skill found."
```

## Outputs

Report each of the following:

- Searched locations (skills first, then plugins)
- Matched candidates
- Whether skill is part of a plugin (if applicable)
- Protected directory violations (if any attempted)
- Deletion results (success/failure per directory)
- Final status
