# create-public-skill

Scaffold a new cross-platform public skill for Claude Code — generates skill.md, README.md, LICENSE (MIT), git repo, and symlink to ~/.claude/skills/.

## Prerequisites

- [Claude Code CLI](https://claude.ai/claude-code)
- Git

## Installation

```bash
git clone https://github.com/a-athaullah/create-public-skill.git
mkdir -p ~/.claude/skills/create-public-skill
cp create-public-skill/skill.md ~/.claude/skills/create-public-skill/
```

## Usage

```
/create-public-skill
```

Provide a skill name and description when prompted. The skill will:

1. Create a directory with skill.md, README.md, LICENSE (MIT), and memory/ folder
2. Initialize a git repo with proper .gitignore (memory contents excluded)
3. Link the skill to ~/.claude/skills/ so it's immediately active
4. Guide you through publishing to GitHub

### Cross-platform

Generated skills follow cross-platform rules:
- No OS-specific commands (powershell, ipconfig, open, pbcopy)
- Portable paths using `$HOME`
- Platform detection via `uname -s` when needed
- Compatible with Linux, macOS, and Windows (Git Bash)

## License

MIT
