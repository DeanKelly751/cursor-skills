---
name: engineering-journal
description: >-
  Log debug sessions, learnings, and end-of-day reflections into a structured
  weekly engineering journal. Combines rubber-duck debugging with a learning log.
  Auto-pulls git activity for context. Entries are searchable and feed into 1:1
  prep and weekly reports. Use when the user asks to log a debug session, record
  something they learned, do an end-of-day reflection, or write a journal entry.
---

# Engineering Journal

Log debug sessions and learnings into a structured, searchable weekly journal.

## Core Principle

**Debugging is learning. Capture both in one place.**

Every debug session teaches something. Every learning has context. A single journal with typed entries keeps everything together and feeds into 1:1 prep and weekly reports.

## Entry Types

| Type | When to use | Key fields |
|------|------------|------------|
| **Debug** | Stuck on a problem, want to think through it | Symptom, hypothesis, investigation, root cause, lesson |
| **Learning** | Discovered something new — concept, tool, pattern, codebase quirk | Context, insight, why it matters |
| **Reflection** | End of day — guided review of the day's work | Pulls from both types above |

## Workflow

### Step 1: Determine Entry Type

Use `AskQuestion`:

```
AskQuestion:
  title: "Engineering Journal"
  questions:
    - id: entry_type
      prompt: "What kind of entry?"
      options:
        - "Debug session — I'm stuck on something or just solved something"
        - "Learning — I figured out something worth remembering"
        - "End-of-day reflection (Recommended)"
```

Then follow the appropriate path below.

---

### Path A: Debug Entry

#### A1: Gather Context

Scan the current conversation for:
- **What is going wrong** (error messages, unexpected behaviour)
- **What was expected** (correct behaviour)
- **What has been tried** (steps taken, hypotheses tested)
- **What fixed it** (if already resolved)

If the conversation doesn't contain enough detail, ask:

```
AskQuestion:
  title: "Debug Entry"
  questions:
    - id: symptom
      prompt: "What's the symptom? (error message, unexpected behaviour, etc.)"
      options:
        - "I'll describe it"
    - id: status
      prompt: "Is this resolved or still open?"
      options:
        - "Resolved — I found the fix"
        - "Still investigating"
```

#### A2: Auto-Gather Technical Context

Run these commands to enrich the entry:
- `git diff --stat` — what files have changed during the debug session
- `git log --oneline -5` — recent commits for context

#### A3: Compose the Entry

Use this template:

```markdown
## Debug: [short descriptive title]

**Date:** [YYYY-MM-DD]
**Status:** [Resolved | Investigating]
**Files involved:** [list from git diff or conversation]

### Symptom
[What is going wrong — include error messages verbatim]

### Expected behaviour
[What should happen instead]

### Hypotheses
1. [First theory] — **Result:** [confirmed / ruled out / untested]
2. [Second theory] — **Result:** [confirmed / ruled out / untested]

### Investigation log
- [Step taken] → [what was observed]
- [Step taken] → [what was observed]

### Root cause
[What actually caused the problem. Write "TBD — still investigating" if unresolved]

### Fix
[What fixed it, or "TBD" if unresolved. Include the actual code change or command if applicable]

### Lesson
[What would you do differently next time? What mental model changed? What should you check first if you see similar symptoms?]
```

---

### Path B: Learning Entry

#### B1: Prompt for the Learning

If not already clear from conversation, ask:

```
AskQuestion:
  title: "Learning Entry"
  questions:
    - id: topic
      prompt: "What did you learn? (concept, tool, pattern, codebase quirk, etc.)"
      options:
        - "I'll describe it"
    - id: source
      prompt: "How did you learn it?"
      options:
        - "Figured it out while coding"
        - "A teammate explained it"
        - "Read docs or an article"
        - "Code review feedback"
```

#### B2: Compose the Entry

Use this template:

