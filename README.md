# Conductor Scripts Skill

![Languages](https://img.shields.io/badge/languages-10+-blue)
![Deterministic](https://img.shields.io/badge/detection-deterministic-green)

**Instant workspace intelligence for any codebase.**

A Claude Code skill that analyzes your project in seconds and generates everything you need to run it - setup commands, dev server, services, ports, and even devcontainer.json.

## Why This Skill?

Ever cloned a repo and spent 30 minutes figuring out how to run it?

- "npm install" ✅
- "npm run dev" ❌ `Error: DATABASE_URL not set`
- *30 minutes of README archaeology...*

This skill eliminates that. It scans your project files and tells you:
- Exact commands to run
- Services you need (Postgres, Redis, etc.)
- Every port the app uses
- Environment variables to configure

**No AI hallucination** - pure deterministic file detection using only Read and Glob tools.

## Features

- **Language Detection** - Node.js, Python, Go, Rust, Ruby, PHP, Java, Elixir
- **Package Managers** - npm, yarn, pnpm, bun, poetry, uv, pip, cargo, bundler, composer, maven, gradle, mix
- **Monorepos** - Turborepo, Nx, Lerna, Rush, npm/pnpm/yarn workspaces, Cargo workspaces, Go workspaces
- **Service Detection** - Docker Compose services (Postgres, Redis, MongoDB, etc.)
- **Port Discovery** - All ports your app uses at a glance
- **Environment Setup** - Detects .env templates and required variables
- **API Documentation** - FastAPI docs, Swagger, GraphQL endpoints
- **CI/CD Detection** - GitHub Actions, Vercel, Netlify, Fly.io
- **devcontainer.json** - Auto-generates VS Code / GitHub Codespaces config

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
| ruby | Gemfile | `bundle install` | `rails s` or `ruby app.rb` |
| php | composer.json | `composer install` | `php artisan serve` |
| java-maven | pom.xml | `mvn install` | `mvn spring-boot:run` |
| java-gradle | build.gradle | `./gradlew build` | `./gradlew bootRun` |
| elixir | mix.exs | `mix deps.get` | `mix phx.server` |

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

### Services & Infrastructure

| Service | Detection | Notes |
|---------|-----------|-------|
| PostgreSQL | docker-compose.yml | Shows port, suggests `docker-compose up -d` |
| MySQL/MariaDB | docker-compose.yml | Shows port |
| Redis | docker-compose.yml | Shows port |
| MongoDB | docker-compose.yml | Shows port |
| Elasticsearch | docker-compose.yml | Shows port |
| RabbitMQ | docker-compose.yml | Shows port |

## Example Output

```
## Quick Start

1. **Install dependencies:**
   pnpm install

2. **Start services:**
   docker-compose up -d

3. **Set up environment:**
   cp .env.example .env

4. **Start the dev server:**
   pnpm dev

5. **Open in browser:**
   http://localhost:3000

## Services Required
- PostgreSQL (port 5432)
- Redis (port 6379)

## Ports Used
- App: http://localhost:3000
- Database: localhost:5432
- Cache: localhost:6379

## API Documentation
- Health Check: http://localhost:3000/api/health

---

## Repository Settings (copy-paste into Conductor)

Setup Script:
docker-compose up -d && cp .env.example .env && pnpm install

Run Script:
pnpm dev

Archive Script:
docker-compose down
```

## License

MIT
