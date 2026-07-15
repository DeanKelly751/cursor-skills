# Cursor Agent Skills

A collection of reusable [Cursor](https://cursor.com) agent skills.

## Skills

### JIRA Ticket Creation

**Path:** `jira-tickets/SKILL.md`

Create JIRA tickets at any hierarchy level (Epic, Story, Task, Sub-task, Bug) via the GitKraken MCP integration. The skill infers as much as possible from conversation context and only asks for what it can't determine.

**Requirements:**
- [GitLens extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) with a connected JIRA integration
- GitKraken MCP server enabled in Cursor

**Usage:**
1. Copy `jira-tickets/SKILL.md` into your project's `.cursor/skills/jira-tickets/` directory
2. Ask the agent to create a JIRA ticket — it will pick up the skill automatically

### GitHub Issue Creation

**Path:** `github-issues/SKILL.md`

Create GitHub issues on any repo from a context document or conversation. The skill parses raw input, improves titles and descriptions, infers labels, presents a review table for approval, and then creates the issues.

**Requirements:**
- [GitLens extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) with a connected GitHub integration
- GitKraken MCP server enabled in Cursor

**Usage:**
1. Copy `github-issues/SKILL.md` into your project's `.cursor/skills/github-issues/` directory
2. Provide a context doc or describe the issues you want to create
3. The agent will parse, improve, and present them for review before creating

### Weekly Status Report

**Path:** `weekly-report/SKILL.md`

Generate a weekly status report by aggregating Git commits, JIRA tickets, GitHub issues, and pull requests. Categorises work into completed, in-progress, and blockers, then formats as markdown, Slack-ready, or email.

**Requirements:**
- [GitLens extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) with connected JIRA and/or GitHub integrations
- GitKraken MCP server enabled in Cursor

**Usage:**
1. Copy `weekly-report/SKILL.md` into your project's `.cursor/skills/weekly-report/` directory
2. Ask the agent for a weekly status report or standup summary

### ADR Writer

**Path:** `adr-writer/SKILL.md`

Capture architecture decisions as ADRs (Architecture Decision Records) using the standard Nygard template. Extracts context, decision, and alternatives from conversation, auto-numbers the file, and writes it to the project.

**Requirements:**
- No external integrations required — works with the filesystem only

**Usage:**
1. Copy `adr-writer/SKILL.md` into your project's `.cursor/skills/adr-writer/` directory
2. Discuss a technical decision in conversation, then ask the agent to record it as an ADR

### RFC / Proposal Drafter

**Path:** `rfc-drafter/SKILL.md`

Draft RFCs and technical proposals from conversation context. Structures ideas into a formal document with summary, motivation, proposal, alternatives, and open questions. Supports multiple output formats and depth levels.

**Requirements:**
- No external integrations required — works with the filesystem only

**Usage:**
1. Copy `rfc-drafter/SKILL.md` into your project's `.cursor/skills/rfc-drafter/` directory
2. Discuss a technical idea in conversation, then ask the agent to draft an RFC or proposal

### PR Description Generator

**Path:** `pr-description/SKILL.md`

Draft a pull request title and description from the current branch's commits and diff, filling in the repo's PR template if one exists. Optionally creates the PR via the GitHub CLI.

**Requirements:**
- No external integrations required — works with local git only
- [GitHub CLI](https://cli.github.com/) (`gh`) if you want the skill to create the PR for you; otherwise it just drafts the markdown

**Usage:**
1. Copy `pr-description/SKILL.md` into your project's `.cursor/skills/pr-description/` directory
2. Ask the agent to draft a PR description for the current branch

### Self-Review Against Reviewer Checklists

**Path:** `self-review/SKILL.md`

Run a self-review of uncommitted or branch changes against this repo's existing reviewer checklists (`reviewers/review-*`), routing changed files to the relevant reviewers and producing one consolidated findings report.

**Requirements:**
- The `reviewers/` skill files must be present alongside this skill (they already are in this repo)

**Usage:**
1. Copy both `self-review/SKILL.md` and the `reviewers/` directory into your project's `.cursor/skills/` directory
2. Ask the agent to self-review your changes before opening a PR

### Repo Orientation / Onboarding Guide

**Path:** `repo-orientation/SKILL.md`

Generate a repo orientation/onboarding guide by detecting the tech stack, mapping the directory structure, finding entry points, and summarising conventions, tooling, and CI/CD.

**Requirements:**
- No external integrations required — works with the filesystem only

**Usage:**
1. Copy `repo-orientation/SKILL.md` into your project's `.cursor/skills/repo-orientation/` directory
2. Ask the agent to generate an onboarding guide or help you understand the codebase

### Engineering Journal

**Path:** `engineering-journal/SKILL.md`

Log debug sessions, learnings, and end-of-day reflections into a structured weekly journal. Combines rubber-duck debugging with a learning log. Entries are searchable and feed into 1:1 prep and weekly reports.

**Requirements:**
- No external integrations required — works with the filesystem and git only

**Usage:**
1. Copy `engineering-journal/SKILL.md` into your project's `.cursor/skills/engineering-journal/` directory
2. Ask the agent to log a debug session, record a learning, or do an end-of-day reflection

### Code Explainer / Onboarding

**Path:** `code-explainer/SKILL.md`

Explain code at any scope — file, directory, or full repo. Supports quick scan, deep dive, system context, and onboarding map modes. Traces request paths, names patterns, flags newcomer traps, and cross-references knowledge skills.

**Requirements:**
- No external integrations required — works with the filesystem and git only

**Usage:**
1. Copy `code-explainer/SKILL.md` into your project's `.cursor/skills/code-explainer/` directory
2. Ask the agent to explain a file, walk through a module, or give you an onboarding map of the repo

### Question Formatter

**Path:** `question-formatter/SKILL.md`

Turn a messy "it's not working" into a structured, answerable technical question. Guards against the XY Problem, auto-gathers code context and errors, and formats for Slack, GitHub, or verbal delivery.

**Requirements:**
- No external integrations required — works with the filesystem and git only

**Usage:**
1. Copy `question-formatter/SKILL.md` into your project's `.cursor/skills/question-formatter/` directory
2. Ask the agent to help you formulate a question for your team or format a question for Slack

### 1:1 Prep

**Path:** `one-on-one-prep/SKILL.md`

Prepare for 1:1 meetings with your manager by auto-pulling recent work activity (commits, PRs, tickets) and prompting for reflection on wins, blockers, learning, and discussion topics.

**Requirements:**
- [GitLens extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) with connected JIRA and/or GitHub integrations (optional — falls back to git log)
- GitKraken MCP server enabled in Cursor (optional)

**Usage:**
1. Copy `one-on-one-prep/SKILL.md` into your project's `.cursor/skills/one-on-one-prep/` directory
2. Ask the agent to prepare for your 1:1 or prep for a meeting with your manager

## License

MIT
