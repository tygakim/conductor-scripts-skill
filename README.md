# Conductor Scripts Skill

A Claude Code skill that automatically detects your project type and generates appropriate Conductor scripts.

## Features

- Detects Node.js (npm/yarn/pnpm/bun) and Python (uv/poetry/pip/pipenv) projects
- Generates setup, run, and archive scripts
- Provides Quick Start guide with correct URLs
- Outputs both Repository Settings and conductor.json formats
- Handles monorepos and hybrid projects

## Installation

```bash
# Clone to your Claude skills directory
git clone https://github.com/tygakim/conductor-scripts-skill.git ~/.claude/skills/conductor-scripts
```

## Usage

In any Claude Code session, run:

```
/conductor-scripts
```

The skill will analyze your project and output:
1. **Quick Start guide** - How to install deps, run dev server, and access the app
2. **Repository Settings** - Copy-paste format for Conductor UI
3. **conductor.json** - JSON format for programmatic use

## Supported Project Types

| Type | Detection | Setup Command |
|------|-----------|---------------|
| npm | package-lock.json | `npm install` |
| yarn | yarn.lock | `yarn install` |
| pnpm | pnpm-lock.yaml | `pnpm install` |
| bun | bun.lockb | `bun install` |
| uv | pyproject.toml + uv | `uv sync` |
| poetry | pyproject.toml + poetry | `poetry install` |
| pip | requirements.txt | `pip install -r requirements.txt` |
| pipenv | Pipfile | `pipenv install` |

## Example Output

```
## Quick Start

1. **Install dependencies:**
   npm install

2. **Start the dev server:**
   npm run dev

3. **Open in browser:**
   http://localhost:3000

---

## Repository Settings (copy-paste into Conductor)

Setup Script:
npm install

Run Script:
npm run dev

Archive Script:
None needed
```

## License

MIT
