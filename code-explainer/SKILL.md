---
name: code-explainer
description: >-
  Explain code at any scope — file, directory, or full repo. Supports quick scan,
  deep dive, system context, and onboarding map modes. Traces request paths, names
  patterns, flags newcomer traps, and cross-references knowledge skills. Use when
  the user asks to explain code, understand a file, onboard to a codebase, walk
  through how something works, or trace a code path.
---

# Code Explainer / Onboarding

Explain code at any scope with the right level of depth for the situation.

## Core Principle

**Scope, read, explain, connect.**

Determine what the user needs to understand and how deeply, gather the relevant code and context, then explain it concretely — with real names, real paths, and real patterns. Link to knowledge skills so explanations become learning opportunities.

## Explanation Modes

| Mode | Trigger phrases | Output |
|------|----------------|--------|
| **Quick scan** | "What does this do?", "Summarise this file" | 2-3 sentence summary, key exports, who calls it |
| **Deep dive** | "Walk me through this", "I need to modify this" | Section-by-section walkthrough, patterns, gotchas |
| **System context** | "How does this fit in?", "What calls this?" | Dependency map, data flow, upstream/downstream consumers |
| **Onboarding map** | "I'm new to this repo", "Where do I start?" | Directory overview, entry points, reading order |

## Workflow

### Step 1: Determine Scope and Mode

Use `AskQuestion`:

```
AskQuestion:
  title: "Code Explainer"
  questions:
    - id: scope
      prompt: "What should I explain?"
      options:
        - "Current file (Recommended)"
        - "A specific file or function"
        - "A directory or module"
        - "The whole repo (onboarding)"
    - id: mode
      prompt: "How deep should I go?"
      options:
        - "Quick scan — just tell me what it does (Recommended)"
        - "Deep dive — walk me through everything"
        - "System context — how it fits into the bigger picture"
        - "Onboarding map — I'm new, where do I start?"
```

If the user already specified a file or asked a specific question in conversation, infer the scope and mode instead of asking.

### Step 2: Gather Context

Depending on the scope, gather:

**For a single file:**
- Read the target file
- Read its imports to understand dependencies
- `Shell: grep -rn "import.*from.*[filename]" --include="*.ts" --include="*.py" --include="*.js" --include="*.go" .` — find who imports this file
- `Shell: git log --oneline -10 [file]` — recent activity and contributors
- Check for a corresponding test file (e.g. `foo.test.ts`, `test_foo.py`)

**For a directory or module:**
- `Shell: find [dir] -type f -name "*.ts" -o -name "*.py" -o -name "*.js" -o -name "*.go" | head -30` — list source files
- Read key entry point files (index.ts, main.py, mod.go, etc.)
- Read any README or documentation files in the directory

**For the whole repo:**
- Read the top-level README if it exists
- `Shell: find . -maxdepth 2 -type f -name "*.md" | head -20` — find documentation
- `Shell: find . -maxdepth 1 -type d | sort` — top-level directory structure
- Read package.json, go.mod, pyproject.toml, Cargo.toml, or equivalent for dependencies
- Look for entry points: main.*, index.*, app.*, server.*

### Step 3: Generate Explanation

Use the template matching the selected mode.

---

#### Quick Scan Template

```markdown
## [filename]

**Purpose:** [1-2 sentences — what this file does and why it exists]

**Key exports:**
- `functionName()` — [what it does]
- `ClassName` — [what it represents]

**Called by:** [list files that import this, or "entry point / not imported directly"]

**Dependencies:** [key imports — what external libraries or internal modules it relies on]
```

---

#### Deep Dive Template

```markdown
## [filename] — Deep Dive

**Purpose:** [what this file does in the system]

**Patterns used:** [name the design patterns — Repository, Factory, Observer, middleware chain, etc. If a relevant knowledge skill exists, reference it: "→ see `architecture/knowledge-design-patterns`"]

### Section-by-section walkthrough

#### [Section/block 1 name] (lines N-M)
[What this section does. Be concrete — use actual variable and function names. Explain the WHY, not just the what.]

#### [Section/block 2 name] (lines N-M)
[Continue for each logical section]

### Gotchas and newcomer traps
- [Thing that looks wrong but is intentional, with explanation]
- [Non-obvious side effect or implicit dependency]
- [Convention that differs from what you'd expect]

### If you need to change this
[Practical guidance: "To add a new endpoint, you'd need to: 1. Add a route in routes.ts, 2. Create a handler in handlers/, 3. Add validation in schemas/. The test file is at test/foo.test.ts."]

### Test coverage
- **Test file:** [path, or "No test file found"]
- **What's tested:** [brief summary of test cases]
- **What's not tested:** [any obvious gaps]

### Recent activity
[Output of git log — who's been here recently, useful for knowing who to ask]
```

