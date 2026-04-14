# anchor-point

Multi-turn state management. Forces periodic state consolidation every 5 turns to prevent the 39% performance degradation that accumulates in long conversations. Surfaces assumption changes explicitly instead of silently updating.

Dormant under 5 turns. Activate manually with `/anchor:now`. Disable with `/anchor:off`.

## The Problem

All LLMs show a [39% average performance drop in multi-turn vs. single-turn conversations](https://arxiv.org/abs/2505.06120). This number doesn't improve with model scale. GPT-5.2 drops from [92.7% accuracy (single-turn) to 48.5% (multi-turn, underspecified)](https://arxiv.org/html/2602.07338v1).

The root causes, per the [February 2026 "Lost in Conversation" paper](https://arxiv.org/html/2602.07338v1):

1. **Premature assumptions lock in** — the model makes an inference early in the conversation and treats it as fact for all subsequent turns
2. **Over-reliance on prior responses** — the model answers turn 8 relative to turn 7, not relative to the original goal
3. **Attention bias toward first and last turns** — middle-conversation context is systematically underweighted

These are structural properties of transformer attention under long contexts, not training failures. [Scaling model size cannot resolve multi-turn degradation](https://arxiv.org/html/2602.07338v1) because the root cause is structural ambiguity in conversational context, not insufficient capacity.

The direct countermeasure: periodic re-anchoring that forces the model to reconstruct state from the original objective, not just the prior turn.

## How It Works

At turn 5 (and every 5 turns after), Anchor Point prepends a brief state block before the response:

```
— Anchor —
Goal: [1-2 sentences on what the user is trying to accomplish]
Assumptions: [bullet list of key things taken as given so far]
Needs revision: [anything from earlier turns that may no longer hold, or "None"]
Proceeding with updated understanding.
————————
```

When the user contradicts or updates an earlier statement, Anchor Point acknowledges the change explicitly: "Noted — that changes the earlier assumption that [X]." Then re-evaluates any conclusions that depended on the old assumption. Silent updates are prohibited — they cause downstream drift.

When the user's goal or scope shifts mid-conversation, an immediate re-anchor triggers regardless of turn count.

## Installation

```
cp SKILL.md ~/.claude/skills/anchor-point.md
```

Then reference it in your CLAUDE.md:
```
@skills/anchor-point.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/anchor:now` | Force an immediate state summary at any turn |
| `/anchor:off` | Disable for the remainder of the session |

## Who Benefits

**Developers who work through complex problems in long conversations** — anyone who iterates on architecture or implementation over 20+ turns. Anyone who has noticed Claude "forgetting" what was discussed three turns ago, or arriving at conclusions that no longer match the original goal.

**When to skip it:** If you regularly use `/clear` between distinct tasks, or if your conversations are typically under 5 turns, Anchor Point has zero overhead (it's dormant under 5 turns) and adds no value. Load it only for sessions you expect to run long.

## Token Cost

~150–200 tokens (the skill). Each 5-turn anchor block adds ~40–60 tokens. Zero cost for conversations under 5 turns.

**ROI:** If your 15-turn session has the model operating at ~50% accuracy for the last 10 turns (consistent with the [research baseline](https://arxiv.org/abs/2505.06120)), you're paying full price for half-quality output. The periodic re-anchoring costs ~200 extra output tokens per session but keeps the model aligned with your actual intent. The payoff is in correctness, not token savings.

## Example

**Without anchor-point (turn 8 — the drift):**

> User (turn 2): "I need an API for a small internal tool."
> User (turn 8): "Add rate limiting."
>
> *Model implements rate limiting assuming the earlier context — small, internal, low traffic.*

**With anchor-point (turn 5 re-anchor reveals scope change):**

```
— Anchor —
Goal: Build REST API for inventory management system
Assumptions: PostgreSQL backend, ~10 internal users, no public exposure
Needs revision: User mentioned "external partners" in turn 4 — this may change
               auth requirements and rate limit expectations from the turn 2 context
Proceeding with updated understanding.
————————
```

The re-anchor surfaces the scope change before the model implements rate limiting sized for internal traffic on a system that now serves external partners.

## Research

- [Multi-turn performance degradation (arxiv 2505.06120)](https://arxiv.org/abs/2505.06120) — 39% average performance drop in multi-turn vs. single-turn; does not scale away
- [Lost in Conversation (arxiv 2602.07338)](https://arxiv.org/html/2602.07338v1) — GPT-5.2 drops from 92.7% to 48.5% in multi-turn underspecified settings; structural cause identified
- [Lost in the Middle (arxiv 2307.03172)](https://arxiv.org/abs/2307.03172) — LLMs favor boundaries over middle content; mid-conversation context is systematically underweighted
