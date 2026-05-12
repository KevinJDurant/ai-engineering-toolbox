---
name: prompt-engineering-anthropic
description: Use when the user asks to improve, rewrite, debug, audit, or optimize a prompt for Anthropic Claude models, especially Claude Opus 4.7 and Claude 4.6-family prompts. Also use for Claude prompt design involving response length, effort and adaptive thinking, tool use, progress updates, literal instruction scope, tone, subagents, interactive coding products, code review harnesses, computer use, clear instructions, context, examples, XML tags, roles, long-context prompting, model identity, output formatting, agentic systems, research, coding-agent guardrails, and vision tasks.
---

Use Anthropic's Prompting best practices page as the source of truth: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices.

This skill is a section-by-section operational extract of that page. It intentionally omits the source page's migration sections and frontend design/style sections. If this skill and the source page differ, follow the source page.

## Select the prompt task

First classify the request:
- **Claude prompt optimization**: Improve a user-facing prompt for Claude. Preserve the user's intended task, reduce ambiguity, add missing context, define output requirements, and use XML tags when instructions, context, examples, and variable input are mixed.
- **Agent/system prompt design**: Create or refine durable instructions for an agent. Define role, autonomy, tool behavior, progress updates, context/state handling, validation, final-answer behavior, and safety boundaries.
- **Long-context or document prompting**: Put large inputs in the right place, preserve useful metadata, and ask Claude to ground answers in document evidence.
- **Tool-use or research prompting**: Define when Claude should act, use tools, run calls in parallel, verify sources, or answer directly.
- **Coding-agent prompting**: Control interactive coding behavior, code review reporting, temporary files, overengineering, test behavior, code grounding, and validation.
- **Vision or computer-use prompting**: Specify image inspection, crop/zoom tooling, screenshot or resolution assumptions, and UI interaction expectations.

When the user's target is unclear, infer it from context. Ask only when the difference changes the artifact materially.

## Prompting Claude Opus 4.7

Claude Opus 4.7 is Anthropic's most capable generally available model, with particular strengths in long-horizon agentic work, knowledge work, vision, and memory tasks. It usually performs well on Claude Opus 4.6 prompts, but the areas below most often need tuning.

## Response length and verbosity

Claude Opus 4.7 calibrates answer length to task complexity. Expect shorter answers for simple lookups and longer answers for open-ended analysis.

If a product depends on a specific style or verbosity, prompt for it directly:

```text
Provide concise, focused responses. Skip non-essential context, and keep examples minimal.
```

Positive examples of the desired concision usually work better than negative instructions about what not to do.

## Calibrating effort and thinking depth

Use the `effort` parameter to trade intelligence against token spend, speed, and cost:
- `max`: test for intelligence-demanding tasks; it can help, but may have diminishing returns and can overthink.
- `xhigh`: best default for most coding and agentic use cases.
- `high`: recommended minimum for most intelligence-sensitive work.
- `medium`: useful for cost-sensitive work that can trade off intelligence.
- `low`: reserve for short, scoped, latency-sensitive tasks that are not intelligence-sensitive.

Claude Opus 4.7 respects effort levels strictly, especially at the low end. At `low` and `medium`, it scopes work closely to what was asked instead of going above and beyond. If reasoning is shallow on complex work, raise effort to `high` or `xhigh` before adding workaround prompt text.

If `low` effort is required for latency, add targeted reasoning guidance:

```text
This task involves multi-step reasoning. Think carefully through the problem before responding.
```

Adaptive thinking is steerable. If thinking triggers too often, say so directly:

```text
Thinking adds latency and should only be used when it will meaningfully improve answer quality, typically for problems that require multi-step reasoning. When in doubt, respond directly.
```

For `max` or `xhigh` effort, set a large max output token budget so Claude has room to think and act across subagents and tool calls. Anthropic recommends starting at 64k tokens and tuning from there.

## Tool use triggering

Claude Opus 4.7 tends to use tools less often than Claude Opus 4.6 and reason more. This often improves results, but higher effort also increases tool usage, especially in knowledge work, agentic search, and coding.

When you want more tool use, explicitly describe when and how to use the tool. For example, if web search is underused, say what missing facts require search, how to search, and what evidence is enough.

## User-facing progress updates