---

#### System Context Template

```markdown
## [filename / module] — System Context

**Role in the system:** [1-2 sentences — what job this component has]

### Dependency map

**This depends on:**
- `[module/file]` — [what it uses from it]
- `[module/file]` — [what it uses from it]

**Depends on this:**
- `[module/file]` — [why it needs this]
- `[module/file]` — [why it needs this]

### Data flow

[Trace a concrete request or operation through the system. Example:]

```
User action → [Controller] → [Service] → [Repository] → [Database]
                   ↓
              [EventBus] → [NotificationService] → [Email provider]
```

[Explain each step with actual class/function names.]

### Related knowledge
[Link to relevant architecture/knowledge skills if the codebase uses recognisable patterns:
- "This module follows hexagonal architecture → see `architecture/knowledge-hexagonal-architecture`"
- "The event handling uses pub/sub → see `architecture/knowledge-event-driven`"]
```

---

#### Onboarding Map Template

```markdown
## Repo Onboarding Map

**What this repo does:** [1-2 sentences — the product/service it provides]

**Tech stack:** [languages, frameworks, databases, key libraries]

### Directory structure

| Directory | Purpose |
|-----------|---------|
| `src/` | [what's in here] |
| `tests/` | [test organisation] |
| `docs/` | [documentation] |
| ... | ... |

### Start reading here

[Ordered list of files to read to understand the codebase, with 1-sentence reason for each:]

1. **`[file]`** — [why to read this first]
2. **`[file]`** — [what this teaches you about the architecture]
3. **`[file]`** — [the main business logic lives here]

### Key entry points

| Entry point | What triggers it |
|-------------|-----------------|
| `[file:function]` | [HTTP request, CLI command, cron job, event, etc.] |

### Domain jargon glossary

| Term | Meaning | Where you'll see it |
|------|---------|-------------------|
| `[DomainTerm]` | [Plain English definition] | [files/modules that use it] |

### How to run it locally

[Commands to get the project running — from README or inferred from package.json/Makefile/docker-compose]

### Who to ask

[From git log: who commits most frequently to which areas]

| Area | Most active contributor |
|------|----------------------|
| `[directory]` | `[git username]` |

### Related knowledge skills
[List any architecture/design patterns in use and link to the relevant knowledge skills]
```

### Step 4: Offer Follow-ups

After presenting the explanation:

```
AskQuestion:
  title: "What's next?"
  questions:
    - id: next
      prompt: "Anything else you want to understand?"
      options:
        - "Done for now (Recommended)"
        - "Deep dive into one of the dependencies"
        - "Trace a specific request path"
        - "Explain the test suite"
        - "Show me how I'd make a change here"
```

## Important Notes

- **Be concrete, not abstract.** Use actual function names, file paths, and variable names. "It has a helper" is useless; "`normalizeEmail()` strips whitespace and lowercases the domain" is useful.
- **Name patterns explicitly.** If code uses Repository, Factory, Observer, middleware chain, or any recognisable pattern, name it. This teaches the reader to recognise patterns in the wild.
- **Flag newcomer traps.** Things that look broken but are intentional, implicit dependencies, magic strings, dynamic imports, runtime registration — anything that would trip up someone new.
- **Cross-reference knowledge skills.** When you spot a pattern covered by a skill in this repo (SOLID, hexagonal architecture, CQRS, etc.), mention it: "→ see `architecture/knowledge-hexagonal-architecture` for background on this pattern."
- **Don't fabricate.** If you're unsure what a section does, say so: "This block is unclear — it may handle [X] but I'd verify with the team." Never invent explanations.
- **Adapt to the language.** Use language-appropriate terminology (modules vs. packages, classes vs. structs, hooks vs. lifecycle methods).
- **Recent activity matters.** Always include `git log` output — knowing who recently touched a file tells the user who to ask questions to. This is especially valuable for interns.
