---
name: capture-issue
description: Capture review feedback as GitHub issues during development sessions. Use when the user provides feedback, enhancement ideas, bugs, or observations about a project and wants them logged as GitHub issues without disrupting the current working context. Triggers on the /capture-issue slash command or when the user explicitly asks to capture feedback as issues. Supports single and batch issue creation—automatically determines the number of discrete issues from the feedback provided.
---

# Issue Capture

Capture review feedback as GitHub issues via the `gh` CLI, isolated from the main working context using the Task tool.

## Workflow

1. Analyze the user's feedback to determine discrete issues. Each distinct bug, enhancement, or observation is a separate issue.
2. Spawn a Task tool sub-agent with the instructions below.
3. Confirm creation with issue numbers and titles. Keep confirmation brief.

## Task Tool Instructions

Pass the following to the Task tool sub-agent:

### Context to include
- The parsed feedback (separated into discrete issues if multiple)
- The current working directory (for repo detection)

### Sub-agent steps

1. Verify `gh` is authenticated: `gh auth status`
2. Detect the repo: `gh repo view --json nameWithOwner -q .nameWithOwner`
3. Fetch available labels: `gh label list --limit 100 --json name -q '.[].name'`
4. For each discrete issue:
   - Write a concise, specific title (imperative mood, e.g., "Fix date picker locale handling")
   - Write a description with: a summary of the problem or enhancement, expected behavior or desired outcome, and any relevant context from the feedback
   - Match 1–3 labels from the available labels if appropriate; skip labeling if no labels fit
   - Create the issue: `gh issue create --title "..." --body "..." --label "label1,label2"`
5. Return the issue number, title, and URL for each created issue

## Parsing Feedback into Issues

Use judgment to separate feedback into discrete issues. Signals for splitting:

- Different UI areas or components mentioned
- Separate bugs vs. stories vs. opportunities (i.e., gaps, problems)
- Unrelated concerns joined by "also," "and another thing," or similar

When ambiguous, prefer fewer issues with broader scope over many granular ones. The user can always break them down later.

## Label Matching

Match labels by semantic meaning, not exact keyword match. For example:

- Feedback about something broken → `bug`, `defect`, or similar
- Feedback about desired improvements → `story`, `enhancement`, `feature`, `improvement`, or similar
- Feedback about visual/styling concerns → `ui`, `design`, `ux`, or similar
- Feedback about gaps or problems with the system  → `opportunity`, `gap`, or similar

Do not create new labels. Only use labels that already exist in the repo.
