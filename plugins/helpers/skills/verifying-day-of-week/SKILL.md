---
name: verifying-day-of-week
description: Use when you need to know, state, or verify what day of the week a date falls on — for today or any specific date. Never calculate days of the week from memory.
---

# Day of Week Lookup

## Overview

When you need to know or state a weekday or calendar date, **never calculate it yourself**. Always use the system `date` command to get the authoritative answer.

## Current date

```bash
date +"%A, %B %d, %Y"
```

Returns output like: `Saturday, January 24, 2026`

## Specific date

To find the day of the week for a specific date (replace `2024-07-04` with the target date in YYYY-MM-DD format):

**macOS/BSD:**
```bash
date -j -f "%Y-%m-%d" "2024-07-04" +"%A, %B %d, %Y"
```

**Linux/GNU:**
```bash
date -d "2024-07-04" +"%A, %B %d, %Y"
```

Use `uname` to detect the platform if unsure which syntax to use.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Calculating the day of the week from memory or training data | Always run the `date` command — never guess |
| Guessing when the command fails | Inform the user of the failure instead |
| Using GNU syntax on macOS without fallback | Check `uname` or use the macOS/BSD variant |