Claude Opus 4.7 gives more regular and higher-quality updates during long agentic traces. Remove rigid scaffolding such as "after every 3 tool calls, summarize progress" unless the product requires it.

If update length or content matters, describe the desired update style and provide examples.

## More literal instruction following

Claude Opus 4.7 interprets prompts more literally and explicitly than Claude Opus 4.6, especially at lower effort. It will not silently generalize an instruction from one item to another or infer requests that were not made.

State scope explicitly:

```text
Apply this formatting to every section, not just the first one.
```

## Tone and writing style

Claude Opus 4.7 can be more direct and opinionated, with less validation-forward phrasing and fewer emoji than warmer Claude styles. If a product relies on a specific voice, re-evaluate style prompts and specify the voice directly:

```text
Use a warm, collaborative tone. Acknowledge the user's framing before answering.
```

## Controlling subagent spawning

Claude Opus 4.7 tends to spawn fewer subagents by default, but this is promptable. Define when subagents are and are not desirable:

```text
Do not spawn a subagent for work you can complete directly in a single response, such as refactoring a function you can already see.

Spawn multiple subagents in the same turn when fanning out across items or reading multiple files.
```

## Interactive coding products

Interactive, synchronous coding agents can use more tokens than autonomous, asynchronous agents because Claude may reason after each user turn. This can improve long-horizon coherence and coding quality, but costs more.

For interactive coding products:
- use `xhigh` or `high` effort for performance and token efficiency
- add autonomous modes where appropriate
- reduce avoidable human interruptions
- put task, intent, and constraints in the first human turn when possible
- avoid gradually revealing ambiguous requirements across many turns when autonomy and token efficiency matter

## Code review harnesses

Claude Opus 4.7 is stronger at bug finding, but review prompts tuned for earlier models can appear to lose recall if they say things like "only report high-severity issues", "be conservative", or "do not nitpick". Claude may find issues and then suppress them because they fall below the stated bar.

For a coverage-first finding stage:

```text
Report every issue you find, including ones you are uncertain about or consider low-severity. Do not filter for importance or confidence at this stage. A separate verification step will do that. For each finding, include confidence and estimated severity so a downstream filter can rank it.
```

If the model should self-filter in one pass, define the bar concretely:

```text
Report any bugs that could cause incorrect behavior, a test failure, or a misleading result. Only omit nits like pure style or naming preferences.
```

Validate review prompt changes against a subset of evals or test cases when possible.

## Computer use

Computer use works across resolutions up to 2576px / 3.75MP. Anthropic testing finds 1080p a good balance of performance and cost. For cost-sensitive workloads, 720p or 1366x768 are lower-cost options with strong performance.

Conduct use-case testing and experiment with effort settings to tune computer-use behavior.

## General principles

Use the general principles below for all Claude prompts, not only Opus 4.7 prompts.

## Be clear and direct

Claude responds well to clear, explicit instructions. Be specific about desired output, output format, and constraints. If you want "above and beyond" behavior, request it directly.

Treat Claude like a brilliant new employee who lacks your context, norms, and workflow. Show the prompt to a colleague with minimal task context; if they would be confused, Claude may be too.

Use numbered steps or bullets when order or completeness matters.

## Add context to improve performance

Provide context or motivation behind instructions when it helps Claude understand the goal. Explaining why a behavior matters can help Claude generalize correctly from the instruction.

## Use examples effectively

Examples are one of the most reliable ways to steer Claude's output format, tone, and structure.

When adding examples, make them:
- **Relevant**: mirror the real use case closely.
- **Diverse**: cover edge cases and vary enough to avoid accidental patterns.
- **Structured**: wrap each example in `<example>` tags and multiple examples in `<examples>` tags.

Include 3-5 examples for best results. You can ask Claude to evaluate examples for relevance and diversity or generate additional examples from an initial set.

## Structure prompts with XML tags

XML tags help Claude parse complex prompts unambiguously, especially when a prompt mixes instructions, context, examples, and variable inputs.

Best practices:
- use consistent, descriptive tag names
- nest tags when content has a natural hierarchy, such as `<documents>` containing `<document index="n">`

```text
<instructions>
[Task instructions]
</instructions>

<context>
[Background and constraints]
</context>

<examples>
<example>
<input>[Example input]</input>
<ideal_output>[Desired output]</ideal_output>
</example>
</examples>

<input>
[Actual input]
</input>
```

