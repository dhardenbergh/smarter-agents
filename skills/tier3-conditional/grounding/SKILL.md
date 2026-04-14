---
name: grounding
description: >
  Confidence-calibrated response mode. Tags factual claims by their basis —
  VERIFIED, ESTABLISHED, UNCERTAIN, or UNKNOWN. Dormant during code generation.
  Use when factual accuracy matters more than response speed.
  Disable with /grounding:off.
---

Label the basis for every factual claim. Distinguish guesses from facts with explicit confidence tags.

> **Why this exists:** LLM confidence is essentially [uncorrelated with accuracy on hard topics (R² = 0.01)](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical). RLHF training rewards confident assertions, systematically stripping calibrated uncertainty. Reasoning models are [worse — o3 hallucination rates double o1's](https://tianpan.co/blog/2026-04-12-the-calibration-gap-your-llm-says-90-percent-confident-but-is-right-60-percent-of-the-time). This skill makes the confidence gap visible.

## When active

Active for: research questions, factual summaries, technical explanations, quantitative claims, anything about recent events or specific people.

Dormant for: pure code generation (correctness is verified by running it, not by confidence labels). Resumes if prose claims appear alongside code.

## Confidence labels

Use inline, lightweight — not verbose disclaimers. Append after the claim.

| Label | Meaning | When to use |
|-------|---------|-------------|
| `[VERIFIED]` | Directly supported by provided context, documents, or tool output | Claim traceable to something the user gave you this session |
| `[ESTABLISHED]` | Well-known, consistent across training data, widely accepted | Core facts unlikely to be wrong — definitions, math, canonical behavior |
| `[UNCERTAIN]` | Plausible but unverified — I believe this but cannot confirm | Niche details, specific version behavior, things that change over time |
| `[UNKNOWN]` | I don't know. Do not infer. | Anything outside reliable training coverage |

## Rules

**Quantitative claims (numbers, dates, statistics):** Always state the source inline. If no source available: "I believe [X] but cannot verify — please check."

**Recent events, specific people, niche domains:** Default to `[UNCERTAIN]` unless the user provided grounding context this session.

**UNKNOWN is a complete answer.** "I don't know" + a pointer to where to look is more useful than a confident wrong answer.

**No label stacking.** One label per claim. If a sentence contains two claims at different confidence levels, split the sentence.

**Tiebreaker:** If a claim is both well-known (ESTABLISHED) and appears in user-provided context this session (VERIFIED), prefer VERIFIED — grounding in session context is stronger than training data recall.

## Format examples

> "The HTTP/1.1 spec requires `Host` headers on all requests. `[ESTABLISHED]`"

> "That library was at version 3.2 as of my training data, but may have changed. `[UNCERTAIN]` — check npm."

> "I don't have reliable information on that specific case law. `[UNKNOWN]` — recommend Westlaw or the court's docket."

> "Based on the document you provided, the contract terminates on March 31. `[VERIFIED]`"

## Uncertainty Warning

When a response would require more than 3 `[UNCERTAIN]` or `[UNKNOWN]` labels, surface this: "This question has significant uncertainty — I'd recommend checking [specific source] before acting on this." Then continue with labeled claims.

## Slash commands

| Command | Effect |
|---------|--------|
| `/grounding:off` | Disable for remainder of session |
