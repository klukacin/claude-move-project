# claude-move-project

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform: macOS](https://img.shields.io/badge/Platform-macOS-blue.svg)](https://www.apple.com/macos/)

A bash utility that moves Claude Code projects while preserving all session history and settings.

## Features

- Moves project folders to new locations
- Automatically migrates session history from `~/.claude/projects/`
- Updates all path references in `~/.claude/history.jsonl`
- Atomic rollback if any step fails
- Dry-run mode to preview changes before execution

## Installation

```bash
git clone https://github.com/klukacin/claude-move-project.git
cd claude-move-project
chmod +x claude-move-project
```

Optionally, add to your PATH:

```bash
sudo ln -s "$(pwd)/claude-move-project" /usr/local/bin/claude-move-project
```

## Usage

```bash
claude-move-project <source> <destination> [options]
```

### Examples

```bash
# Preview what would happen (recommended first step)
claude-move-project ./my-project ~/new-location --dry-run

# Move a project
claude-move-project ./my-project ~/new-location

# Move without confirmation prompt
claude-move-project ./my-project ~/new-location --force

# Move with verbose output
claude-move-project ./my-project ~/new-location --verbose
```

### Options

| Option | Description |
|--------|-------------|
| `-n, --dry-run` | Preview changes without executing |
| `-f, --force` | Skip confirmation prompt |
| `--no-backup` | Skip backup of history.jsonl |
| `-v, --verbose` | Show detailed output |
| `-h, --help` | Show help message |
| `--version` | Show version |

## How It Works

Claude Code stores project data in three locations:

1. **Project folder** - Your actual project with `.claude/` settings
2. **History folder** - `~/.claude/projects/[encoded-path]/` with session JSONL files
3. **History index** - `~/.claude/history.jsonl` with project path references

This script handles all three, ensuring your session history follows your project.

### Migration Sequence

1. Backup `history.jsonl`
2. Move project folder to destination
3. Rename history folder in `~/.claude/projects/`
4. Update path references in `history.jsonl`

If any step fails, all changes are automatically rolled back.

## Disclaimer

**USE AT YOUR OWN RISK**

- This tool has only been tested on **macOS**
- Always run with `--dry-run` first to preview changes
- Consider backing up your `~/.claude/` directory before use
- The authors are not responsible for any data loss

## Attribution

Created by [ws.agency](https://ws.agency)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

Copyright (c) 2025 WEB Solutions Ltd. (ws.agency) & Kristijan Lukačin
