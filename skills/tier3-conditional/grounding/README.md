# grounding

Confidence-calibrated response mode. Tags every factual claim by its basis — `[VERIFIED]`, `[ESTABLISHED]`, `[UNCERTAIN]`, or `[UNKNOWN]`. Makes the gap between confidence and accuracy visible instead of hiding it.

Active for research, factual summaries, technical explanations, and quantitative claims. Dormant during pure code generation. Disable with `/grounding:off`.

## The Problem

LLM confidence is [essentially uncorrelated with accuracy on hard topics (R² = 0.01)](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical). The model that sounds most certain is not the most likely to be correct.

Why: RLHF training [systematically strips calibrated uncertainty](https://tianpan.co/blog/2026-04-12-the-calibration-gap-your-llm-says-90-percent-confident-but-is-right-60-percent-of-the-time) because human raters prefer confident assertions over hedged ones. The training signal rewards confidence independent of correctness. Reasoning models make this worse — [o3's hallucination rates double o1's](https://www.scottgraffius.com/blog/files/ai-hallucinations-2026.html) because optimizing for reasoning degrades factual recall. Calibration on SimpleQA is [ECE of 0.63–0.81 across frontier models](https://tianpan.co/blog/2026-04-12-the-calibration-gap-your-llm-says-90-percent-confident-but-is-right-60-percent-of-the-time) — close to random.

[MIT researchers (March 2026)](https://news.mit.edu/2026/better-method-identifying-overconfident-large-language-models-0319) found that self-consistency fails as an overconfidence detector — only cross-model disagreement measurement is reliable. A prompt-level skill can't fully fix calibration, but it can force the model to explicitly categorize its basis, surfacing uncertainty that would otherwise be suppressed.

## How It Works

Grounding adds inline confidence labels to factual claims — lightweight, not verbose disclaimers:

| Label | Meaning | When to use |
|-------|---------|-------------|
| `[VERIFIED]` | Directly supported by context, documents, or tool output provided this session | Claim traceable to something the user gave you |
| `[ESTABLISHED]` | Well-known, consistent across training data, widely accepted | Definitions, core facts, canonical behavior unlikely to be wrong |
| `[UNCERTAIN]` | Plausible but unverified — believe this but cannot confirm | Niche details, specific version behavior, things that change over time |
| `[UNKNOWN]` | I don't know. Do not infer. | Anything outside reliable training coverage |

**Rules:**
- Quantitative claims (numbers, dates, statistics): always state the source inline. If none available: "I believe [X] but cannot verify — please check."
- Recent events, specific people, niche domains: default to `[UNCERTAIN]` unless grounded in context provided this session.
- `[UNKNOWN]` is a complete answer. "I don't know" + a pointer to where to look is more useful than a confident wrong answer.
- No label stacking. One label per claim. If a sentence has two claims at different confidence levels, split the sentence.

Dormant on pure code generation — correctness is verified by running the code, not by confidence labels.

## Installation

```
cp SKILL.md ~/.claude/skills/grounding.md
```

Then reference it in your CLAUDE.md:
```
@skills/grounding.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/grounding:off` | Disable confidence labeling for the remainder of the session |

## Who Benefits

**Researchers and technical writers** using LLMs for research synthesis, documentation, or analysis — anyone who has been burned by a confident-sounding hallucination. **Anyone** working in a domain where acting on a wrong fact has real consequences: compliance, medicine, security, legal.

**When to skip it:** Pure code generation sessions. If you're writing software and running it to verify correctness, confidence labels add noise. Grounding is high-value when "trust but don't verify" is the failure mode, not when you have an automated test suite.

## Token Cost

~150–200 tokens (the skill). Each response adds ~20–40 tokens for the confidence labels. Cached after the first turn — amortized input cost across a session is near-zero.

**ROI:** One caught hallucination that would have been acted upon. Downstream work built on a fabricated fact costs far more than every session of the skill combined.

## Example

**Without grounding:**

> "The EU AI Act requires high-risk AI systems to maintain logs for at least 5 years, and this applies to all systems deployed after January 2024."

**With grounding:**

> "The EU AI Act requires high-risk AI systems to maintain logs for at least 5 years. `[ESTABLISHED]` — This is in Article 12 of the final text. The effective date for high-risk systems depends on system category; a blanket 'January 2024' date for all systems is an oversimplification. `[UNCERTAIN]` — Check the Commission's implementation timeline for your specific system category."

The first response reads as authoritative. The second is accurate about what's established and honest about what isn't. For compliance decisions, only the second is safe to act on.

## Research

- [LLM confidence uncorrelated with accuracy](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical) — R² = 0.01 on hard topics; confidence is not a reliability signal
- [The calibration gap](https://tianpan.co/blog/2026-04-12-the-calibration-gap-your-llm-says-90-percent-confident-but-is-right-60-percent-of-the-time) — ECE 0.63–0.81 on SimpleQA; RLHF strips calibrated uncertainty
- [o3 hallucination rates](https://www.scottgraffius.com/blog/files/ai-hallucinations-2026.html) — reasoning models double o1's hallucination rate on factual recall
- [MIT overconfidence detection research](https://news.mit.edu/2026/better-method-identifying-overconfident-large-language-models-0319) — self-consistency fails; only cross-model disagreement is reliable
- [FaR calibration prompting (arxiv 2402.17124)](https://arxiv.org/abs/2402.17124) — fact-and-reflection approach lowers ECE by 23.5%; basis for grounding's label system
