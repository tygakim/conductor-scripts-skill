---
name: conductor-scripts
description: Analyze project and generate Conductor scripts for setup, run, and archive
disable-model-invocation: true
allowed-tools: Read, Glob
---

# Conductor Scripts Generator

Analyze the current project and generate appropriate Conductor scripts for setup, run, and archive operations. Also explain how to start the dev server and access the running app.

## Your Task

1. **Detect the project type** by checking for these files:

### Node.js Detection
- `package.json` - Read it to find available scripts
- Package manager detection:
  - `package-lock.json` → npm
  - `yarn.lock` → yarn
  - `pnpm-lock.yaml` → pnpm
  - `bun.lockb` → bun

### Python Detection
- `pyproject.toml` - Check `[tool.poetry]`, `[tool.uv]`, `[build-system]`
- `requirements.txt` - pip-based project
- `setup.py` - legacy setuptools
- `Pipfile` - pipenv

### Go Detection
- `go.mod` - Go modules project
- Check for `main.go` or `cmd/` directory for entry point
- Check for `Makefile` with run/dev targets

### Rust Detection
- `Cargo.toml` - Cargo project
- Check `[package]` for binary vs library
- Check for `src/main.rs` (binary) vs `src/lib.rs` (library)

### Monorepo Detection
Check for these patterns FIRST before individual package detection:
- **Node.js workspaces**: `package.json` with `"workspaces"` field
- **pnpm workspaces**: `pnpm-workspace.yaml`
- **Lerna**: `lerna.json`
- **Nx**: `nx.json`
- **Turborepo**: `turbo.json`
- **Rush**: `rush.json`
- **Go workspace**: `go.work`
- **Cargo workspace**: `Cargo.toml` with `[workspace]`
- **Multiple package.json files**: Check for `packages/`, `apps/`, `libs/` directories

### Ruby Detection
- `Gemfile`, `Gemfile.lock` - Bundler project
- Check for Rails (`config/application.rb`) → `rails server`
- Check for Sinatra (`app.rb` with `require 'sinatra'`) → `ruby app.rb`

### PHP Detection
- `composer.json`, `composer.lock` - Composer project
- Check for Laravel (`artisan` file) → `php artisan serve`
- Check for Symfony (`bin/console`) → `symfony server:start` or `php bin/console server:run`

### Java Detection
- `pom.xml` - Maven project → `mvn spring-boot:run` or `mvn exec:java`
- `build.gradle`, `build.gradle.kts` - Gradle → `./gradlew bootRun` or `./gradlew run`
- Check for Spring Boot (`spring-boot-starter` in dependencies)

### Elixir Detection
- `mix.exs` - Mix project
- Check for Phoenix (`config/config.exs` with Phoenix imports) → `mix phx.server`
- Plain Elixir → `iex -S mix`

### Docker & Services Detection
- `docker-compose.yml` or `compose.yaml` - Parse for services
- Common services to detect: postgres, mysql, mariadb, redis, mongodb, elasticsearch, rabbitmq, kafka
- For each service, note the port mapping (e.g., `5432:5432` for postgres)

### Environment Setup Detection
- `.env.example`, `.env.local.example`, `.env.sample` - Template exists
- Check docker-compose.yml for `environment:` section variables
- Scan for common patterns: `DATABASE_URL`, `REDIS_URL`, `API_KEY`, `SECRET_KEY`

### Port Detection
- Parse `package.json` scripts for `--port`, `-p` flags
- Check `vite.config.ts/js` for `server.port`
- Check `next.config.js` or `next.config.mjs` for custom port
- Parse docker-compose `ports:` mappings
- Check `.env.example` for `PORT` variable
- Check `main.go`, `main.rs`, or framework configs for hardcoded ports

### API Documentation Detection
- FastAPI projects → `/docs` (Swagger UI), `/redoc` (ReDoc)
- `swagger.json`, `openapi.json`, `openapi.yaml` files in root or `docs/`
- GraphQL (`schema.graphql` or graphql dependencies) → `/graphql` playground
- Health endpoints: `/health`, `/healthz`, `/api/health`

### CI/CD & Deployment Detection
- `.github/workflows/*.yml` - GitHub Actions
- `.gitlab-ci.yml` - GitLab CI
- `vercel.json` - Vercel deployment
- `netlify.toml` - Netlify deployment
- `fly.toml` - Fly.io deployment
- `railway.json` or `railway.toml` - Railway deployment
- `render.yaml` - Render deployment
- `Dockerfile` - Containerized deployment
- `Procfile` - Heroku deployment

2. **Determine the scripts:**

