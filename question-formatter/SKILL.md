---
name: question-formatter
description: >-
  Turn a messy "it's not working" into a structured, answerable technical question.
  Guards against the XY Problem, auto-gathers code context and errors, and formats
  for Slack, GitHub, or verbal delivery. Use when the user wants to ask a question
  to their team, post on Slack, write a GitHub comment, or needs help formulating
  a technical question.
---

# Question Formatter

Turn a vague problem description into a clear, well-structured technical question that gets fast answers.

## Core Principle

**A well-asked question gets answered in 5 minutes. A poorly-asked one gets ignored for 2 hours.**

Structure the question so the reader immediately understands: what you're trying to do, what went wrong, and what you've already ruled out.

## Why This Matters

Senior engineers prioritise questions that are:
1. **Specific** — not "it's broken" but "the /api/users endpoint returns 500 when the email field contains a plus sign"
2. **Contextualised** — shows what you've already tried, so they don't suggest things you've ruled out
3. **Minimal** — includes only the relevant code, not the whole file
4. **Goal-oriented** — explains what you're ultimately trying to accomplish, not just your current approach

## Workflow

### Step 1: Understand the Problem

Scan the current conversation for:
- **What went wrong** (error messages, unexpected behaviour)
- **What was expected** (correct behaviour)
- **What was tried** (debugging steps, attempted fixes)
- **What the user is working on** (broader task context)

If the conversation doesn't contain enough, ask:

```
AskQuestion:
  title: "Question Formatter"
  questions:
    - id: problem
      prompt: "What's going wrong? (error, unexpected behaviour, confusion, etc.)"
      options:
        - "I'll describe it"
    - id: goal
      prompt: "What are you ultimately trying to accomplish? (not your current approach — the end goal)"
      options:
        - "I'll describe it"
```

### Step 2: XY Problem Guard

The [XY Problem](https://xyproblem.info/) is when you ask about your attempted *solution* instead of the actual *problem*. This is especially common for interns who may not know the idiomatic approach yet.

**Check:** Does the described problem match the stated goal? If the user says "How do I parse this regex?" but the goal is "I need to validate email addresses", the real question is about email validation, not regex.

If you detect an XY mismatch, flag it:

> It sounds like you're asking about [X], but your actual goal is [Y]. Senior engineers will give better help if you ask about [Y] directly. I'll frame the question around your real goal.

### Step 3: Auto-Gather Technical Context

Pull relevant context automatically:

- **Current file snippet:** Read the file the user is working on. Extract only the relevant function or block — not the entire file.
- **Error messages:** If errors were mentioned in conversation, include them verbatim.
- **Git diff:** `Shell: git diff --stat` — shows what's been changed, useful for "it was working before".
- **Language/framework version:** Read from package.json, go.mod, requirements.txt, etc.

### Step 4: Choose Output Format

```
AskQuestion:
  title: "Output Format"
  questions:
    - id: format
      prompt: "Where are you asking this question?"
      options:
        - "Slack message (Recommended)"
        - "GitHub issue or PR comment"
        - "Verbal — I'll ask in person or standup"
```

### Step 5: Compose the Question

Use the template matching the format.

---

#### Slack Format

```
*[Concise title of the question]*

*What I'm trying to do:*
[1-2 sentences — the ultimate goal, not the attempted solution]

*What's happening:*
[Concrete description of the problem — include error messages in a code block]

*What I've tried:*
• [Step 1] → [result]
• [Step 2] → [result]

*What I've ruled out:*
• [Thing checked that wasn't the cause]

*Relevant code:* _(omit this section if the question is about process, infrastructure, or domain knowledge rather than code)_
\`\`\`[language]
[Minimal code snippet — only what's needed to understand the problem]
\`\`\`

*Environment:* [language version, framework, OS if relevant — omit if not applicable]

Any pointers appreciated!
```

---

#### GitHub Format

```markdown
## Question: [Concise title]

### Goal
[What I'm ultimately trying to accomplish]

### Problem
[What's going wrong — include error messages in code blocks]

### What I've tried
- [Step 1] → [result]
- [Step 2] → [result]

### What I've ruled out
- [Thing that isn't the cause and why]

### Relevant code _(omit if not a code question)_

\`\`\`[language]
[Minimal snippet]
\`\`\`

### Environment _(omit if not applicable)_
- [Language/framework version]
- [OS if relevant]
- [Any relevant config]
```

---

#### Verbal Format

A short, structured script for asking in person or at standup:

```
Hey [name], I've got a question about [area].

I'm trying to [goal].

I'm seeing [symptom] — here's the error: [key error message].

I've already checked [thing 1] and [thing 2], so I don't think it's [ruled-out cause].

My best guess is [hypothesis] — does that sound right, or am I looking in the wrong place?
```

---

### Step 6: Present for Review

Display the formatted question and ask:

```
AskQuestion:
  title: "Review Question"
  questions:
    - id: approve
      prompt: "Here's your formatted question. How does it look?"
      options:
        - "Looks good, I'll send it (Recommended)"
        - "I want to adjust it"
        - "Switch to a different format"
        - "Also log this as a debug entry (→ Engineering Journal)"
```

## Question Quality Checklist

Before finalising, verify the question passes these checks:

| Check | Pass? |
|-------|-------|
| States the **goal**, not just the attempted approach | |
| Includes a **concrete symptom** (error message, wrong output, unexpected behaviour) | |
| Shows **what was tried** with results | |
| Includes a **minimal code snippet** if the question involves code (not the whole file) | |
| Specifies **environment** where relevant | |
| Is **self-contained** — a reader doesn't need to ask "what do you mean?" | |

## Important Notes

- **Never include secrets, tokens, passwords, or API keys** in code snippets. Before including any code, scan for these patterns and replace matches with `[REDACTED]`:
  - Variable names containing: `secret`, `password`, `passwd`, `token`, `api_key`, `apikey`, `api_secret`, `credential`, `private_key`, `access_key`, `secret_key`
  - String literals matching: `Bearer `, `Basic `, `sk-`, `pk-`, `ghp_`, `gho_`, `github_pat_`, `xoxb-`, `xoxp-`, `AKIA` (AWS key prefix)
  - PEM blocks: `-----BEGIN`
  - Connection strings: `://.*:.*@` (contains embedded credentials)
  - Environment variable references: `.env` file contents, `process.env.SECRET_*`
  - See `security/knowledge-secure-by-design` for broader guidance on handling sensitive data.
- **Minimal snippets only.** A 5-line snippet gets read. A 50-line dump gets skipped. Trim to the smallest code that shows the problem.
- **Error messages verbatim.** Don't paraphrase errors — copy them exactly, including stack traces (trimmed to the relevant frames).
- **Don't answer the question.** This skill formats the question — it doesn't try to solve the problem. If you know the answer, tell the user directly instead of formatting a question.
- **Adapt the tone.** Slack is casual, GitHub is professional, verbal is conversational. Match the context.
- **Include what was ruled out.** This is the most underrated part. It saves the answerer from suggesting things you've already tried, and shows you did your homework.
- This skill integrates with the **Engineering Journal** — a well-formatted question can be logged as a debug entry after the problem is resolved.
