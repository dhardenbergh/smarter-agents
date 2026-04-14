# Cost-Benefit Analysis: LLM Skills Kit

The skeptic's objection is legitimate: every skill file you add gets loaded into the context window on every invocation. That's real cost. Before you type a word, [Claude Code's system already consumes 16,063 tokens in an empty directory and ~23,000 in a real project](https://www.reddit.com/r/ClaudeCode/comments/1s3xjn4/i_measured_claude_codes_hidden_token_overhead/) — core system prompt (~2,800), built-in tools (~10,000), and CLAUDE.md plus project files accounting for the rest. Every skill compounds this.

The question is whether each skill saves more than it costs.

---

## Token Economics

### Base Cost Structure (Sonnet 4.6)

| Component | Cost |
|-----------|------|
| Input tokens | [$3 per million](https://www.ssdnodes.com/blog/claude-code-pricing-in-2026-every-plan-explained-pro-max-api-teams/) |
| Output tokens | [$15 per million](https://www.ssdnodes.com/blog/claude-code-pricing-in-2026-every-plan-explained-pro-max-api-teams/) (5× input) |
| Cached input tokens | [$0.30 per million](https://www.ssdnodes.com/blog/claude-code-pricing-in-2026-every-plan-explained-pro-max-api-teams/) (10% of standard) |
| Input >200K tokens | [$6 per million](https://www.ssdnodes.com/blog/claude-code-pricing-in-2026-every-plan-explained-pro-max-api-turns-explained/) (2× standard) |

**The critical asymmetry:** Output tokens cost 5× input tokens. Any skill that reduces output is disproportionately valuable. Skills that reduce re-prompting — which burns large output token sequences — pay for themselves many times over.

### The Caching Multiplier

Skill files are the textbook case for prompt caching: static text, loaded at the beginning of every session, identical across turns. After the first turn, the skill file costs 10% of its listed input price.

[ProjectDiscovery measured this directly](https://projectdiscovery.io/blog/how-we-cut-llm-cost-with-prompt-caching): restructuring their prompts for cache alignment took their LLM costs from baseline to −59% overall (−70% in optimized windows). Their system prompts are 20K+ tokens per agent, re-sent on every step of 40-step tasks. In multi-turn sessions — how developers actually use Claude Code — the amortized input cost of a skill approaches near-zero.

### Typical Session Economics

From [actual Claude Code usage patterns](https://systemprompt.io/guides/claude-code-cost-optimisation):

| Session Type | Input Tokens | Output Tokens | Cost (Sonnet 4.6) |
|-------------|-------------|--------------|-------------------|
| Quick fix (single file) | 10K–30K | 2K–5K | $0.06–$0.17 |
| Feature implementation | 100K–200K | 30K–50K | ~$1.05 |
| Codebase exploration | 150K–300K | 5K–10K | ~$0.79 |
| Full day (20+ turns) | 500K–2M | 100K–300K | $6–$12 |

---

## Per-Skill Breakdown

### Tier 1: Build-Time Tools — Cost Nothing at Runtime

These run once to optimize files you already have. Zero ongoing cost.

#### positive

| Metric | Value |
|--------|-------|
| Skill footprint | ~0 tokens (one-time rewrite, not runtime) |
| Input cost per session | $0.00 |
| Output impact | Better instruction adherence → fewer re-prompts |
| Re-prompting savings | ~10–20% fewer failed attempts on constraint-following |
| Break-even | Immediate |

Negation failures are a [structural limitation of autoregressive models](https://arxiv.org/abs/2406.02965) that doesn't scale away. This is free. There is no argument against doing it.

#### context-trim

| Metric | Value |
|--------|-------|
| Skill footprint | ~0 tokens (one-time audit + rewrite, not runtime) |
| Input cost per session | Net negative — removes tokens from every future session |
| Re-prompting savings | [Reduced re-prompting from 3.4 to 1.4 attempts](https://www.linkedin.com/pulse/building-cost-efficient-llm-systems-using-prompt-templates-rahul-p-pqqrc) in comparable study |
| Break-even | First session — starts saving immediately |

A 2,000-token CLAUDE.md compressed by 40% saves 800 tokens per turn. Over 15 turns with caching, that's ~11,200 input tokens saved. [Adding Playwright MCP consumes 17.6K tokens; Supabase MCP jumps to 38.5K](https://www.youtube.com/watch?v=tALqym3jJLs) — this is where Trim pays off most.

#### toolsmith

| Metric | Value |
|--------|-------|
| Skill footprint | ~0 tokens (one-time audit, not runtime) |
| Input cost per session | Net negative — reduces tool schema tokens |
| Re-prompting savings | [Dynamic toolsets reduce token usage 90–96%](https://www.speakeasy.com/blog/how-we-reduced-token-usage-by-100x-dynamic-toolsets-v2) |
| Break-even | Immediate for MCP-heavy setups |

---

### Tier 2: Always-On Runtime — High ROI for Developers

Combined footprint: ~500–700 tokens (~$0.002/session cached across 15 turns).

#### forge

| Metric | Value |
|--------|-------|
| Skill footprint | ~200–400 tokens |
| Input cost per session (15 turns, cached) | ~$0.001 |
| Output impact | Slight increase (~50–100 tokens for verification) |
| Re-prompting savings | 20–30% fewer code review rejections; 1–3 fewer fix cycles per feature |
| Break-even | 2nd turn of first session |

A rejected code review cycle costs 30K–50K output tokens. At $15/M, that's $0.45–$0.75 saved per prevented cycle. Skill cost: ~$0.001. **ROI: 450–750×** per prevented failure.

#### contract

| Metric | Value |
|--------|-------|
| Skill footprint | ~150–250 tokens |
| Input cost per session (15 turns, cached) | ~$0.0008 |
| Output impact | +50–80 tokens (contract statement) offset by eliminating misaligned output |
| Re-prompting savings | Prevents doubling the cost of any feature-scale misinterpretation |
| Break-even | First misaligned output avoided |

[Precise prompts save 50,000+ tokens per task](https://systemprompt.io/guides/claude-code-cost-optimisation) compared to vague ones. Contract forces that precision automatically on every non-trivial task.

#### checkpoint

| Metric | Value |
|--------|-------|
| Skill footprint | ~200–300 tokens |
| Input cost per session (15 turns, cached) | ~$0.001 |
| Output impact | +30–50 tokens per 3-action checkpoint |
| Re-prompting savings | Targets [41–87% agentic failure rate](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail); one caught loop saves 50K–200K tokens |
| Break-even | First prevented failure cascade |

An agent loop [burns 30,000–100,000 tokens](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail) before you notice. A [3-agent team uses ~7× the tokens of a single session](https://www.verdent.ai/guides/claude-code-pricing-2026) — in agentic workflows, reliability failures are the dominant cost.

---

### Tier 3: Conditional — Strong Value for Specific Workflows

#### contrarian

| Metric | Value |
|--------|-------|
| Skill footprint | ~150–200 tokens |
| Input cost per session (15 turns, cached) | ~$0.0007 |
| Output impact | Slight increase (model explains disagreements) |
| Re-prompting savings | Prevents building on incorrect assumptions → avoids entire project rework |
| Break-even | First corrected wrong assumption |

ROI here isn't measured in tokens. [GPT-5 agrees with provably false math 29% of the time](https://arstechnica.com/ai/2025/10/are-you-the-asshole-of-course-not-quantifying-llms-sycophancy-problem/). In medical contexts, models follow misinformation [100% of the time](https://www.nature.com/articles/s41746-025-02008-z). This skill is cheap insurance against the model validating your worst ideas.

#### anchor-point

| Metric | Value |
|--------|-------|
| Skill footprint | ~150–200 tokens |
| Input cost per session (15 turns, cached) | ~$0.0007 |
| Output impact | +40–60 tokens every 5 turns (state summary) |
| Re-prompting savings | Targets [39% multi-turn degradation](https://arxiv.org/abs/2505.06120); keeps the model aligned with actual intent |
| Break-even | 6th turn (after first state checkpoint) |

If your 15-turn session has the model operating at ~50% accuracy for the last 10 turns (consistent with [multi-turn research baselines](https://arxiv.org/html/2602.07338v1)), you're paying full price for half-quality output.

#### grounding

| Metric | Value |
|--------|-------|
| Skill footprint | ~150–200 tokens |
| Input cost per session (15 turns, cached) | ~$0.0007 |
| Output impact | +20–40 tokens per response (confidence labels) |
| Re-prompting savings | Prevents downstream work built on hallucinated claims |
| Break-even | First caught hallucination |

LLM confidence is [essentially uncorrelated with accuracy on hard topics (R² = 0.01)](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical). Forcing the model to categorize its basis surfaces uncertainty that RLHF training would otherwise suppress.

---

## The Combined Stack

### Build-Time Foundation (Everyone)

Run once. Zero runtime cost. Net savings on every subsequent session.

| Skill | Action | Runtime Cost |
|-------|--------|-------------|
| **positive** | Rewrites negative instructions → positive | $0.00 |
| **context-trim** | Restructures and compresses system prompt | $0.00 (net savings) |
| **toolsmith** | Optimizes MCP tool schemas | $0.00 (net savings) |

### Always-On Layer (Developers)

Combined footprint: ~500–700 tokens (~$0.002/session cached).

| Skill | Tokens | Amortized Cost (15-turn cached) | Savings |
|-------|--------|--------------------------------|---------|
| **forge** | ~300 | $0.001 | 1–3 re-prompt cycles saved (~$0.45–$2.25) |
| **contract** | ~200 | $0.0007 | Prevents misaligned output (~$0.50–$3.00) |
| **checkpoint** | ~250 | $0.0009 | Prevents failure cascades (~$1.50–$7.50) |

**Combined ROI: ~$0.003 input cost to prevent an estimated $2.45–$12.75 in wasted output per session. That's an 800–4,000× return.**

For subscription users on Max plans, the savings aren't in dollars — they're in quota preservation. If your 88K token/5-hour window is burning on re-prompts and agent loops, these skills could reclaim 30–50% more productive capacity from the same plan.

### Conditional Layer (Specific Workflows)

| Skill | Add When | Skip When |
|-------|----------|-----------|
| **contrarian** | Solo dev, making decisions with LLM input | You have a team reviewing your work |
| **anchor-point** | Sessions regularly exceed 10 turns | You `/clear` frequently between tasks |
| **grounding** | Research, documentation, analysis | Pure code generation |

---

## Addressing the Objections

### "But it adds tokens to every request"

Yes — ~500–700 tokens for the always-on layer. In a 15-turn session on Sonnet 4.6, cached after the first turn:

- Input cost of skill files: ~$0.003
- Typical session total: $3–$12
- Skill overhead as percentage of session: **0.03–0.1%**

A single unnecessary file read (1,000 lines ≈ 10,000 tokens) costs more than the entire always-on skill stack for the whole session.

### "I can just be a better prompter"

[The Cursor rules study](https://dev.to/aman_kumar_bdd40f1b711c15/cursor-rules-pay-more-upfront-iterate-less-later-od9) measured this directly:

- **Without rules:** 3 attempts × ~25K tokens = ~75K tokens + frustration + time
- **With rules:** 1 attempt × ~100K tokens = ~100K tokens + done correctly

Rules use more tokens per session but fewer total tokens to complete the task. The same logic applies to skills.

### "Won't models just get better and make these obsolete?"

Some will. Multi-turn degradation persists at [39% and doesn't scale away](https://arxiv.org/abs/2505.06120). Sycophancy is a [training incentive problem](https://openai.com/index/sycophancy-in-gpt-4o/), not a capability problem. Agentic reliability sits at [41–87% failure rates](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail) despite massive capability gains. Skills targeting structural architecture limitations persist. Skills targeting training artifacts persist. See [future-proofing.md](./future-proofing.md) for the full durability analysis.

---

## Decision Matrix

| | Everyday (Pro $20) | Working Developer (Max 5x $100) | Power User / Agentic (Max 20x $200) | API / Production |
|---|---|---|---|---|
| **positive** | Yes | Yes | Yes | Yes |
| **context-trim** | Yes | Yes | Yes | Yes |
| **toolsmith** | If using MCP | Yes | Yes | Yes |
| **forge** | Skip | Yes (code domain) | Yes (all domains) | Yes |
| **contract** | Skip | For complex tasks | Yes | Yes |
| **checkpoint** | Skip | Skip | Yes | Yes |
| **contrarian** | Skip | If solo dev | If solo dev | For decision-support apps |
| **anchor-point** | Skip | For long sessions | Yes | For conversational apps |
| **grounding** | Skip | For research tasks | For research tasks | For user-facing factual apps |

**The pattern:** Build-time tools for everyone. Runtime skills scale with plan tier. Agentic skills only for agentic workflows. Most developers need three skills (positive + context-trim + forge) and will see measurable improvement at negligible cost.