### Setup Script (runs when workspace is created)
| Project Type | Setup Command |
|--------------|---------------|
| npm | `npm install` |
| yarn | `yarn install` |
| pnpm | `pnpm install` |
| bun | `bun install` |
| poetry | `poetry install` |
| uv | `uv sync` |
| pip + requirements.txt | `pip install -r requirements.txt` |
| pip + pyproject.toml | `pip install -e .` |
| pipenv | `pipenv install` |
| Go | `go mod download` |
| Rust | `cargo build` |
| Monorepo (npm) | `npm install` (from root) |
| Monorepo (pnpm) | `pnpm install` |
| Monorepo (turborepo) | `pnpm install` or `npm install` |
| Ruby | `bundle install` |
| PHP (Composer) | `composer install` |
| Java (Maven) | `mvn install` or `mvn dependency:resolve` |
| Java (Gradle) | `./gradlew build` or `./gradlew dependencies` |
| Elixir | `mix deps.get` |

### Run Script (triggered by Run button)
Look for these patterns in order of preference:

**Node.js** (check `scripts` in package.json):
1. `dev` → `{pm} run dev`
2. `start` → `{pm} run start` or `{pm} start`
3. `serve` → `{pm} run serve`
4. If none found, check for frameworks:
   - Next.js (`next` in dependencies) → `{pm} run dev`
   - Vite (`vite` in devDependencies) → `{pm} run dev`

**Python** (check for framework indicators):
1. FastAPI (`fastapi` in deps) → `uvicorn main:app --reload` or check pyproject.toml scripts
2. Django (`django` in deps) → `python manage.py runserver`
3. Flask (`flask` in deps) → `flask run` or `python app.py`
4. General script → check `[project.scripts]` or `[tool.poetry.scripts]` in pyproject.toml

**Go**:
1. Check `Makefile` for `run`, `dev`, or `start` targets → `make run` / `make dev`
2. Check for `cmd/server/` or `cmd/api/` → `go run ./cmd/server`
3. Check for `main.go` in root → `go run .`
4. Air (hot reload) config `.air.toml` → `air`

**Rust**:
1. Check `Cargo.toml` for `[[bin]]` targets
2. Default binary → `cargo run`
3. Specific binary → `cargo run --bin <name>`
4. cargo-watch for hot reload → `cargo watch -x run`

**Monorepo**:
1. Check root `package.json` scripts for `dev`, `start`
2. Turborepo → `turbo run dev` or `pnpm dev`
3. Nx → `nx serve <app>` or `nx run-many --target=serve`
4. Lerna → `lerna run dev --parallel`
5. If no root script, list available packages and ask user which to run

**Ruby**:
1. Check for Rails (`config/application.rb`) → `rails server` or `bin/rails server`
2. Check for Rack (`config.ru`) → `rackup`
3. Check for Sinatra pattern → `ruby app.rb`
4. Check `Procfile` for web process definition

**PHP**:
1. Check for Laravel (`artisan` file) → `php artisan serve`
2. Check for Symfony (`bin/console`) → `symfony server:start` or `php -S localhost:8000 -t public`
3. Generic PHP → `php -S localhost:8000`

**Java**:
1. Maven with Spring Boot (`spring-boot-maven-plugin` in pom.xml) → `mvn spring-boot:run`
2. Maven generic → `mvn exec:java`
3. Gradle with Spring Boot → `./gradlew bootRun`
4. Gradle generic → `./gradlew run`

**Elixir**:
1. Check for Phoenix (`phoenix` in deps) → `mix phx.server`
2. Plain Elixir app → `mix run --no-halt` or `iex -S mix`

### Archive Script (runs when workspace is archived)
Usually not needed. Only suggest if there are:
- Local databases to clean up
- Temp files in known locations
- Docker containers to stop

3. **Output in BOTH formats:**

## Output Format

### Section 1: Quick Start Guide

First, provide a friendly "How to run this app" guide:

```
## Quick Start

1. **Install dependencies:**
   <setup command>

2. **Start the dev server:**
   <run command>

3. **Open in browser:**
   <URL where the app will be accessible, e.g., http://localhost:3000>

The dev server will hot-reload when you make changes.
```

Determine the URL based on:
- Next.js, Vite, Create React App → `http://localhost:3000` (or check config)
- FastAPI/Uvicorn → `http://localhost:8000` (with `/docs` for API docs)
- Django → `http://localhost:8000`
- Flask → `http://localhost:5000`
- Go (Gin, Echo, Fiber) → `http://localhost:8080` (check main.go for port)
- Rust (Actix, Axum, Rocket) → `http://localhost:8080` (check config)
- Rails → `http://localhost:3000`
- PHP/Laravel → `http://localhost:8000`
- Phoenix/Elixir → `http://localhost:4000`
- Check for PORT in .env or config files

### Section 1.5: Services & Prerequisites (if docker-compose detected)

If `docker-compose.yml` or `compose.yaml` was found, output:

```
## Services Required

The following services were detected in docker-compose.yml:
- PostgreSQL (port 5432)
- Redis (port 6379)
[list all detected services with their ports]

**Start services before running the app:**
```bash
docker-compose up -d
```

**Stop services:**
```bash
docker-compose down
```
```

### Section 1.6: Ports Overview

If multiple ports were detected, provide a summary:

```
## Ports Used

| Service | URL | Purpose |
|---------|-----|---------|
| App | http://localhost:3000 | Next.js frontend |
| API | http://localhost:8000 | FastAPI backend |
| Database | localhost:5432 | PostgreSQL |
| Cache | localhost:6379 | Redis |
```

### Section 1.7: API Documentation (if detected)

If API documentation endpoints were detected:

```
## API Documentation

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc
- **OpenAPI Spec**: http://localhost:8000/openapi.json
- **Health Check**: http://localhost:8000/health

[For GraphQL projects:]
- **GraphQL Playground**: http://localhost:4000/graphql
```

### Section 1.8: CI/CD Info (if detected)

If deployment configuration was found:

```
## Deployment

- **Platform**: Vercel (vercel.json detected)
- **CI**: GitHub Actions (.github/workflows/ci.yml)
- **Container**: Dockerfile available

[Include any relevant deploy commands or notes from the config files]
```

### Section 2: Repository Settings format

```
## Repository Settings (copy-paste into Conductor)

Setup Script:
<the setup command>

Run Script:
<the run command>

Archive Script:
<the archive command, or "None needed">
```

### Section 3: conductor.json format

```json
{
  "scripts": {
    "setup": "<setup command>",
    "run": "<run command>"
  }
}
```

Only include `archive` in the JSON if one is actually needed.

### Section 4: devcontainer.json (for VS Code / GitHub Codespaces)

Generate a devcontainer.json based on detected stack. This enables one-click development environments.

**Base image selection:**
| Stack | Base Image |
|-------|------------|
| Node.js | `mcr.microsoft.com/devcontainers/javascript-node:20` |
| Python | `mcr.microsoft.com/devcontainers/python:3.11` |
| Go | `mcr.microsoft.com/devcontainers/go:1.21` |
| Rust | `mcr.microsoft.com/devcontainers/rust:1` |
| Ruby | `mcr.microsoft.com/devcontainers/ruby:3.2` |
| PHP | `mcr.microsoft.com/devcontainers/php:8.2` |
| Java | `mcr.microsoft.com/devcontainers/java:17` |
| Elixir | `elixir:1.15` |

**Features to add based on detection:**
| Detection | Feature to Add |
|-----------|----------------|
| Docker/docker-compose | `"ghcr.io/devcontainers/features/docker-in-docker:2": {}` |
| PostgreSQL service | `"ghcr.io/devcontainers/features/postgresql:1": {}` |
| Node.js (in non-Node project) | `"ghcr.io/devcontainers/features/node:1": {}` |
| Python (in non-Python project) | `"ghcr.io/devcontainers/features/python:1": {}` |
| Go (in non-Go project) | `"ghcr.io/devcontainers/features/go:1": {}` |
| GitHub CLI | `"ghcr.io/devcontainers/features/github-cli:1": {}` |
| AWS CLI | `"ghcr.io/devcontainers/features/aws-cli:1": {}` |

**VS Code extensions by stack:**
| Stack | Extensions |
|-------|------------|
| Node.js/TypeScript | `dbaeumer.vscode-eslint`, `esbenp.prettier-vscode` |
| Python | `ms-python.python`, `ms-python.vscode-pylance` |
| Go | `golang.go` |
| Rust | `rust-lang.rust-analyzer` |
| Ruby | `shopify.ruby-lsp` |
| PHP | `bmewburn.vscode-intelephense-client` |
| Java | `vscjava.vscode-java-pack` |
| Elixir | `jakebecker.elixir-ls` |
| Docker | `ms-azuretools.vscode-docker` |
| Prisma | `Prisma.prisma` |
| Tailwind | `bradlc.vscode-tailwindcss` |
| GraphQL | `GraphQL.vscode-graphql` |

**Example output:**

```json
{
  "name": "my-project",
  "image": "mcr.microsoft.com/devcontainers/javascript-node:20",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    "ghcr.io/devcontainers/features/github-cli:1": {}
  },
  "forwardPorts": [3000, 5432],
  "postCreateCommand": "npm install",
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "Prisma.prisma"
      ]
    }
  }
}
```

**Port forwarding:**
Include all detected ports in `forwardPorts` array:
- Application ports (3000, 8000, 8080, etc.)
- Database ports (5432, 3306, 27017, etc.)
- Cache ports (6379, etc.)

## Additional Notes

- **Monorepos**: If detected, list available packages/apps and ask which one to run. Suggest root-level dev script if available.
- If the project type is unclear, list what you found and ask the user to clarify
- For the run script, prefer dev/watch modes over production builds
- If there's a `.env.example` file, suggest adding `cp .env.example .env` to the setup script (before install)
- Look for README.md for additional context about running the project
- Check for docker-compose.yml - if present, mention that Docker might be needed
- **Go projects**: Check for Air config (`.air.toml`) for hot reload support
- **Rust projects**: Check for `cargo-watch` in dev dependencies for hot reload
