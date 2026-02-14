# Issue Tracker Plugin Rename — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Rename the `product-development` plugin to `issue-tracker` and its `capture-issue` skill to `capturing-issues`, aligning with writing-skills naming conventions.

**Architecture:** File renames and content updates across plugin config, skill frontmatter, command file, and marketplace registry. No code changes — all configuration and documentation.

**Tech Stack:** Git, JSON, Markdown with YAML frontmatter

---

### Task 1: Rename plugin directory

**Files:**
- Rename: `plugins/product-development/` → `plugins/issue-tracker/`

**Step 1: Rename the directory**

```bash
mv plugins/product-development plugins/issue-tracker
```

**Step 2: Verify structure is intact**

```bash
ls -R plugins/issue-tracker/
```

Expected:
```
.claude-plugin/plugin.json
commands/capture-issue.md
skills/capture-issue/SKILL.md
```

**Step 3: Commit**

```bash
git add plugins/product-development plugins/issue-tracker
git commit -m "Rename product-development plugin directory to issue-tracker"
```

---

### Task 2: Rename skill directory

**Files:**
- Rename: `plugins/issue-tracker/skills/capture-issue/` → `plugins/issue-tracker/skills/capturing-issues/`

**Step 1: Rename the directory**

```bash
mv plugins/issue-tracker/skills/capture-issue plugins/issue-tracker/skills/capturing-issues
```

**Step 2: Verify**

```bash
ls plugins/issue-tracker/skills/capturing-issues/
```

Expected: `SKILL.md`

**Step 3: Commit**

```bash
git add plugins/issue-tracker/skills/
git commit -m "Rename capture-issue skill directory to capturing-issues"
```

---

### Task 3: Update plugin.json

**Files:**
- Modify: `plugins/issue-tracker/.claude-plugin/plugin.json`

**Step 1: Update name and description**

Change the file contents to:

```json
{
  "name": "issue-tracker",
  "description": "Capture and track actionable issues during development sessions.",
  "version": "1.0.0",
  "author": {
    "name": "Joshua Rudd"
  },
  "license": "MIT",
  "repository": "https://github.com/joshuarudd/ruddiments"
}
```

**Step 2: Verify JSON is valid**

```bash
python3 -c "import json; json.load(open('plugins/issue-tracker/.claude-plugin/plugin.json'))"
```

Expected: no output (valid JSON)

**Step 3: Commit**

```bash
git add plugins/issue-tracker/.claude-plugin/plugin.json
git commit -m "Update plugin.json with issue-tracker name and description"
```

---

### Task 4: Update SKILL.md frontmatter

**Files:**
- Modify: `plugins/issue-tracker/skills/capturing-issues/SKILL.md`

**Step 1: Update frontmatter name and description**

Replace the YAML frontmatter (lines 1-4) with:

```yaml
---
name: capturing-issues
description: Use when the user provides feedback, enhancement ideas, bugs, or observations during a development session and wants them captured as issues without disrupting the current working context.
---
```

Do NOT change anything below line 4 (the skill body content is out of scope).

**Step 2: Verify frontmatter parses correctly**

```bash
head -4 plugins/issue-tracker/skills/capturing-issues/SKILL.md
```

Expected:
```
---
name: capturing-issues
description: Use when the user provides feedback, enhancement ideas, bugs, or observations during a development session and wants them captured as issues without disrupting the current working context.
---
```

**Step 3: Commit**

```bash
git add plugins/issue-tracker/skills/capturing-issues/SKILL.md
git commit -m "Update SKILL.md frontmatter with capturing-issues name and description"
```

---

### Task 5: Update command file

**Files:**
- Modify: `plugins/issue-tracker/commands/capture-issue.md`

**Step 1: Update skill reference**

Replace the file contents with:

```
Capture the following feedback as issues. Read the capturing-issues SKILL.md for full instructions, then use the Task tool to create the issues in a sub-agent so the main session context stays clean. Return a brief confirmation with issue numbers, titles, and URLs.

$PROMPT
```

Note: "GitHub issues" changed to "issues" (tool-agnostic), and "capture-issue SKILL.md" changed to "capturing-issues SKILL.md".

**Step 2: Commit**

```bash
git add plugins/issue-tracker/commands/capture-issue.md
git commit -m "Update command file to reference renamed skill"
```

---

### Task 6: Update marketplace.json

**Files:**
- Modify: `.claude-plugin/marketplace.json`

**Step 1: Update the product-development entry**

Replace the `product-development` object in the `plugins` array with:

```json
{
  "name": "issue-tracker",
  "source": "./plugins/issue-tracker",
  "description": "Capture and track actionable issues during development sessions.",
  "version": "1.0.0"
}
```

**Step 2: Verify JSON is valid**

```bash
python3 -c "import json; json.load(open('.claude-plugin/marketplace.json'))"
```

Expected: no output (valid JSON)

**Step 3: Commit**

```bash
git add .claude-plugin/marketplace.json
git commit -m "Update marketplace.json with issue-tracker plugin entry"
```

---

### Task 7: Verify everything works

**Step 1: Confirm no references to old names remain**

```bash
grep -r "product-development" plugins/issue-tracker/ .claude-plugin/marketplace.json
grep -r "capture-issue" plugins/issue-tracker/skills/ plugins/issue-tracker/.claude-plugin/
```

Expected: no output from either command

**Step 2: Confirm directory structure**

```bash
ls -R plugins/issue-tracker/
```

Expected:
```
.claude-plugin/plugin.json
commands/capture-issue.md
skills/capturing-issues/SKILL.md
```

**Step 3: Test plugin loads**

```bash
claude --plugin-dir ./plugins/issue-tracker --print-config 2>&1 | head -20
```

Verify the plugin name shows as `issue-tracker`.
