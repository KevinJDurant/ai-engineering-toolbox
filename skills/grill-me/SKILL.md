---
name: grill-me
description: Use when the user asks to be grilled, challenged, stress-tested, pressure-tested, or interviewed about a plan, architecture, product idea, prompt, workflow, design, or implementation strategy. Ask one decision-focused question at a time, give a recommended answer, and keep a local question log.
---

Interrogate the user's plan relentlessly until the important assumptions, risks, tradeoffs, and next decisions are explicit.

## Process

1. If repo facts can answer a question, inspect the codebase or local artifacts instead of asking.
2. Ask one high-impact question at a time.
3. For each question, include why it matters, your recommended default, and the tradeoff if the user chooses differently.
4. Use each answer to choose the next question.
5. Stop when the plan is executable, the user asks to stop, or the remaining uncertainty is explicitly accepted.

## Question Log

- Keep a Markdown log at `.codex/grilling/grill-YYYYMMDD-HHMMSS.md`, using local time.
- Create `.codex/grilling/` if it does not exist.
- If inside a Git repo, add `.codex/grilling/` to `.git/info/exclude` if needed. Do not edit `.gitignore` unless asked.
- If the user asks for a shareable record, write it under `docs/grilling/` instead.
- Track only useful context: questions, answers, decisions, open risks, and the next action.

## Rules

- Do not dump a checklist.
- Do not accept vague answers; ask a sharper follow-up.
- Do not re-ask answered questions.
- Keep drilling until every decision-changing assumption is answered, rejected, or explicitly accepted as a risk.
- When stopping, summarize decisions, open risks, next action, and the log path.
