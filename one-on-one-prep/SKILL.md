---
name: one-on-one-prep
description: >-
  Prepare for 1:1 meetings with your manager by auto-pulling recent work activity
  (commits, PRs, tickets) and prompting for reflection on wins, blockers, learning,
  and discussion topics. Pulls from the engineering journal if available. Use when
  the user asks to prepare for a 1:1, prep for a meeting with their manager, or
  wants a summary of what they've been working on for a check-in.
---

# 1:1 Prep

Prepare structured notes for a 1:1 meeting with your manager.

## Core Principle

**Data first, then reflect. Show up prepared, not winging it.**

Auto-pull your recent activity so you don't forget anything, then layer on the human parts — what you're proud of, where you're stuck, and what you want to talk about.

## How This Differs From a Weekly Report

| | Weekly Report | 1:1 Prep |
|---|---|---|
| **Perspective** | Backward-looking, factual | Forward-looking, reflective |
| **Audience** | Team / stakeholders | Your manager |
| **Purpose** | "Here's what I did" | "Here's what I need from you" |
| **Tone** | Professional summary | Candid, conversational bullets |

## Workflow

### Step 1: Confirm Parameters

Use `AskQuestion`:

```
AskQuestion:
  title: "1:1 Prep"
  questions:
    - id: period
      prompt: "How far back should I look?"
      options:
        - "Since last 1:1 — I'll give the date"
        - "Last 7 days (Recommended)"
        - "Last 14 days"
    - id: manager
      prompt: "Who is your manager? (for framing the notes)"
      options:
        - "I'll type their name"
```

If the user selects "Since last 1:1", ask for the date:

```
AskQuestion:
  title: "Last 1:1 Date"
  questions:
    - id: last_meeting
      prompt: "When was your last 1:1? (approximate is fine)"
      options:
        - "Last Monday"
        - "Last Thursday"
        - "I'll type the date"
```

### Step 2: Gather Activity Data

Pull from all available sources **in parallel**:

**Git commits:**
```
Shell: git log --oneline --since="[period]" --author="$(git config user.name)"
```

**JIRA tickets (if available):**
```
CallMcpTool:
  server: user-eamodio.gitlens-extension-GitKraken
  toolName: issues_assigned_to_me
  arguments:
    provider: "jira"
```

**GitHub PRs (open):**
```
CallMcpTool:
  server: user-eamodio.gitlens-extension-GitKraken
  toolName: pull_request_assigned_to_me
  arguments:
    provider: "github"
```

**GitHub PRs (recently merged):**
```
CallMcpTool:
  server: user-eamodio.gitlens-extension-GitKraken
  toolName: pull_request_assigned_to_me
  arguments:
    provider: "github"
    is_closed: true
```

**Engineering journal (if available):**
```
Glob: journal/*.md
```

If journal files exist for the period, read them and extract:
- Debug entries with status "Resolved" → potential **wins**
- Learning entries → **growth** section
- Debug entries with status "Investigating" → potential **blockers**
- Reflection entries → **discussion topics**

### Step 3: Prompt for Reflection

Present the gathered activity summary, then ask the user for the parts that can't be automated:

```
AskQuestion:
  title: "Reflect Before Your 1:1"
  questions:
    - id: wins
      prompt: "Anything you're proud of that the data might not show? (helped a teammate, figured out something hard, etc.)"
      options:
        - "Yes, I'll describe it"
        - "The data covers it"
    - id: blockers
      prompt: "Anything blocking you that your manager could help with?"
      options:
        - "Yes, I need help with something"
        - "No blockers right now"
    - id: growth
      prompt: "Anything you want to learn or get better at?"
      options:
        - "Yes, I have something in mind"
        - "Not sure yet"
    - id: topics
      prompt: "Anything else you want to discuss? (feedback, career, team, process, etc.)"
      options:
        - "Yes, I have a topic"
        - "Nothing beyond the usual"
```

### Step 4: Compose the Prep Notes

Use this template:

```markdown
# 1:1 Prep — [date]

**Period:** [start] – [end]
**Meeting with:** [manager name]

## Wins

[Things completed, shipped, or figured out. Pull from merged PRs, closed tickets, and resolved debug entries. Include the user's additions from the reflection prompt.]

- Merged [PR #N](url) — [brief description]
- Closed [TICKET-N] — [brief description]
- [User-provided win]

## Current Work

[What's in progress right now. Pull from open PRs and active tickets.]

- [PR #N](url) — [status: in review / WIP / waiting on feedback]
- [TICKET-N] — [brief description and status]

## Blockers & Asks

[The most important section. What do you need from your manager?]

- **[Blocker description]** — What would help: [specific ask — review, decision, introduction, unblocking a dependency]

[If no blockers: "No blockers right now."]

## Learning & Growth

[What you've learned recently and what you want to learn next. Pull from journal learning entries.]

**Recently learned:**
- [Insight from journal or user input]

**Want to explore:**
- [Area the user wants to grow in]

## Discussion Topics

[Anything else to bring up — feedback, career goals, team dynamics, process suggestions.]

- [Topic from user input]

---

*Prepared with data from [N] commits, [N] PRs, and [N] tickets.*
```

### Step 5: Present for Review

Display the prep notes and ask:

```
AskQuestion:
  title: "Review 1:1 Prep"
  questions:
    - id: approve
      prompt: "Here are your 1:1 prep notes. How do they look?"
      options:
        - "Looks good (Recommended)"
        - "I want to adjust some sections"
        - "Save to a file"
```

### Step 6: Output

Based on user choice:

- **Looks good:** Display the final notes for the user to reference during the meeting.
- **Adjust:** Ask which sections to change and regenerate.
- **Save:** Write to `notes/one-on-one/[YYYY-MM-DD].md`. Create the directory if needed.

## Tips for Effective 1:1s (Shown Once)

The first time this skill is used, append these tips after the prep notes:

> **Quick tips for your 1:1:**
> - Lead with blockers — this is the highest-value use of your manager's time
> - Be specific in your asks — "Can you review PR #42?" beats "I need help"
> - Share what you learned — it shows growth and helps your manager calibrate your projects
> - It's OK to say "I don't know" — that's more useful than pretending
> - Ask for feedback — "Is there anything I should be doing differently?" opens the door

## Important Notes

- The MCP tool calls reuse the same integrations as the `weekly-report` skill. If those aren't configured, fall back to git log only.
- If no JIRA/GitHub integrations are available, skip those sections gracefully — git commits alone are still useful.
- The engineering journal integration is optional — if no `journal/` directory exists, skip that data source and rely on user input for the growth/blocker sections.
- Keep the notes as **bullet points, not prose**. These are glance-during-the-meeting notes, not a formal document.
- The **Blockers & Asks** section should always frame problems as actionable requests. Not "deployment is broken" but "Deployment is blocked by the DNS migration — could you check with the platform team on the timeline?"
- If the user has been using the journal consistently, the reflection prompts will be much easier to answer since the data is already captured. Encourage journal use as a habit that pays off at 1:1 time.
