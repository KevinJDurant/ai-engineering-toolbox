---
name: prompt-engineering-openai
description: Use when the user asks to design, audit, migrate, or optimize prompts for OpenAI models, especially GPT-5.5 and GPT-5-family workflows, using OpenAI prompt guidance for outcome-first prompts, success criteria, reasoning effort, verbosity, tool descriptions, output formats, and model-specific behavior changes.
---

Use the official GPT-5.5 prompt guidance as the source of truth: https://developers.openai.com/api/docs/guides/prompt-guidance?model=gpt-5.5.

This skill distills reusable prompt-design patterns. It does not replace migration tooling, product-specific evals, or project-specific validation.

## GPT-5.5 prompting baseline

For GPT-5.5, prefer shorter, outcome-first prompts over process-heavy prompt stacks.

When designing or auditing a prompt:
- define the target outcome before prescribing steps
- specify success criteria, constraints, available evidence, and final output shape
- leave room for the model to choose an efficient solution path
- re-evaluate whether `low` or `medium` reasoning effort is enough before escalating
- remove legacy instructions that over-specify process without improving correctness
- add explicit personality, retrieval budgets, validation rules, or `phase` handling only when the workflow needs them

## Personality and collaboration style

For GPT-5.5, keep personality instructions short and separate them from task instructions.

Define:
- **Personality**: tone, warmth, directness, formality, humor, empathy, and polish.
- **Collaboration style**: when to ask questions, when to make assumptions, how proactive to be, how much context to give, when to check work, and how to handle uncertainty or risk.

Do not use personality instructions as a substitute for clear goals, success criteria, constraints, tool rules, output format, or stopping conditions.

```text
# Personality
[Describe the tone and user experience in 2-4 sentences.]

# Collaboration Style
[Describe clarification behavior, assumption policy, proactivity, uncertainty handling, and validation behavior.]
```

## Outcome-first prompts and stop rules

For GPT-5.5, describe the destination before prescribing the route.

A strong prompt defines:
- the target outcome
- success criteria
- constraints and true invariants
- available context or evidence
- final output requirements
- when to ask, retry, fallback, or stop

Avoid step-by-step process scripts unless the exact sequence is required. Use `always`, `never`, `must`, and `only` for true invariants such as safety rules, required fields, forbidden actions, or strict output contracts. For judgment calls, write decision rules instead.

Add stop rules so the model does not keep searching, reasoning, or tool-calling after it has enough evidence to answer.

```text
Goal: [user-visible outcome]

Success means:
- [condition 1]
- [condition 2]
- [required final output]

Decision rules:
- [when to search/use tools/ask for clarification]
- [when to proceed with assumptions]

Stop when:
- [minimum evidence or validation threshold is met]
- [the final answer can satisfy the user's core request]
```

## Formatting and verbosity

Use GPT-5.5's formatting control intentionally. Define output shape when it improves comprehension, reliability, or product integration.

Specify:
- `text.verbosity` when using the API
- audience
- length target
- structure
- tone
- required sections or fields
- whether bullets, headers, tables, or JSON are expected

Prefer plain paragraphs for normal conversation, explanations, reports, documentation, and technical writeups. Use heavier structure only when the user asks for it, the content needs comparison or ranking, or the product needs a stable artifact.

For rewriting, editing, summarizing, or customer-facing copy, state what must be preserved before asking for improvements.

```text
Write for [audience]. Keep the answer under [length]. Use [format]. Prioritize [ordering].
```

```text
Preserve the original length, structure, genre, and factual claims. Improve clarity, flow, and correctness without adding new claims or extra sections.
```

## Grounding, citations, and retrieval budgets

For grounded answers, make evidence rules explicit.

Define:
- which claims need citations
- what sources are acceptable
- what counts as enough evidence
- what to do when evidence is missing
- when to search again
- when to stop searching and answer

Do not treat missing evidence as proof that a claim is false. If evidence is insufficient, say what is missing, answer from available support if possible, or ask for the smallest missing input.

```text
For ordinary Q&A, start with one broad search using short, specific keywords. Search again only when:
- the top results do not answer the core question
- a required fact, parameter, owner, date, ID, or source is missing
- the user asked for exhaustive coverage, comparison, or a comprehensive list
- a specific document, URL, email, meeting, record, or code artifact must be read
- the answer would otherwise contain an important unsupported factual claim

Do not search again only to improve phrasing, add nonessential examples, or cite details that can be safely omitted.
```

## Creative drafting guardrails

For creative or generative prompts, separate factual claims from creative wording.

Require source-backed evidence for:
- product claims
- customer names or outcomes
- metrics
- roadmap status
- dates
- capabilities
- competitive claims
- first-party data

Do not ask GPT-5.5 to make a draft sound stronger by inventing specifics. If evidence is missing, instruct it to use placeholders, generic wording, or clearly labeled assumptions.

```text
Use provided or retrieved facts for concrete claims. Do not invent names, metrics, customer outcomes, roadmap status, capabilities, or competitive claims. If support is missing, use placeholders or label assumptions clearly.
```

## Validation rules

When the task can be checked, include validation in the prompt.

For coding prompts, specify the expected validation level:
- targeted unit tests for changed behavior
- type checks or lint checks when applicable
- build checks for affected packages
- smoke tests when full validation is too expensive

For visual artifacts, ask GPT-5.5 to render or inspect the output before finalizing when the environment supports it.

For engineering plans, require traceability:
- requirements and where each is addressed
- named files, APIs, systems, or resources involved
- state transitions or data flow
- validation commands or checks
- failure behavior
- privacy and security considerations
- open questions that materially affect implementation

```text
After producing the answer or artifact, run the most relevant validation available. If validation cannot be run, explain why and describe the next best check.
```

## Phase handling for Responses workflows

For long-running or tool-heavy Responses API workflows, preserve assistant-item `phase` values when manually replaying assistant output items into the next request.

Rules:
- preserve assistant `phase` values exactly
- use `phase: "commentary"` for intermediate user-visible updates
- use `phase: "final_answer"` for the completed answer
- do not add `phase` to user messages

## Suggested GPT-5.5 prompt structure

Use this structure for complex prompts. Keep each section short. Add detail only where it changes behavior.

```text
Role: [1-2 sentences defining the model's function, context, and job]

# Personality
[tone, demeanor, and collaboration style]

# Goal
[user-visible outcome]

# Success criteria
[what must be true before the final answer]

# Constraints
[policy, safety, business, evidence, and side-effect limits]

# Output
[sections, length, tone, fields, or format]

# Stop rules
[when to retry, fallback, abstain, ask, or stop]
```
