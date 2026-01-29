# Conductor Scripts Skill

A Claude Code skill that automatically detects your project type and generates appropriate Conductor scripts.

## Features

- Detects **Node.js** (npm/yarn/pnpm/bun), **Python** (uv/poetry/pip), **Go**, and **Rust** projects
- Handles **monorepos** (workspaces, Turborepo, Nx, Lerna, Cargo workspaces)
- Generates setup, run, and archive scripts
- Provides Quick Start guide with correct URLs
- Outputs both Repository Settings and conductor.json formats

## Installation

```bash
git clone https://github.com/tygakim/conductor-scripts-skill.git ~/.claude/skills/conductor-scripts
```

## Usage

In any Claude Code session, run:

```
/conductor-scripts
```

## Supported Project Types

### Languages

| Type | Detection | Setup Command | Run Command |
|------|-----------|---------------|-------------|
| npm | package-lock.json | `npm install` | `npm run dev` |
| yarn | yarn.lock | `yarn install` | `yarn dev` |
| pnpm | pnpm-lock.yaml | `pnpm install` | `pnpm dev` |
| bun | bun.lockb | `bun install` | `bun dev` |
| uv | pyproject.toml + uv | `uv sync` | `uv run ...` |
| poetry | pyproject.toml + poetry | `poetry install` | `poetry run ...` |
| pip | requirements.txt | `pip install -r requirements.txt` | framework-specific |
| Go | go.mod | `go mod download` | `go run .` or `make run` |
| Rust | Cargo.toml | `cargo build` | `cargo run` |

### Monorepos

| Type | Detection | Notes |
|------|-----------|-------|
| npm workspaces | `"workspaces"` in package.json | Lists available packages |
| pnpm workspaces | pnpm-workspace.yaml | Lists available packages |
| Turborepo | turbo.json | `turbo run dev` |
| Nx | nx.json | `nx serve <app>` |
| Lerna | lerna.json | `lerna run dev` |
| Go workspace | go.work | Lists modules |
| Cargo workspace | `[workspace]` in Cargo.toml | Lists members |

## Example Output

```
## Quick Start

1. **Install dependencies:**
   go mod download

2. **Start the dev server:**
   make run

3. **Open in browser:**
   http://localhost:8080

---

## Repository Settings (copy-paste into Conductor)

Setup Script:
go mod download

Run Script:
make run

Archive Script:
None needed
```

## License

MIT
