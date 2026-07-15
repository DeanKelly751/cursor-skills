---
name: repo-orientation
description: >-
  Generate a repo orientation/onboarding guide by detecting the tech stack,
  mapping the directory structure, finding entry points, and summarising
  conventions, tooling, and CI/CD. Use when the user asks to help understand
  a codebase, generate an onboarding guide, orient them in a repo, or explain
  how a project is structured.
---

# Repo Orientation / Onboarding Guide

Generate a orientation guide for any codebase by inspecting its manifests, directory structure, and configuration — no MCP integration required.

## Core Principle

**Extract from the repo, structure, confirm, write.**

Everything needed lives in the repo itself: manifest files, directory conventions, CI config, and existing docs. Detect what's there, degrade gracefully for what isn't, and never invent details that can't be inferred.

## Workflow

### Step 1: Detect the Stack

Use `Glob` at the repo root (and one level into common subdirectories like `backend/`, `frontend/`, `apps/*`, `services/*` for monorepos) for manifest files:

| File | Stack signal |
|---|---|
| `package.json` | Node.js/JavaScript/TypeScript |
| `pyproject.toml`, `requirements.txt`, `setup.py` | Python |
| `go.mod` | Go |
| `Cargo.toml` | Rust |
| `pom.xml`, `build.gradle`, `build.gradle.kts` | Java/Kotlin |
| `Gemfile` | Ruby |
| `composer.json` | PHP |

`Read` whichever are found and list key dependencies/frameworks (e.g. `react`, `express`, `django`, `fastapi`, `spring-boot`) — not the entire dependency tree, just what signals the architecture (web framework, ORM, test framework, major libraries).

For monorepos with multiple manifests, note each sub-project separately rather than merging them.

### Step 2: Map the Directory Structure

`Glob` the top 1-2 levels of the repo tree. Classify folders using this convention table, adapting names as needed for the actual repo:

| Folder pattern | Likely purpose |
|---|---|
| `src/`, `lib/` | Main source code |
| `api/`, `routes/`, `controllers/` | API layer |
| `services/` | Business logic / service layer |
| `components/` | UI components |
| `tests/`, `test/`, `__tests__/`, `spec/` | Test suites |
| `migrations/` | Database schema migrations |
| `infra/`, `terraform/`, `deploy/` | Infrastructure as code |
| `.github/workflows/` | CI/CD pipelines |
| `docs/` | Documentation |
| `scripts/` | Dev/ops tooling scripts |

Only include folders that actually exist — don't pad the map with placeholders for a convention the repo doesn't follow.

### Step 3: Find Entry Points

Look for, depending on what Step 1 detected:
- `package.json` `scripts` block (`start`, `dev`, `build`, `test`) via `Read`
- `Dockerfile` `CMD`/`ENTRYPOINT` lines
- `main.py`, `manage.py`, `app.py`, `wsgi.py`/`asgi.py`
- `main.go`, `cmd/*/main.go`
- `Application.java`/`Main.java`, or Spring Boot's `@SpringBootApplication` class
- `Makefile` targets, if present, as another source of canonical commands

### Step 4: Detect Conventions and Tooling

`Glob`/`Read` for:
- Linter/formatter config: `.eslintrc*`, `.prettierrc*`, `ruff.toml`, `.flake8`, `pyproject.toml` `[tool.*]` sections, `rustfmt.toml`
- Commit hooks: `.husky/`, `.pre-commit-config.yaml`
- Test framework config: `jest.config.*`, `pytest.ini`, `vitest.config.*`

### Step 5: Summarise CI/CD

`Glob` for `.github/workflows/*.yml` (or `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/config.yml`). `Read` each and summarize, in plain English, what triggers them and what stages they run (lint, test, build, deploy) — don't paste the raw YAML.

### Step 6: Pull Highlights From Existing Docs

If `README.md` and/or `CONTRIBUTING.md` exist, `Read` them and extract anything Steps 1-5 couldn't infer (e.g. required env vars, local service dependencies like a database or message queue, known gotchas). Summarize and attribute rather than duplicating verbatim.

### Step 7: Draft the Guide

```markdown
# [Repo Name] — Orientation Guide

## Overview
[1 paragraph: what this repo/service does, inferred from README/package description/main module]

## Tech Stack
[Language(s), frameworks, key libraries — from Step 1]

## Getting Started
- Install: `[command]`
- Run: `[command]`
- Test: `[command]`

## Directory Map
| Path | Purpose |
|------|---------|
[from Step 2]

## Key Entry Points
[From Step 3 — where execution starts, main services/modules]

## Conventions & Tooling
[From Step 4 — linting, formatting, commit hooks, test framework]

## CI/CD
[From Step 5 — plain-English summary of what runs on PR/merge/deploy]

## Gotchas & Tips
[From Step 6 — env vars, local dependencies, anything non-obvious]
```

Omit any section entirely if nothing could be inferred for it — don't fill it with placeholder text.

### Step 8: Present for Review

```
AskQuestion:
  title: "Review Orientation Guide"
  questions:
    - id: approve
      prompt: "Here's the drafted orientation guide. How does it look?"
      options:
        - "Looks good, save it (Recommended)"
        - "I want to edit it"
        - "Regenerate with more/less detail"
```

### Step 9: Save

```
AskQuestion:
  title: "Save Location"
  questions:
    - id: location
      prompt: "Where should I save it?"
      options:
        - "docs/ONBOARDING.md (Recommended)"
        - "ORIENTATION.md at repo root"
        - "Just show me, don't save"
```

On approval, create the target directory if needed and `Write` the file. Report the path back to the user.

## Handling Edge Cases

- **Monorepo with multiple stacks**: produce one guide with a "Sub-Projects" subsection per stack rather than picking just one, or ask the user which sub-project to focus on if they only care about one.
- **No manifest files found**: say so explicitly rather than guessing a stack; ask the user what language/framework the repo uses.
- **Guide already exists at the target path**: ask whether to overwrite, merge, or save alongside as a new version — never silently overwrite.
- **Very large repos**: cap the Directory Map at the most relevant top-level folders (skip generated/vendored directories like `node_modules/`, `vendor/`, `dist/`, `.venv/`).

## Important Notes

- This skill is re-runnable — encourage using it again after major structural changes to keep the guide current.
- Never fabricate commands or setup steps that weren't found in the repo; if install/run/test commands can't be determined, say so and ask the user.
- Keep the tone factual and orientation-focused — this is a map of the repo, not a design critique (that's what the `reviewers/` skills and `self-review` are for).
