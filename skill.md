---
name: create-public-skill
description: Scaffold a new cross-platform public skill — generates skill.md, README.md, LICENSE (MIT), git repo, and symlink to ~/.claude/skills/
user_invocable: true
---

# Create Public Skill

Scaffold a new public skill that is cross-platform (Linux, macOS, Windows) and ready to publish as a GitHub repository.

## Input

Argumen yang diterima:
- **Skill name** — lowercase, hyphen-separated (e.g., `my-skill`)
- **Description** — one-line description of what the skill does

Jika tidak diberikan, tanyakan ke user.

## Validation

1. Nama skill harus lowercase, hanya huruf, angka, dan hyphen
2. Cek apakah sudah ada di `$PUBLIC_SKILL_DIR/` — jika ada, tanyakan user
3. Cek apakah sudah ada di `~/.claude/skills/` — jika ada, tanyakan user

## Variables

Deteksi platform untuk path:
```bash
if [[ "$OSTYPE" == "msys" || "$OSTYPE" == "mingw"* || "$OSTYPE" == "cygwin" ]]; then
  PUBLIC_SKILL_DIR="/c/public-skill"
else
  PUBLIC_SKILL_DIR="$HOME/public-skill"
fi
```

## Steps

### 1. Create Directory Structure

```
$PUBLIC_SKILL_DIR/{name}/
├── skill.md
├── README.md
├── LICENSE
├── memory/
│   └── .gitkeep
└── .gitignore
```

### 2. Generate skill.md

Template:
```markdown
---
name: {name}
description: {description}
user_invocable: true
---

# {Title Case Name}

{description}

## Input

Argumen yang diterima:
- (definisikan bersama user)

## Steps

(definisikan bersama user)
```

**Cross-platform rules** — skill.md HARUS mengikuti aturan ini:
- Tidak boleh pakai command Windows-only (`powershell`, `ipconfig`, `mklink`, `cmd /c`)
- Tidak boleh pakai command macOS-only (`open`, `pbcopy`, `defaults`)
- Gunakan `$HOME` bukan hardcoded path
- Path separator: forward slash `/`
- Jika perlu platform-specific, deteksi via:
  ```bash
  case "$(uname -s)" in
    Linux*)  OS=linux;;
    Darwin*) OS=mac;;
    MINGW*|MSYS*|CYGWIN*) OS=windows;;
  esac
  ```
- Gunakan tools yang tersedia cross-platform: `bash`, `git`, `node`, `python`, `curl`

### 3. Generate README.md

```markdown
# {name}

{description}

## Prerequisites

- [Claude Code CLI](https://claude.ai/claude-code)

## Installation

\```bash
git clone https://github.com/a-athaullah/{name}.git
mkdir -p ~/.claude/skills/{name}
cp {name}/skill.md ~/.claude/skills/{name}/
\```

## Usage

\```
/{name}
\```

## License

MIT
```

### 4. Generate LICENSE

MIT License dengan tahun saat ini dan nama "Ahmad Athaullah".

### 5. Generate .gitignore

```
memory/*.md
!memory/.gitkeep
```

### 6. Create .gitkeep

File kosong di `memory/.gitkeep` agar folder memory tetap ada di git.

### 7. Init Git & Commit

```bash
cd "$PUBLIC_SKILL_DIR/{name}"
git init
git add -A
git commit -m "Initial commit: {name} skill"
```

### 8. Create Symlink

Link skill ke `~/.claude/skills/` agar langsung aktif:

```bash
case "$(uname -s)" in
  MINGW*|MSYS*|CYGWIN*)
    # Windows: use junction (no admin required)
    cmd //c "mklink /J \"$(cygpath -w "$HOME/.claude/skills/{name}")\" \"$(cygpath -w "$PUBLIC_SKILL_DIR/{name}")\""
    ;;
  *)
    # Linux/macOS: symlink
    ln -sf "$PUBLIC_SKILL_DIR/{name}" "$HOME/.claude/skills/{name}"
    ;;
esac
```

### 9. Create GitHub Repo & Push

Buat repo public di GitHub dan push otomatis menggunakan `gh` CLI:

```bash
cd "$PUBLIC_SKILL_DIR/{name}"
gh repo create a-athaullah/{name} --public --source=. --remote=origin --push
```

Jika `gh` belum terinstall atau belum login:
1. Install: `winget install GitHub.cli` (Windows) / `brew install gh` (macOS) / `sudo apt install gh` (Linux)
2. Login: `gh auth login --web -p https`
3. Ulangi command di atas

## Post-Create

Setelah scaffold selesai, bantu user mengisi konten `skill.md` — definisikan steps, input, dan workflow bersama user.
