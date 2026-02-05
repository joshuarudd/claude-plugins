---
name: lookup-day-of-week
description: Lookup the current day of the week, or the day of the week for a specific date. Use whenever you need to know, state, or verify what day of the week a date falls on.
user-invocable: false
---

# Day of Week Lookup

When you need to know or state a day of the week, **never calculate it yourself**. Always use the system `date` command to get the authoritative answer.

## Current date

```bash
date +"%A, %B %d, %Y"
```

Returns output like: `Saturday, January 24, 2026`

## Specific date

To find the day of the week for a specific date, try the GNU date syntax first:

```bash
date -d "2024-07-04" +"%A, %B %d, %Y"
```

If that fails (macOS/BSD), use:

```bash
date -j -f "%Y-%m-%d" "2024-07-04" +"%A, %B %d, %Y"
```

Replace `2024-07-04` with the target date in YYYY-MM-DD format.

## Rules

- Never guess or calculate the day of the week from memory.
- Always run the command and use the returned result.
- If the command fails, inform the user rather than guessing.