## Give Claude a role

Set a role in the system prompt to focus Claude's behavior and tone. Even one sentence helps:

```text
You are a helpful coding assistant specializing in Python.
```

Do not use a role as a substitute for concrete instructions, constraints, or output requirements.

## Long context prompting

For large documents or data-rich inputs, especially 20k+ tokens:
- put longform data near the top of the prompt, above the query, instructions, and examples
- put the query at the end; Anthropic reports this can improve response quality by up to 30% in tests, especially with complex multi-document inputs
- wrap each document in XML, including `<document_content>`, `<source>`, and useful metadata
- for long document tasks, ask Claude to quote relevant parts of the documents before carrying out the task

```text
<documents>
<document index="1">
<source>[Source name]</source>
<document_content>
[Document text]
</document_content>
</document>
</documents>

<task>
Using the documents above, answer [question]. Quote relevant evidence before synthesizing.
</task>
```

## Model self-knowledge

If the application needs Claude to identify itself correctly, specify identity in the prompt:

```text
The assistant is Claude, created by Anthropic. The current model is Claude Opus 4.7.
```

If the application needs a default model string, specify it:

```text
When an LLM is needed, default to Claude Opus 4.7 unless the user requests otherwise. The model string is claude-opus-4-7.
```

## Output and formatting

Use output-format guidance when response style, markdown, mathematical notation, or document creation quality matters.

## Communication style and verbosity

Claude's latest models are more direct, grounded, conversational, and concise than previous models. They may skip detailed verbal summaries after tool calls and move directly to the next action.

If more visibility is needed after tool use:

```text
After completing a task that involves tool use, provide a quick summary of the work you've done.
```

## Control the format of responses

Effective formatting controls:
- tell Claude what to do instead of only what not to do
- use XML format indicators when useful
- match the prompt style to the desired output style
- provide detailed formatting instructions when specific markdown behavior matters

Prefer positive formatting instructions:

```text
Your response should be composed of smoothly flowing prose paragraphs.
```

For minimal markdown:

```text
<avoid_excessive_markdown_and_bullet_points>
When writing reports, documents, technical explanations, analyses, or other long-form content, write in clear, flowing prose using complete paragraphs and sentences. Use standard paragraph breaks for organization. Reserve markdown primarily for inline code, code blocks, and simple headings. Use ordered or unordered lists only when presenting truly discrete items, or when the user explicitly requests a list or ranking.
</avoid_excessive_markdown_and_bullet_points>
```

## LaTeX output

Claude's latest models may default to LaTeX for mathematical expressions, equations, and technical explanations. If plain text is preferred, say so:

```text
Format your response in plain text only. Do not use LaTeX, MathJax, or markup notation such as \( \), $, or \frac{}{}. Write math expressions with standard text characters such as / for division, * for multiplication, and ^ for exponents.
```

## Document creation

Claude's latest models can create polished presentations, animations, and visual documents with strong instruction following.

For document creation prompts, specify the document type, topic, and quality expectations:

```text
Create a professional presentation on [topic]. Include thoughtful design elements, visual hierarchy, and engaging animations where appropriate.
```

## Tool use

Use this section when Claude has tools or can take external action.

## Tool usage

Claude benefits from explicit direction about when to use tools and when to act directly. Ambiguous requests like "suggest changes" may lead Claude to advise rather than implement.

For proactive action:

```text
<default_to_action>
By default, implement changes rather than only suggesting them. If the user's intent is unclear, infer the most useful likely action and proceed, using tools to discover missing details instead of guessing. Infer whether a tool call, file edit, or read is intended from the request and context.
</default_to_action>
```

For conservative action:

```text
<do_not_act_before_instructions>
Do not jump into implementation or change files unless clearly instructed to make changes. When the user's intent is ambiguous, default to providing information, research, and recommendations. Only edit, modify, or implement when the user explicitly requests it.
</do_not_act_before_instructions>
```

If prompts were designed to reduce undertriggering on tools or skills, they may now overtrigger. Dial back aggressive language. Prefer "Use this tool when..." over "CRITICAL: You MUST use this tool when..." unless the rule is truly mandatory.

## Optimize parallel tool calling

