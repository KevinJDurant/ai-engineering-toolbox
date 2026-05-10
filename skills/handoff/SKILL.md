---
name: handoff
description: Use when the user asks for a handoff, says "do a handoff", wants to continue later, or wants another agent/session to pick up the work. Create the handoff file directly; do not ask for confirmation unless required information is missing.
---

Create a handoff document for the current session and save it in this repository.

## Required Behavior

1. Inspect the current conversation and relevant repository state.
2. Create `docs/handoff/` if it does not already exist.
3. Write a new Markdown file at `docs/handoff/handoff-YYYYMMDD-HHMMSS.md`, using local time.
4. Report the created file path to the user.

Do not stop at a summary in chat. The output of this skill is the handoff file.

## Handoff Content

Use this structure:

- `# Handoff: <short task name>`
- `## Current State`
- `## Decisions Made`
- `## Files Changed`
- `## Commands Run`
- `## Open Questions`
- `## Next Steps`
- `## Suggested Skills`

Omit sections that do not apply.

## Content Rules

- Be concise and specific.
- Include exact paths for files and directories.
- Include branch names and commit hashes when relevant.
- Include commands already run only when they matter for continuation.
- Call out uncommitted work, failing checks, blocked commands, or missing approvals.
- Reference existing artifacts by path, branch, commit, issue, PR, or URL instead of duplicating their contents.
- Do not include secrets, credentials, tokens, private keys, or unrelated personal data.

If the user provides a focus area, tailor the handoff to that focus.
