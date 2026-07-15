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
        - "Update an existing debug entry — resolve an open investigation"
```

Then follow the appropriate path below.

---

### Path A: Debug Entry

#### A0: Check for Open Investigations

Before creating a new debug entry, check for existing unresolved entries:

```
Shell: grep -l "\\*\\*Status:\\*\\* Investigating" journal/*.md 2>/dev/null
```

If open entries are found, alert the user:

> You have [N] open debug entry/entries still marked as "Investigating". Want to update one of those instead, or start a new entry?

If the user wants to update, follow Path D. Otherwise continue to A1.

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

**Date:** [YYYY-MM-DD]

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

---

### Path D: Update Existing Debug Entry

#### D1: Find Open Entries

Search for unresolved debug entries:

```
Shell: grep -n "\\*\\*Status:\\*\\* Investigating" journal/*.md
```

Present the list of open entries with their titles and dates.

If no open entries are found, inform the user:

> No open debug entries found. All previous debug sessions are resolved.

#### D2: Select Entry to Update

```
AskQuestion:
  title: "Update Debug Entry"
  questions:
    - id: entry
      prompt: "Which entry do you want to update?"
      options:
        - "[Most recent investigating entry title] (Recommended)"
        - "I'll specify which one"
    - id: resolution
      prompt: "What happened?"
      options:
        - "Found the root cause and fixed it"
        - "Found the root cause, fix is pending"
        - "Giving up / no longer relevant"
```

#### D3: Update the Entry

Use `Edit` to update the existing entry in the weekly journal file:

1. Change `**Status:** Investigating` → `**Status:** Resolved`
2. Fill in the `### Root cause` section with the user's description
3. Fill in the `### Fix` section with the actual fix
4. Fill in the `### Lesson` section — prompt the user if not obvious from conversation

Report the update:

> Updated debug entry in `journal/2026-W28.md` — "Debug: Redis connection timeout" → Resolved

Then offer follow-ups (same as Step 4):

```
AskQuestion:
  title: "What's next?"
  questions:
    - id: next
      prompt: "Entry updated. Anything else?"
      options:
        - "Done for now (Recommended)"
        - "Log what I learned from this (→ Learning entry)"
        - "Turn this into a question for my team (→ Question Formatter)"
        - "Create a JIRA ticket for follow-up work (→ JIRA Tickets)"
```

---

## Journal Format Contract

Other skills in this repo depend on the journal's structure. If you change these, update the consuming skills.

**Consuming skills:** `one-on-one-prep`

| Element | Format | Used by |
|---------|--------|---------|
| Entry type heading | `## Debug:`, `## Learned:`, `## Reflection:` | one-on-one-prep (categorisation) |
| Status field | `**Status:** Resolved` or `**Status:** Investigating` | one-on-one-prep (wins vs blockers) |
| Date field | `**Date:** YYYY-MM-DD` | one-on-one-prep (period filtering) |
| Lesson field | `### Lesson` | one-on-one-prep (growth section) |
| Insight field | `### Insight` | one-on-one-prep (growth section) |
| File naming | `journal/YYYY-WNN.md` | one-on-one-prep (glob pattern) |

## Reading Previous Entries

When the user asks to review their journal:

1. Use `Glob` to list files in `journal/` matching `*.md`
2. Show available weeks
3. Let the user pick a week or search by keyword
4. Display entries, optionally filtered by type (Debug / Learning / Reflection)

## Important Notes

- Journal files live in `journal/` at the project root by default. Create the directory if it doesn't exist.
- **Multi-repo setups:** If the user works across multiple repositories, suggest using a single shared journal location (e.g. `~/engineering-journal/`) instead of per-project journals. This avoids fragmenting entries across repos and ensures 1:1 prep can pull from all work. Offer this as a configuration option in Step 2 when the user first creates an entry.
- File naming uses ISO 8601 week dates: `YYYY-WNN.md` (e.g. `2026-W28.md`).
- Always **append** to existing weekly files, never overwrite.
- Date format within entries is ISO 8601 (`YYYY-MM-DD`).
- Debug entries with status "Investigating" should be flagged when the user starts a new debug entry — prompt them to update the old one.
- End-of-day reflections should reference debug and learning entries from the same day to avoid duplication — summarise rather than repeat.
- The `Lesson` field in debug entries and the `Insight` field in learning entries are the most valuable fields — encourage the user to be specific. "I learned about caching" is weak; "Redis SCAN is O(1) per call but O(N) total — don't use it in a request path" is useful.
- This journal integrates with the **1:1 Prep** skill — recent entries are pulled automatically to populate wins and growth sections.
