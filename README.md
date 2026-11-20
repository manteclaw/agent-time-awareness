# agent-time-awareness

A prompt-only skill that stops agents from answering time-sensitive questions with the wrong year.

## The problem it prevents

Coding agents answer from training data unless they are forced to check the date first. A typical failure looks like this:

> **User:** "What happened in crypto in the last 3 days?"
>
> **Agent (no date check):** searches for `crypto news 2025` — a year baked in from its training data — and returns a confident summary of events from the wrong year, without ever noticing.
>
> **Agent (with this skill):** checks the current date first, computes the absolute range (e.g. Nov 17–20, 2026), searches with those dates, and answers from results that actually cover the last 3 days.

The failure is silent: the answer reads fine, cites real-looking events, and is simply wrong. The skill exists to make the date check happen before any search query is built, every time.

## What it does

- **Mandatory two-step rule:** check the current date first, alone, then build the query from the returned date. Never batch the date check with a search.
- **Coverage table:** which queries need the check (relative time, current events) and which don't (user gave an explicit date).
- **Year self-check:** before submitting a search, verify every year in the query came from the date tool. A training-data year in a "current events" query means the check was skipped — stop and go back.
- **Reply integrity:** only present information traceable to tool outputs in the current session; say so when results are incomplete instead of filling gaps from training data.
- **Fallback:** if the date cannot be obtained, say the result may be inaccurate rather than presenting a training-knowledge date as fact.

## Install

This is a prompt-only skill — no code, no dependencies, no API keys. Copy the whole `agent-time-awareness/` folder (or just `SKILL.md`) into your agent's skills directory:

| Agent | Skills folder |
|---|---|
| Claude Code | `~/.claude/skills/agent-time-awareness/` |
| Codex | `~/.codex/skills/agent-time-awareness/` |
| Cursor | `~/.cursor/skills/agent-time-awareness/` |
| Gemini CLI | `~/.gemini/skills/agent-time-awareness/` |

(Exact paths can vary by version — any folder your agent loads `SKILL.md` files from works. Project-local skills folders, e.g. `.claude/skills/`, work the same way.)

## Adapting the date tool

The skill refers to "your runtime's date tool" rather than naming one, because the name differs per runtime. In practice that usually means running `date` (or `date -u`) through the shell tool, or calling whatever status/date tool your runtime provides.

If your runtime has no date tool and no shell, the skill tells the agent to ask the user for today's date instead of guessing.

## License

MIT — see [LICENSE](LICENSE).
