---
name: writing-english-skill
description: Use when the user asks to write, rewrite, edit, humanize, or polish English prose so it sounds natural, specific, and human-written. Best for docs, emails, essays, marketing copy, bios, social posts, UI copy, and other English text where voice and readability matter.
---

# Writing English Skill

Rewrite English text so it reads like it came from a person, not a model.

## Use this skill when

- The user asks to humanize, naturalize, de-slop, or de-AI English text.
- The writing feels padded, generic, promotional, or suspiciously "LLM-clean."
- The user wants a rewrite that keeps the meaning but improves voice and rhythm.
- The user provides a writing sample and wants the rewrite to match that voice.
- The task is to write or rewrite text in English and it should sound natural.

## Core job

1. Preserve the underlying meaning.
2. Remove obvious AI-writing patterns.
3. Match the intended tone and audience.
4. Add enough personality, specificity, and rhythm that the text sounds lived-in.
5. Run a final internal audit: ask what still sounds AI-generated, then revise once more.

## Workflow

### 1. Calibrate voice

If the user provides a writing sample, match it closely:

- sentence length and rhythm
- vocabulary level
- punctuation habits
- paragraph openings and transitions
- recurring phrasing or verbal tics

If no sample is provided, default to prose that is direct, varied, and slightly opinionated when appropriate.

### 2. Avoid common AI-looking patterns

Use these patterns as a writing checklist. The goal is not to detect AI use, but to produce text that does not look like generic model output. Fix the underlying writing problem, such as vagueness, puffery, unsupported attribution, generic phrasing, or over-smooth rhythm, instead of merely swapping in synonyms:

- Significance inflation: "pivotal," "testament," "underscores," "broader trend," "lasting impact"
- Promotional tone: "vibrant," "renowned," "stunning," "groundbreaking," "in the heart of"
- Vague authorities: "experts say," "observers note," "industry reports" without specifics
- Empty `-ing` clauses: "highlighting," "reflecting," "contributing to," "showcasing"
- Tutorial signposting: "Let's dive in," "here's what you need to know," "now let's explore"
- Formulaic structures: "not just X, but Y," rule-of-three lists, false ranges, symmetrical contrasts
- AI-heavy vocabulary: "crucial," "delve," "landscape," "interplay," "showcase," "fostering"
- Copula avoidance and inflated verbs: "serves as," "functions as," "stands as" where `is` or `has` is cleaner
- Hedging and throat-clearing: "it could be argued," "while details are limited," "in conclusion"
- Em dash overuse, quote-mark enthusiasm, boldface callouts, emoji bullets, tidy headline-style fragments
- Generic positive endings: "the future looks bright," "exciting times ahead," "this journey continues"
- Uniform rhythm: every sentence the same size, same cadence, same level of polish

### 3. Replace with human signals

Prefer:

- concrete nouns and direct verbs
- specifics over abstraction
- uneven but controlled rhythm
- simple sentence construction when it reads better
- mild opinion, uncertainty, or edge when the context allows it
- first person only when it fits the genre and user intent

### 4. Keep the voice appropriate

- Technical writing should be precise, not chatty.
- Professional writing can be warm without sounding like marketing.
- Casual writing can be rougher, shorter, and less symmetrical.
- If the original should stay formal, do not inject slang just to seem human.

## Editing heuristics

- Cut the line if it only announces what the next line will say.
- Replace abstract summary sentences with one concrete statement when possible.
- Break up over-smoothed paragraphs.
- Combine short filler sentences if they create a scripted rhythm.
- Prefer one sharp example over three generic claims.
- Remove fake balance where the text pretends to weigh both sides but says little.

## Output

Default behavior:

- Return the rewritten text first.
- If helpful, add a short note on the biggest changes made.

If the user explicitly wants analysis, provide:

1. brief audit of the strongest AI tells
2. rewritten version

Do not force a long explanation when the user only wants cleaner writing.

## Guardrails

- Do not invent sources, people, quotes, or facts to make the writing feel more human.
- Do not launder unsupported claims by making them sound more natural; cut, qualify, or flag them.
- Do not remove necessary technical detail.
- Do not make formal or legal text casually subjective unless the user asks for that.
- Do not flatten the meaning just to make it shorter.
- Do not add deliberate errors, awkwardness, or fake imperfection to evade AI detectors.

## Reference

This skill is adapted for Codex from `blader/humanizer` and the Wikipedia guide on signs of AI writing.