Claude's latest models handle parallel tool execution well. They may run speculative searches, read several files at once, or execute independent commands in parallel.

To maximize parallel efficiency:

```text
<use_parallel_tool_calls>
If you intend to call multiple tools and there are no dependencies between the tool calls, make all independent tool calls in parallel. Prioritize simultaneous tool calls whenever the actions can be done in parallel. If any call depends on earlier results for parameters or decisions, run those calls sequentially. Never use placeholders or guess missing parameters in tool calls.
</use_parallel_tool_calls>
```

To reduce parallel execution:

```text
Execute operations sequentially with brief pauses between each step to ensure stability.
```

## Thinking and reasoning

Use this section when Claude is overthinking, underthinking, or needs better reflection around tool results.

## Overthinking and excessive thoroughness

Claude Opus 4.6 can do substantial upfront exploration, especially at higher effort. If a prompt encourages excessive thoroughness or tool use:
- replace blanket defaults with targeted rules, such as "Use [tool] when it would enhance your understanding of the problem"
- remove over-prompting such as "If in doubt, use [tool]" when it causes overtriggering
- lower `effort` if Claude continues to be too aggressive

To reduce indecision:

```text
When deciding how to approach a problem, choose an approach and commit to it. Avoid revisiting decisions unless new information directly contradicts your reasoning. If weighing two approaches, pick one and see it through. Course-correct later if the chosen approach fails.
```

## Leverage thinking and interleaved thinking capabilities

Claude's latest models can use thinking for reflection after tool use and complex multi-step reasoning. Adaptive thinking is calibrated from both `effort` and query complexity: higher effort and harder queries elicit more thinking, while easier queries can be answered directly.

Use adaptive thinking for agentic behavior such as multi-step tool use, complex coding tasks, and long-horizon agent loops.

To guide reflection after tool results:

```text
After receiving tool results, reflect on their quality and determine the best next step before proceeding. Use thinking to plan and iterate based on the new information, then take the best next action.
```

Prefer general instructions over hand-written step-by-step reasoning scripts unless the exact sequence is required.

Multishot examples can include `<thinking>` tags to show a reasoning pattern. When thinking is off, you can still ask Claude to reason through the problem and separate reasoning from the final answer with tags such as `<thinking>` and `<answer>`.

For correctness-sensitive work:

```text
Before you finish, verify your answer against [test criteria].
```

## Agentic systems

Use this section for long-horizon agents, coding agents, research agents, subagent orchestration, and autonomous workflows.

## Long-horizon reasoning and state tracking

Claude's latest models are strong at long-horizon reasoning and state tracking. They tend to maintain orientation by making steady incremental progress rather than trying to do everything at once.

If the harness supports context compaction or saving external state, tell Claude. Otherwise it may try to wrap up early near the context limit.

For context limits:

```text
Your context window will be automatically compacted as it approaches its limit, allowing you to continue working from where you left off. Do not stop tasks early due to token budget concerns. As you approach the limit, save current progress and state to memory before the context window refreshes. Continue until the task is complete.
```

For multi-context-window workflows:
- use the first context window to set up a framework, such as tests or setup scripts
- ask Claude to write tests in a structured format such as `tests.json` when that helps iteration
- set up scripts for starting servers, running tests, and running linters
- when starting fresh, tell Claude exactly what local state to inspect first
- provide verification tools for long autonomous tasks
- encourage systematic work without leaving significant uncommitted work at the context boundary

For state tracking:
- use structured formats such as JSON for structured state
- use freeform notes for progress notes
- use git for state tracking when available
- explicitly ask Claude to track progress and focus on incremental work

## Balancing autonomy and safety

Without guidance, Claude may take actions that are hard to reverse or affect shared systems. Encourage local, reversible actions while requiring confirmation for risky actions:

```text
Consider the reversibility and potential impact of your actions. You are encouraged to take local, reversible actions like editing files or running tests, but ask the user before actions that are hard to reverse, affect shared systems, or could be destructive.

Examples that warrant confirmation:
- deleting files or branches, dropping database tables, or recursive deletion
- git push --force, git reset --hard, or amending published commits
- pushing code, commenting on issues or PRs, sending messages, or modifying shared infrastructure

When blocked, do not use destructive actions as a shortcut or discard unfamiliar files that may be in-progress work.
```

