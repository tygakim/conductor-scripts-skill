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
- Check for PORT in .env or config files

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

## Additional Notes

- If you detect a monorepo (multiple package.json files, or workspaces config), mention this and suggest the user specify which package to run
- If the project type is unclear, list what you found and ask the user to clarify
- For the run script, prefer dev/watch modes over production builds
- If there's a `.env.example` file, suggest adding `cp .env.example .env` to the setup script (before install)
- Look for README.md for additional context about running the project
- Check for docker-compose.yml - if present, mention that Docker might be needed