```markdown
## Learned: [short descriptive title]

**Date:** [YYYY-MM-DD]
**Source:** [How you learned it — coding, teammate, docs, code review]

### Context
[What you were working on when you learned this]

### Insight
[The thing you learned — explain it as if teaching someone else. Be specific: include code snippets, command examples, or configuration details where relevant]

### Why it matters
[How does this change how you work? When will you use this again?]

### Related
[Links to docs, files in the codebase, related skills, or people who helped]
```

---

### Path C: End-of-Day Reflection

#### C1: Auto-Pull Today's Activity

Gather data automatically:

```
Shell: git log --oneline --since="today" --author="$(git config user.name)"
```

Also check for any open debug entries from today that are still in "Investigating" status.

#### C2: Prompt for Reflection

Present the day's commits, then ask:

```
AskQuestion:
  title: "End-of-Day Reflection"
  questions:
    - id: hardest
      prompt: "What was the hardest thing you worked on today?"
      options:
        - "I'll describe it"
    - id: learned
      prompt: "Anything you figured out that you didn't know this morning?"
      options:
        - "Yes, I'll describe it"
        - "Nothing major today"
    - id: confused
      prompt: "Anything still confusing that you want to revisit tomorrow?"
      options:
        - "Yes, I'll describe it"
        - "All clear for now"
```

#### C3: Compose the Reflection

Use this template:

```markdown
## Reflection: [YYYY-MM-DD]

### What I worked on
[Summary from git log — list the main things, not every commit]

### Hardest thing today
[From the user's answer — what made it hard, how they pushed through]

### What I learned
[From the user's answer — or "Quiet day for new learnings" if nothing major]

### Still fuzzy
[From the user's answer — things to revisit, questions to ask tomorrow]

### Tomorrow's focus
[Infer from open work, or ask if not obvious]
```

---

### Step 2: Determine the Journal File

Journal files are organised by ISO week:

1. Calculate the current ISO week number and year
2. Target file: `journal/[YYYY]-W[WW].md` (e.g. `journal/2026-W28.md`)
3. If the file exists, **append** the new entry (add a horizontal rule `---` separator)
4. If the file doesn't exist, create it with a header:

```markdown
# Engineering Journal — Week [WW], [YYYY]
```

Then append the entry below the header.

Use `mkdir -p journal` to ensure the directory exists.

### Step 3: Write the Entry

1. Create the directory if needed: `Shell: mkdir -p journal`
2. If the weekly file exists, use `Edit` to append the entry after the last line
3. If the weekly file doesn't exist, use `Write` to create it with the header and entry
4. Report what was saved:

> Journal entry saved to `journal/2026-W28.md` — "Debug: Redis connection timeout on cold start"

### Step 4: Offer Follow-ups

After saving, suggest relevant next actions:

```
AskQuestion:
  title: "What's next?"
  questions:
    - id: next
      prompt: "Entry saved. Anything else?"
      options:
        - "Done for now (Recommended)"
        - "Add another entry"
        - "Turn this into a question for my team (→ Question Formatter)"
        - "Create a JIRA ticket for follow-up work"
```

## Reading Previous Entries

When the user asks to review their journal:

1. Use `Glob` to list files in `journal/` matching `*.md`
2. Show available weeks
3. Let the user pick a week or search by keyword
4. Display entries, optionally filtered by type (Debug / Learning / Reflection)

## Important Notes

- Journal files live in `journal/` at the project root. Create the directory if it doesn't exist.
- File naming uses ISO 8601 week dates: `YYYY-WNN.md` (e.g. `2026-W28.md`).
- Always **append** to existing weekly files, never overwrite.
- Date format within entries is ISO 8601 (`YYYY-MM-DD`).
- Debug entries with status "Investigating" should be flagged when the user starts a new debug entry — prompt them to update the old one.
- End-of-day reflections should reference debug and learning entries from the same day to avoid duplication — summarise rather than repeat.
- The `Lesson` field in debug entries and the `Insight` field in learning entries are the most valuable fields — encourage the user to be specific. "I learned about caching" is weak; "Redis SCAN is O(1) per call but O(N) total — don't use it in a request path" is useful.
- This journal integrates with the **1:1 Prep** skill — recent entries are pulled automatically to populate wins and growth sections.
