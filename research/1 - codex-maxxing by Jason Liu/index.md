# Voice Input
- Use a Whisper Workflow to perfect your voice input before it reaches the AI. See https://www.typewhisper.com/en/docs/mac/workflows/ on how to setup a local whisper flow. Have AI change your rambling to clear instructions.

# Steering
- If you're using the Codex App, check your steering settings to let your chats steer instantly.
- Keep chatting and/or queue'ing chats to steer the agent. Don't wait.

# Memory
- In Codex, currently, you can enable Memories through the experimental features.
- You can also create you own vault.
- Keep in mind that Grep is king, vaults like Supermemory/MemPalace and so on still underperform.
- In your global AGENTS.md or copilot-instructions.md, tell the model to write things down as it learns about people, learns about progress of projects, decisions that have been made, or close an open loop, it should update the relevant pages in the vault.
- Have the vualt be a self-contained GitHub repository so the agent can see the diffs as surface memory.

# Computer and Browser Use
- Have variables like $browser, $chrome, @computer etcetera to point Codex to use those tools (these variables need to be configured).
- When browser or app use is restricted, install a browser MCP (https://github.com/chromeDevTools/chrome-devtools-mcp/ or better https://agent-browser.dev/cdp-mode) and login to your Teams, Slack, Mails, etc and have the sessions persisted. It can then do read/write actions headless. Have a subagent review the click actions.

```md
# AGENTS.md
Interpret these tags as workflow hints:
- `@browser`: use Browser Use or the in-app browser for web tasks that do not require a signed-in Chrome profile.
- `@chrome`: use the Chrome DevTools MCP server for authenticated browser tasks, DOM inspection, network inspection, and persistent logged-in sessions.
- `@computer`: use Computer Use for native desktop apps or flows that require OS-level interaction.
```

# Heartbeats
- Plan recurring checks such as keep an eye on X for a few hours and use the browser.

For example:
```md
Every 30 minutes, check Teams, Calendar and Outlook for unanswered messages/e-mails that need my attention.
Help me prioritize what matters most.
If someone asks me a question, research the answer as deeply as you can and draft a reply for me, but do not send it.
``` 

For example:
```md
Keep an eye on this every weekday morning.
Check Slack, Gmail, and Calendar.
Tell me:
- what needs my attention
- what I should prepare for
- what changed that I might miss
- what seems blocked on me

Keep it short.
If nothing important changed, say that.
```

# Goals
- Codex specific use /goal to persue long running tasks until completion.
- GitHub copilot has autopilot mode that leans very much into this.

# Morning Brief
- Ask your AI to send you a brief every morning (using Browser Tools/MCP or app connection): Using Teams, Outlook, and Teams Calendar, what do I have going on today?
- **Also let the brief draft the work.**

Example of the prompt:
```md
Give me a morning brief.

Before writing my morning brief:
- review TODO.md for open loops
- review people/ for relevant collaborator context
- review projects/ for active workstream state
- review notes/ for recent background that may change today's priorities

Use that vault context to make the brief more specific and less forgetful.

Focus on:
- people waiting on me
- meetings that need preparation
- commitments I made yesterday that are still open
- anything that changed overnight and affects today's priorities

Use clear headers and bullet points.

Organize the brief into:
- To-dos
- Needs replies
- Project-level updates

For anything in "Needs replies":
- include the direct Slack link
- say what the person is waiting on
- include just enough context for me to decide whether to answer now

Keep it short, structured, and easy to scan.
```

# Sources
- https://jxnl.co/writing/2026/05/18/six-levels-of-complexity-of-an-ai-powered-morning-brief-with-codex/#why-i-like-this-ladder
- https://jxnl.co/writing/2026/05/10/codex-maxxing/