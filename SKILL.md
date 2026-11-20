---
name: time-awareness
description: Agent temporal awareness guardrail. Ensures the correct current date is used for all relative-time and current-events queries. Use when a query requires knowing today, now, or current events to produce a correct answer.
license: MIT
compatibility: Any agent runtime with a tool that returns the current date (Claude Code, Codex, Cursor, Gemini CLI, and other Agent Skills runtimes)
metadata:
  author: manteclaw
  version: "1.0.0"
  tags: time date awareness temporal utilities
---

## Time Awareness

**When a query requires knowing "today" or "now" to produce a correct answer, you MUST check the current date first, using your runtime's date tool.** This applies to ALL relative-time and current-events queries.

### Which tool is the date tool?

Use whatever your runtime provides to get the current date: a shell command such as `date` (or `date -u`), a built-in status/date tool, or any equivalent. The name does not matter — what matters is that the date comes from the runtime, not from training data.

If your runtime has no date tool and no shell, ask the user for today's date before answering a time-sensitive query — do not guess it from training data.

### Mandatory Two-Step Process

1. Call your date tool **ALONE** — never batch it with search or other tools.
2. **WAIT** for the result. Only after receiving the current date, construct your query with the returned year/month/day.

Calling the date tool and search tools in the same turn, or skipping the date check entirely, will produce wrong-year queries.

### When the Date Check Is Required

| Scenario | Action |
|----------|--------|
| User gives a **specific date** | Use it directly, no date check needed |
| User uses **relative time** ("最近", "过去N天", "接下来一周", "last 3 days", "this week", etc.) | MUST check the date first → compute absolute date range → include in query |
| **Current events** without time spec (news, prices, rankings, schedules, countdowns, etc.) | MUST check the date first → include current year+month in query |

### Year Self-Check

Before submitting any search query, verify every year in it comes from your date tool. If you see a training-data year (e.g. "2025") in your query about CURRENT events — STOP, you skipped or ignored the date check. Go back to step 1.

### Reply Integrity

Present ONLY information traceable to tool outputs in the current session. Do NOT fill gaps with training-data "background knowledge" (numbers, dates, prices, details not in tool results). If results are incomplete, say so — don't guess.

### Fallback

If all tool calls fail or you cannot obtain current date/real-time data, explicitly tell the user the result may be inaccurate. Never present training-knowledge dates as fact.