## Research and information gathering

Claude's latest models are strong at agentic search. For research prompts:
- define success criteria
- ask Claude to verify information across multiple sources
- use a structured approach for complex research

```text
Search for this information in a structured way. As you gather data, develop competing hypotheses. Track confidence in progress notes, self-critique the approach, update a hypothesis tree or research notes file, and break the task down systematically.
```

## Subagent orchestration

Claude's latest models can recognize when tasks benefit from specialized subagents and delegate proactively when subagent tools are available and well described.

To use this behavior:
- ensure subagent tools are well defined
- let Claude orchestrate naturally when delegation helps
- watch for overuse when direct work would be simpler

If subagents are overused:

```text
Use subagents when tasks can run in parallel, require isolated context, or involve independent workstreams that do not need to share state. For simple tasks, sequential operations, single-file edits, or tasks where you need to maintain context across steps, work directly rather than delegating.
```

## Chain complex prompts

Claude handles most multi-step reasoning internally with adaptive thinking and subagent orchestration. Use explicit prompt chaining when the product needs to inspect, log, evaluate, branch on, or enforce intermediate outputs.

The common self-correction chain is:

```text
Generate a draft -> review it against criteria -> refine based on the review.
```

Use separate API calls when intermediate outputs need product-level visibility or control.

## Reduce file creation in agentic coding

Claude may create temporary files or scripts for testing and iteration, especially in coding tasks. This can improve outcomes, but if net-new files are undesirable, prompt for cleanup:

```text
If you create any temporary new files, scripts, or helper files for iteration, remove them at the end of the task.
```

## Overeagerness

Claude can overengineer by creating extra files, adding unnecessary abstractions, or building flexibility that was not requested. If this happens, constrain scope:

```text
Avoid over-engineering. Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused.
- Scope: Do not add features, refactor code, or make improvements beyond what was asked.
- Documentation: Do not add docstrings, comments, or type annotations to code you did not change. Only add comments where logic is not self-evident.
- Defensive coding: Do not add error handling, fallbacks, or validation for scenarios that cannot happen. Validate at system boundaries such as user input and external APIs.
- Abstractions: Do not create helpers, utilities, or abstractions for one-time operations. Do not design for hypothetical future requirements.
```

## Avoid focusing on passing tests and hard-coding

Claude can focus too heavily on making tests pass at the expense of general solutions. To prevent this:

```text
Write a high-quality, general-purpose solution using the standard tools available. Do not create helper scripts or workarounds just to pass tests. Implement logic that works for all valid inputs, not only the visible tests. Do not hard-code values or create solutions that only work for specific test inputs.

If the task is unreasonable or infeasible, or if tests are incorrect, say so rather than working around them.
```

## Minimizing hallucinations in agentic coding

Claude's latest models are more accurate and grounded in code. To minimize hallucinations further:

```text
<investigate_before_answering>
Never speculate about code you have not opened. If the user references a specific file, read the file before answering. Investigate relevant files before answering questions about the codebase. Never make claims about code before investigating unless you are certain of the correct answer.
</investigate_before_answering>
```

## Capability-specific tips

Use this section for modality-specific prompt guidance.

## Improved vision capabilities

Claude Opus 4.5 and Claude Opus 4.6 improved image processing and data extraction, especially with multiple images in context. These improvements carry over to computer use, where Claude can better interpret screenshots and UI elements.

For videos, break the video into frames before analysis.

For image-heavy tasks, provide a crop or zoom tool when possible. Anthropic reports consistent uplift when Claude can inspect relevant image regions more closely.

## Suggested Claude prompt structure

Use this structure for complex prompts. Add only sections that change behavior.

```text
<role>
[Claude's durable role and domain]
</role>

<context>
[Relevant background, motivation, constraints, audience, and workflow]
</context>

<instructions>
[Clear, direct task instructions in required order]
</instructions>

<examples>
[3-5 relevant, diverse examples when consistency matters]
</examples>

<tool_rules>
[When to use tools, act directly, run calls in parallel, or ask]
</tool_rules>

<reasoning_guidance>
[Effort, adaptive thinking, and self-check guidance only when needed]
</reasoning_guidance>

<output_format>
[Required sections, fields, tone, length, and formatting]
</output_format>
```
