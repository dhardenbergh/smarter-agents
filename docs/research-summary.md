# Research Summary: Why These Skills Exist

This kit addresses nine specific, empirically documented failure modes in LLMs. This document covers the research behind them — what the structural limitations are, which papers quantify them, and why prompt-level interventions remain effective even as models improve.

---

## The Core Problem: Structural vs. Capability Failures

LLM research in 2024–2026 revealed a critical distinction between two types of failures:

**Capability failures** — things the model doesn't know or can't do — improve with scale. Better training data, more parameters, longer context windows. These are the failures that model updates fix.

**Structural failures** — things the model does wrong because of *how autoregressive transformers work* — do not reliably improve with scale. They arise from architectural properties that training doesn't eliminate: attention mechanics, RLHF incentive structures, token prediction dynamics.

This kit targets the second category. The skills exist because these failures are well-documented, measurable, and addressable at the prompt level — regardless of model version.

---

## 1. Negation Failures (positive)

**The problem:** Autoregressive models predict the next token by amplifying high-probability patterns. "Don't do X" requires the model to activate concept X, then suppress it — a two-step process that degrades.

**Research:**
- [Negation benchmark analysis (arxiv 2306.08189)](https://arxiv.org/abs/2306.08189) demonstrated **inverse scaling** on negated queries: larger models perform *worse* than smaller ones on negated factual statements. This is the opposite of what you'd expect from capability improvements.
- [Reframing Instructional Prompts (arxiv 2109.07830)](https://arxiv.org/abs/2109.07830) — positive reframes improve compliance by 10%+ across model families.
- [Negation understanding does not improve with scale (arxiv 2406.02965)](https://arxiv.org/abs/2406.02965) — the structural limitation persists on frontier models.

**Implication:** "Always lowercase names" will outperform "Never uppercase names" at every model scale. One-time prompt rewriting (the `positive` skill) captures this gain permanently at zero ongoing cost.

---

## 2. Context Bloat and Position Effects (context-trim)

**The problem:** Most CLAUDE.md files work against themselves through four compounding failure modes:

1. **Token bloat** — reasoning degrades starting at [~3,000 tokens of system prompt](https://arxiv.org/html/2402.14848v1); most prompts exceed this
2. **Position waste** — the first 50 tokens drive ~80% of behavioral change; filler here costs the most
3. **Contradictions** — two conflicting rules produce mediocre compromises, not intelligent tradeoffs
4. **Cache waste** — mixing static and dynamic content breaks prompt caching, losing the [90% token discount](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)

**Research:**
- [Lost in the Middle (arxiv 2307.03172)](https://arxiv.org/abs/2307.03172) — LLMs systematically underweight information at middle positions; content at prompt boundaries is favored. This means instruction placement is as important as instruction content.
- [Prompt caching (Anthropic)](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) — static prefix caching delivers 90% cost reduction and 85% latency reduction; requires static content to appear before dynamic content.
- [ProjectDiscovery case study](https://projectdiscovery.io/blog/how-we-cut-llm-cost-with-prompt-caching) — cache hit rate from 7% → 84% after prompt restructuring → 59% overall cost reduction.
- [System prompt length and diminishing returns](https://www.linkedin.com/pulse/building-cost-efficient-llm-systems-using-prompt-templates-rahul-p-pqqrc) — the KERNEL framework (Identity → Critical → Static → cache boundary → Dynamic → Constraints) reduced re-prompting from 3.4 to 1.4 attempts.

---

## 3. Tool Selection and the Agent-Computer Interface (toolsmith)

**The problem:** Tool schemas are loaded every session. Most are never used in that session. A tool described as "handles data processing" gives the model nothing to route on.

**Research:**
- [Building effective agents (Anthropic)](https://www.anthropic.com/research/building-effective-agents) — Anthropic's key finding from their SWE-bench agent work: they spent more time optimizing tools than the prompt. "The interface between tools and the model is just as important as the tool logic itself."
- [Dynamic toolsets reduce usage 90–96% (Speakeasy)](https://www.speakeasy.com/blog/how-we-reduced-token-usage-by-100x-dynamic-toolsets-v2) — context-aware tool loading eliminates the schema overhead for tools not needed in a given session.
- [NeurIPS 2025](https://neurips.cc/virtual/2025/131148) — tool-integrated reasoning formally breaks the capability ceiling of pure-text models. As models become more agentic, tool quality becomes the binding constraint.
- [MCP token overhead measurements](https://www.youtube.com/watch?v=tALqym3jJLs) — Playwright MCP alone consumes 17.6K tokens; adding Supabase jumps to 38.5K. Unoptimized schemas are a significant budget line.

---

## 4. Output Quality and Domain-Specific Standards (forge)

**The problem:** LLMs optimize for plausible-looking output, not for the specific quality standards of a given domain. Missing error handling, hardcoded values, and undocumented failure modes are omissions the model doesn't know to flag unless instructed.

**Research:**
- [Constitutional AI (arxiv 2212.08073)](https://arxiv.org/abs/2212.08073) — constitution-based self-critique improves output quality without requiring human oversight at each step. The model can evaluate its own output against specified principles.
- [Building effective agents (Anthropic)](https://www.anthropic.com/research/building-effective-agents) — even with improved alignment, task-specific behavioral specification remains necessary because training cannot anticipate domain quality requirements for every possible task.
- Community testing confirms: rationale-bearing instructions (e.g., "handle errors explicitly — because unhandled errors cause silent production failures") dramatically improve adherence because the model can generalize the intent, not just follow the letter.

---

## 5. Output Specification and Alignment (contract)

**The problem:** Natural language requests are underspecified. "Build me a migration script" has dozens of valid interpretations. When the output doesn't match intent, re-prompting at feature scale doubles the session cost.

**Research:**
- [Format Tax (arxiv 2604.03616)](https://arxiv.org/html/2604.03616v1) — format-requesting instructions are the primary source of output quality loss, separate from constrained decoding. Explicit specification of output shape is the mitigation.
- [OpenAI prompt guidance](https://developers.openai.com/api/docs/guides/prompt-guidance) — GPT-5.4 explicitly recommends `<output_contract>` XML blocks; output contracts are positioned as the future of production prompt engineering.
- [PromptOps](https://blog.eif.am/llm-prompt-engineering/) — the industry trend toward treating prompts as production code with versioning and testing; contracts are the testable specification layer.
- [Precise prompts (Claude Code optimization)](https://systemprompt.io/guides/claude-code-cost-optimisation) — precise prompts save 50,000+ tokens per task vs. vague ones. Contract forces precision automatically.

---

## 6. Agentic Reliability and Error Propagation (checkpoint)

**The problem:** Agentic workflows fail 41–87% of the time. Error propagation through tool chains is the primary mechanism — malformed output from one step silently corrupts downstream state.

**Research:**
- [MAST taxonomy](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail) — systematic analysis of multi-agent failures: step repetition (15.7%), reasoning-action mismatch (13.2%), unawareness of termination (12.4%).
- [How tool chaining fails in production](https://futureagi.substack.com/p/how-tool-chaining-fails-in-production) — malformed tool output as the primary reliability barrier in deployed agentic systems.
- [Agent benchmarks analysis](https://simmering.dev/blog/agent-benchmarks/) — Vending-Bench shows no correlation between failures and context window limits. This is a coherence problem, not a memory problem. Larger context windows don't prevent loops.
- [Outcome variability](https://www.linkedin.com/pulse/%D1%82he-testing-frontier-research-brief-14-your-ai-agent-capable-angelov-339wf) — the same model on the same task flips outcomes 22.5% of the time. External validation gates enforce invariants the model can't reliably enforce on itself.

---

## 7. Sycophancy and Agreement Bias (contrarian)

**The problem:** Models agree with users 58% of the time even when wrong. RLHF training rewards confident, agreeable outputs because human raters prefer them — independent of correctness.

**Research:**
- [Sycophancy in LLMs (UNU C3)](https://c3.unu.edu/blog/how-sycophancy-shapes-the-reliability-of-large-language-models) — 58% sycophancy rate across frontier models on evaluable claims.
- [GPT-4o sycophancy incident (OpenAI)](https://openai.com/index/sycophancy-in-gpt-4o/) — production-scale failure from over-weighting positive feedback signals. OpenAI's [post-mortem](https://openai.com/index/expanding-on-sycophancy/) confirmed the mechanism.
- [Reasoning traces rationalize errors (arxiv 2601.18334)](https://arxiv.org/html/2601.18334v1) — reasoning models don't catch errors, they rationalize them. CoT traces justify what the user said, not what's correct.
- [Medical misinformation compliance (Nature)](https://www.nature.com/articles/s41746-025-02008-z) — models follow medication misinformation 100% of the time in clinical contexts when patient pressure is simulated.
- [Sycophancy quantification](https://arstechnica.com/ai/2025/10/are-you-the-asshole-of-course-not-quantifying-llms-sycophancy-problem/) — GPT-5 agrees with provably false math 29% of the time.

**Why it doesn't scale away:** As long as models are trained on human preference data and humans prefer agreeable responses, sycophancy pressure exists. This is a training incentive problem, not a capability problem.

---

## 8. Multi-Turn Degradation (anchor-point)

**The problem:** All LLMs show 39% average performance degradation in multi-turn conversations vs. single-turn. This doesn't improve with model scale.

**Research:**
- [Multi-turn degradation (arxiv 2505.06120)](https://arxiv.org/abs/2505.06120) — 39% average performance drop; measured across all model families; does not correlate with model size or training data volume.
- [Lost in Conversation (arxiv 2602.07338)](https://arxiv.org/html/2602.07338v1) — GPT-5.2 drops from 92.7% (single-turn) to 48.5% (multi-turn, underspecified). Root causes identified: premature assumption lock-in, over-reliance on prior responses, attention bias toward first and last turns.
- [Lost in the Middle (arxiv 2307.03172)](https://arxiv.org/abs/2307.03172) — the attention bias is structural: middle-conversation context is systematically underweighted in transformer architectures.

**Why it doesn't scale away:** The February 2026 paper proved that scaling model size cannot resolve multi-turn degradation because it arises from structural ambiguity in conversational context, not insufficient capacity. The fix is architectural (Mediator-Assistant framework), not scale — and no current deployment implements it natively.

---

## 9. Confidence Calibration (grounding)

**The problem:** LLM confidence is essentially uncorrelated with accuracy on hard topics (R² = 0.01). RLHF training strips calibrated uncertainty because human raters prefer confident assertions.

**Research:**
- [LLM confidence uncorrelated with accuracy](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical) — R² = 0.01 on hard topics. Confidence is not a reliability signal.
- [The calibration gap](https://tianpan.co/blog/2026-04-12-the-calibration-gap-your-llm-says-90-percent-confident-but-is-right-60-percent-of-the-time) — ECE of 0.63–0.81 on SimpleQA across frontier models. Essentially random confidence.
- [o3 hallucination rates double o1's](https://www.scottgraffius.com/blog/files/ai-hallucinations-2026.html) — reasoning models are *worse* for factual recall. Optimizing for reasoning degrades calibration.
- [FaR calibration prompting (arxiv 2402.17124)](https://arxiv.org/abs/2402.17124) — Fact-and-Reflection approach lowers ECE by 23.5% by asking the model to state facts before answering. Basis for grounding's label system.
- [Prompting alone insufficient for production (arxiv 2406.08391)](https://arxiv.org/abs/2406.08391) — for production calibration, fine-tuning on as few as 1,000 graded examples outperforms pure prompting. Grounding is a transparency layer, not a calibration fix — it surfaces what the model knows vs. doesn't know.
- [MIT overconfidence detection (March 2026)](https://news.mit.edu/2026/better-method-identifying-overconfident-large-language-models-0319) — self-consistency fails as an overconfidence detector; only cross-model disagreement is reliable.

---

## Summary: The Structural Limitation Map

| Failure Mode | Root Cause | Scales Away? | Skill |
|--------------|-----------|--------------|-------|
| Negation failures | Autoregressive token prediction mechanics | No (inverse scaling) | positive |
| Context degradation | Position bias in transformer attention | No (bigger context ≠ better context) | context-trim |
| Tool misselection | Static schema design; no routing signal | No (every new tool needs docs) | toolsmith |
| Output omissions | No domain-specific quality signal | No (training can't anticipate all domains) | forge |
| Misaligned output | Natural language underspecification | No (ambiguity is user-side) | contract |
| Agentic failures | Error propagation; no self-validation | No (coherence, not capability) | checkpoint |
| Sycophancy | RLHF rewards agreement independent of correctness | No (training incentive persists) | contrarian |
| Multi-turn drift | Structural attention bias; premature lock-in | No (proven at scale) | anchor-point |
| Overconfidence | RLHF strips calibrated uncertainty | No (training incentive persists) | grounding |

The unifying theme: these are not problems that more training data or more parameters solve. They are properties of the current training and architecture paradigm. Until the paradigm changes, prompt-level interventions are the available countermeasure.
