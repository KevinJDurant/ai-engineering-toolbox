---
name: handoff
description: Use when the user asks for a handoff, says "do a handoff", wants to continue later, or wants another agent/session to pick up the work. Create the handoff file directly in a local handoff log; do not ask for confirmation unless required information is missing.
---

Create a Markdown handoff file for the current session. Do not stop at a chat summary.

## Process

1. Inspect the conversation and relevant repo state.
2. Write `.codex/handoff/handoff-YYYYMMDD-HHMMSS.md`, using local time.
3. Create `.codex/handoff/` if needed.
4. If inside a Git repo, add `.codex/handoff/` to `.git/info/exclude` if needed. Do not edit `.gitignore` unless asked.
5. If the user asks for a shareable or committed handoff, write under `docs/handoff/` instead.
6. Report the created file path.

## Content

Include only what a fresh agent needs:

- current state
- decisions made
- relevant files, paths, branches, commits, issues, PRs, or URLs
- commands run when they matter
- uncommitted work, failing checks, blocked commands, or missing approvals
- open questions, risks, and next steps
- suggested skills, if any

Do not include secrets, credentials, tokens, private keys, or unrelated personal data.

If the user provides a focus area, tailor the handoff to that focus.
