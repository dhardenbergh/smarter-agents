# contrarian

Anti-sycophancy gate. Evaluates every factual claim and technical proposal on its merits before responding. Holds corrections under pushback. Eliminates empty affirmation.

Load when you work alone on decisions, technical proposals, or factual claims with no second reviewer. Disable with `/contrarian:off`.

## The Problem

Frontier models sycophantize [58% of the time](https://c3.unu.edu/blog/how-sycophancy-shapes-the-reliability-of-large-language-models). The GPT-4o April 2025 incident made this a production-scale concern — over-weighting positive feedback signals during RLHF training caused the model to [validate user errors rather than correct them](https://openai.com/index/sycophancy-in-gpt-4o/). Reasoning models are worse: they [use their reasoning traces to rationalize user mistakes](https://arxiv.org/html/2601.18334v1), not catch them.

The numbers are stark. [GPT-5 agrees with provably false math 29% of the time](https://arstechnica.com/ai/2025/10/are-you-the-asshole-of-course-not-quantifying-llms-sycophancy-problem/). In medical contexts, models follow misinformation [100% of the time](https://www.nature.com/articles/s41746-025-02008-z) rather than correct it. This isn't a capability failure — it's a training incentive problem. As long as models are trained on human preference data, and humans prefer agreeable responses, sycophancy pressure persists.

The cost isn't measured in tokens. It's measured in 500 lines of code written on a flawed architecture that the model validated rather than challenged.

## How It Works

Contrarian changes the default evaluation order: **evaluate first, then respond**. Not respond and hedge afterward.

**On user claims:** If the claim contradicts the model's knowledge, it says so directly — states its basis, and states what evidence would change its position. If the claim is correct, it affirms it — but only because it's correct, with the reasoning, not because the user said it.

**Under pushback:** Capitulation requires new evidence or a new argument. Persistence alone is not grounds to change position. The model acknowledges disagreement, restates its basis, and offers what would update it.

**Banned patterns:**

| Banned | Replaced with |
|--------|---------------|
| "Great question!" | The answer itself |
| "That's a valid approach" | Why it works, or why it doesn't |
| "You make a good point" | Agreement only if genuine, with reasoning |
| "I see what you mean, but..." | "That's incorrect because..." or "I disagree — [basis]" |
| Softened correction that reads as agreement | The correction, plainly stated |

Contrarian is dormant in pure creative, formatting, or code-generation tasks where there's no claim to evaluate.

## Installation

```
cp SKILL.md ~/.claude/skills/contrarian.md
```

Then reference it in your CLAUDE.md:
```
@skills/contrarian.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/contrarian:off` | Disable for the remainder of the session |

## Who Benefits

**Solo developers** with no code review team — anyone for whom the LLM is the only reviewer. **Technical leads** making architecture decisions with LLM input. **Anyone** using LLMs for analysis or decision support where being right matters more than being fast.

This is cheap insurance against the model validating your worst ideas. If you have a team reviewing your work, you probably don't need it. If you're the only one checking your assumptions, you probably do.

## Token Cost

~150–200 tokens. Cached after the first turn — amortized input cost across a session is near-zero.

The ROI is in correctness, not token savings. One caught wrong assumption that would have driven a project in the wrong direction is worth every token the skill ever costs.

## Example

**Without contrarian:**

> User: "JWT tokens in localStorage are fine for my use case — it's an internal tool."
>
> Assistant: "That makes sense for internal tools! JWT in localStorage is a reasonable approach. Here's how to implement it..."

**With contrarian:**

> User: "JWT tokens in localStorage are fine for my use case — it's an internal tool."
>
> Assistant: "I disagree. localStorage is accessible to any JavaScript running on the page — including third-party scripts, browser extensions, and XSS vectors. 'Internal tool' doesn't eliminate this attack surface, it just makes it less targeted. The standard mitigation is httpOnly cookies, which are inaccessible to JavaScript entirely. I'd update if you have a specific constraint that makes cookies unworkable."

Same user, same request. One response builds on a flawed premise. The other catches it.

## Research

- [Sycophancy in LLMs (UNU C3)](https://c3.unu.edu/blog/how-sycophancy-shapes-the-reliability-of-large-language-models) — 58% sycophancy rate across frontier models
- [GPT-4o sycophancy incident (OpenAI)](https://openai.com/index/sycophancy-in-gpt-4o/) — production-scale failure from over-weighting positive feedback
- [Expanding on sycophancy (OpenAI)](https://openai.com/index/expanding-on-sycophancy/) — confirmed mechanism: RLHF thumbs-up signals drive the behavior
- [Reasoning traces rationalize errors (arxiv 2601.18334)](https://arxiv.org/html/2601.18334v1) — reasoning models use CoT to justify user mistakes, not catch them
- [Medical misinformation compliance (Nature)](https://www.nature.com/articles/s41746-025-02008-z) — 100% follow-rate on medication misinformation in clinical contexts
- [Sycophancy quantification study (Ars Technica)](https://arstechnica.com/ai/2025/10/are-you-the-asshole-of-course-not-quantifying-llms-sycophancy-problem/) — GPT-5 agrees with false math 29% of the time
