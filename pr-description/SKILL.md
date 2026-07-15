---
name: pr-description
description: >-
  Draft a pull request title and description from the current branch's commits
  and diff, filling in the repo's PR template if one exists. Optionally creates
  the PR via the GitHub CLI. Use when the user asks to draft a PR description,
  write up a pull request, or open a PR for the current branch.
---

# PR Description Generator

Draft a clear, well-structured PR title and description from local git history — no MCP integration required.

## Core Principle

**Gather from git, fill the template, confirm, ship.**

Everything needed to draft a good PR description already lives in the branch's commits and diff. Extract it, structure it, and let the user review before anything is created.

## Workflow

### Step 1: Gather Branch Context

Run these via `Shell` (all read-only):

```bash
git branch --show-current
```

Determine the base branch — try `main`, then `master`, then whatever the default branch is reported as by:

```bash
git remote show origin | sed -n '/HEAD branch/s/.*: //p'
```

Then gather the commit log and diff stat since divergence:

```bash
git log <base>..HEAD --oneline
git diff <base>...HEAD --stat
git diff <base>...HEAD
```

If `git log <base>..HEAD --oneline` returns nothing (no commits yet, or working on uncommitted changes only), fall back to:

```bash
git diff --stat
git diff
```

and ask the user to briefly describe the intent of the change, since there's no commit history to infer it from.

### Step 2: Find a Repo PR Template

Use `Glob` to look for, in order of preference:
- `.github/PULL_REQUEST_TEMPLATE.md`
- `.github/pull_request_template.md`
- `docs/PULL_REQUEST_TEMPLATE.md`
- `PULL_REQUEST_TEMPLATE.md`

If found, `Read` it and use its section headings as the structure for Step 4 instead of the default template. If not found, use the default template below.

### Step 3: Infer a Linked Ticket

Look for a ticket key in the branch name or commit messages (e.g. `feature/PROJ-123-fix-thing` or a commit like `PROJ-123: fix thing` -> `PROJ-123`). Common patterns: `[A-Z]{2,}-\d+`. If nothing matches, omit the "Related" section rather than guessing.

### Step 4: Draft Title and Body

**Title**: Imperative mood, concise, no trailing period. If recent merged commit/PR titles on the base branch follow a convention (e.g. Conventional Commits like `fix:`, `feat:`), match it:

```bash
git log <base> --oneline -20
```

**Default body template** (used only when no repo template was found in Step 2):

```markdown
## Summary
[1-3 sentences: what changed and why, derived from commit messages and the diff]

## Changes
- [Bullet per logical change, derived from commit messages / diff stat]
- [...]

## Test Plan
- [ ] [Inferred from touched test files, e.g. "Added/updated unit tests in X"]
- [ ] [If no tests touched: "Manual verification: ..." — ask the user what they tested]

## Related
Closes TICKET-123   <!-- omit this line entirely if no ticket was inferred -->
```

If a repo template was found instead, fill its existing sections with the same inferred content — don't invent new sections it doesn't have.

### Step 5: Present for Review

```
AskQuestion:
  title: "Review PR Description"
  questions:
    - id: approve
      prompt: "Here's the drafted PR title and description. How does it look?"
      options:
        - "Looks good (Recommended)"
        - "I want to edit it"
        - "Regenerate"
```

If "I want to edit it", ask what to change and re-draft. If "Regenerate", re-run Step 4 with more emphasis on the diff content rather than commit messages (or vice versa).

### Step 6: Deliver

Always print the final title + markdown body in the chat.

Then ask how to proceed:

```
AskQuestion:
  title: "Next Step"
  questions:
    - id: action
      prompt: "What should I do with this?"
      options:
        - "Just show me the markdown (Recommended)"
        - "Create the PR with gh pr create"
        - "Push the branch first, then create the PR"
```

**If "Create the PR with gh pr create"**: first check `gh --version` succeeds via `Shell`. If it doesn't, tell the user the GitHub CLI isn't installed/authenticated and fall back to showing the markdown. If it does:

```bash
gh pr create --title "<TITLE>" --body "<BODY>"
```

Use a heredoc for the body to preserve formatting, matching this repo's convention:

```bash
gh pr create --title "<TITLE>" --body "$(cat <<'EOF'
<BODY>
EOF
)"
```

**If "Push the branch first, then create the PR"**: run `git push -u origin HEAD` (this requires `git_write`/network permissions — request them), then proceed with `gh pr create` as above.

Report back the PR URL that `gh pr create` prints on success.

## Handling Edge Cases

- **Branch has no upstream yet**: `gh pr create` will fail if the branch hasn't been pushed. Detect this and offer to push first.
- **No diff at all** (branch identical to base): tell the user there's nothing to describe rather than fabricating content.
- **Merge commits in the log**: filter noisy merge commits (`git log --no-merges`) when drafting the summary, but still count them in context if relevant.
- **Very large diffs**: summarize by file/module in the Changes section rather than trying to describe every line.

## Important Notes

- This skill works best when commits follow some consistent convention (Conventional Commits or similar) — it adapts to whatever pattern it detects rather than enforcing one.
- Never run `git push` or `gh pr create` without explicit approval from Step 6.
- If the current branch is the same as the detected base branch, stop and tell the user — there's no PR to describe.
- Ticket inference is best-effort; always let the user correct or remove it during Step 5.
