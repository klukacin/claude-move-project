# claude-move-project

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform: macOS | Linux](https://img.shields.io/badge/Platform-macOS%20%7C%20Linux-blue.svg)](https://github.com/wsagency/claude-move-project#supported-platforms)

A bash utility that moves Claude Code projects while preserving all session history and settings.

## Features

- **Move** project folders to new locations
- **Remove** projects and all associated session data (`--remove`)
- **Pack** projects into portable `.claudepack` archives (`--pack`)
- **Unpack** archives with automatic path rewriting (`--unpack`)
- Automatically migrates session history from `~/.claude/projects/`
- Updates all path references in `~/.claude/history.jsonl`
- Atomic rollback if any step fails
- Dry-run mode to preview changes before execution

## Installation

```bash
git clone https://github.com/wsagency/claude-move-project.git
cd claude-move-project
chmod +x claude-move-project
```

Optionally, add to your PATH:

```bash
sudo ln -s "$(pwd)/claude-move-project" /usr/local/bin/claude-move-project
```

## Usage

```bash
# Move a project
claude-move-project <source> <destination> [options]

# Remove a project and all session data
claude-move-project --remove <project-path>

# Pack a project into a portable archive
claude-move-project --pack <project-path> [archive-path]

# Unpack an archive to a new location
claude-move-project --unpack <archive-path> <destination>
```

### Examples

```bash
# Preview what would happen (recommended first step)
claude-move-project ./my-project ~/new-location --dry-run

# Move a project (specifying full destination path)
claude-move-project ./my-project ~/new-location/my-project

# Move into an existing directory (mv-like behavior)
# If ~/projects/ exists, this moves to ~/projects/my-project
claude-move-project ./my-project ~/projects

# Move without confirmation prompt
claude-move-project ./my-project ~/new-location --force

# Move with verbose output
claude-move-project ./my-project ~/new-location --verbose

# Move a project with special characters in name
claude-move-project "./project [v1.0]" "./project [v2.0]"

# Remove project and all session data
claude-move-project --remove ./my-project
claude-move-project --remove ./my-project --dry-run

# Pack project for transfer
claude-move-project --pack ./my-project
claude-move-project --pack ./my-project ~/backup.claudepack

# Unpack to new location
claude-move-project --unpack backup.claudepack ~/new-location
```

### Destination Behavior

The destination argument works like `mv`:

- If destination **doesn't exist**: Creates it as the new project location
- If destination **is an existing directory**: Moves the project *into* that directory

```bash
# Destination doesn't exist - creates ~/new-location as the project
claude-move-project ./my-app ~/new-location

# ~/projects exists - moves to ~/projects/my-app
claude-move-project ./my-app ~/projects
```

### Options

| Option | Description |
|--------|-------------|
| `--remove` | Delete project and all Claude session data |
| `--pack` | Archive project into .claudepack file |
| `--unpack` | Restore archive to destination |
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

### Archive Format (.claudepack)

The `--pack` command creates a tar.gz archive with this structure:

```
project-name.claudepack
├── manifest.json        # Metadata (version, original path, timestamp)
├── project/             # Project files including .claude/ settings
├── sessions/            # Session JSONL files from ~/.claude/projects/
└── history-entries.jsonl  # Relevant entries from history.jsonl
```

When unpacking, paths are automatically rewritten to match the new destination.

## Testing

Run the test suite to verify the script works correctly:

```bash
# Run all tests
./test.sh

# Run a specific test
./test.sh test_basic_move
```

The test suite covers:
- Basic move operations
- Relative path resolution
- mv-like destination behavior
- Special characters (brackets, spaces, dots)
- Symlink handling
- Dry-run mode
- Error conditions (missing source, existing dest)
- Backup/rollback functionality

## Supported Platforms

| Platform | Status |
|----------|--------|
| macOS | ✅ Fully supported |
| Linux | ✅ Supported |
| Windows | ⚠️ Via WSL or Git Bash |

### Windows Users

This script requires a bash environment. Windows users can run it using:

**Option 1: WSL2 (Recommended)**
1. Install WSL2: `wsl --install` in PowerShell (admin)
2. Open your WSL distro (e.g., Ubuntu)
3. Navigate to your project: `cd /mnt/c/Users/YourName/projects/myproject`
4. Run: `./claude-move-project ./my-project /mnt/c/new-location`

**Option 2: Git Bash**
1. Install [Git for Windows](https://git-scm.com/download/win) (includes Git Bash)
2. Open Git Bash
3. Navigate to your project and run the script

## Disclaimer

**USE AT YOUR OWN RISK**

- This tool has been tested on macOS and should work on Linux
- Windows users must use WSL or Git Bash (see above)
- Always run with `--dry-run` first to preview changes
- Consider backing up your `~/.claude/` directory before use
- The authors are not responsible for any data loss

## Attribution

Created by [ws.agency](https://ws.agency)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

Copyright (c) 2025 WEB Solutions Ltd. (ws.agency) & Kristijan Lukačin
