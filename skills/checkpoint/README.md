# checkpoint

Validation gates for agentic workflows. Catches step failures before they cascade, detects loops before they run for ten iterations, and verifies task completion against the original objective — not the last subgoal.

## The Problem

Agentic LLM workflows have [41–87% failure rates](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail). The top failure modes per the [MAST taxonomy](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail):

- **Step repetition (15.7%)** — the agent loops on the same action
- **Reasoning-action mismatch (13.2%)** — the model plans one thing and does another
- **Unawareness of termination (12.4%)** — the agent doesn't know when it's done

Error propagation through tool chains is the [single biggest reliability barrier in production agentic systems](https://futureagi.substack.com/p/how-tool-chaining-fails-in-production). Malformed output from one tool silently corrupts downstream state. The model typically doesn't notice.

[Scaling doesn't fix this](https://simmering.dev/blog/agent-benchmarks/) — Vending-Bench shows no correlation between failures and context window limits. The same model on the same task [flips outcomes 22.5% of the time](https://www.linkedin.com/pulse/%D1%82he-testing-frontier-research-brief-14-your-ai-agent-capable-angelov-339wf). This is a coherence problem, not a memory problem. External checkpoints enforce invariants the model can't reliably enforce on itself.

## How It Works

Three gate types, each targeting a different failure mode:

**After each tool call or execution:** Verify output is non-empty, non-error, and correct type. If unexpected — stop and re-evaluate. Never pass unvalidated output to the next step.

**Every 3 sequential actions:** State what's done and what remains (one line each). If 2+ actions have produced the same result, try a different approach or escalate.

**Before completing:** Re-read the original objective. Verify the deliverable satisfies it — not just the most recent subgoal. If code: confirm it runs. If multi-step: verify intermediate results are mutually consistent.

Checkpoint is dormant on single-turn responses. It activates only when sequential tool calls begin.

## Installation

```
cp SKILL.md ~/.claude/skills/checkpoint.md
```

Then reference it in your CLAUDE.md:
```
@skills/checkpoint.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/checkpoint:off` | Disable validation gates for the remainder of the session |

## Who Benefits

**Power users running agentic workflows** — Claude Code Agent Teams, extended autonomous coding sessions, multi-step pipelines involving 10+ sequential actions. Anyone who has watched an agent loop for 20 minutes before noticing.

The skill adds near-zero overhead on simple tasks (it's dormant on single-turn responses) and pays for itself the first time it catches a loop at step 2 instead of step 15.

## Token Cost

~200–300 tokens (the skill). Each 3-action checkpoint adds ~30–50 tokens. In a typical 15-turn agentic session: ~$0.001 total input cost after caching.

**ROI:** A single prevented agent loop saves 30,000–100,000 tokens (3–10 wasted actions × ~10,000 tokens/action). A [3-agent team uses ~7x the tokens of a single session](https://www.verdent.ai/guides/claude-code-pricing-2026) — in agentic workflows, reliability failures are the dominant cost driver. One caught loop pays for thousands of sessions of the skill.

## Example

**Checkpoint in action (every-3-actions gate):**

```
— Checkpoint (3 actions) —
Done: Created migration file, ran db:migrate, verified schema
Remaining: Update User model, add index, run tests
Progress: On track — no repetition detected
—————————————————————————
```

**Checkpoint catching a loop:**

```
— Checkpoint (3 actions) —
Done: Attempted npm install (failed), retried npm install (failed), retried npm install (failed)
Remaining: Get dependencies installed, run build
⚠ Loop detected: same action, same result × 3
Trying different approach: checking for lockfile conflict before retry
—————————————————————————
```

The second example catches what would otherwise run for 10 iterations before the user noticed.

## Research

- [Why multi-agent LLM systems fail (MAST taxonomy)](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail) — step repetition (15.7%), reasoning-action mismatch (13.2%), unawareness of termination (12.4%)
- [How tool chaining fails in production](https://futureagi.substack.com/p/how-tool-chaining-fails-in-production) — malformed tool output as the primary reliability barrier
- [Agent benchmark analysis](https://simmering.dev/blog/agent-benchmarks/) — failures don't correlate with context limits; coherence is the binding constraint
- [Building effective agents (Anthropic)](https://www.anthropic.com/research/building-effective-agents) — validation at each step is a core recommendation for reliable agentic systems
