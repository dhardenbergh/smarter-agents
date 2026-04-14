# trim

Audits and compresses system prompts for token efficiency and model comprehension. Target: 30–50% reduction. Run once, zero runtime cost.

## The Problem

Most CLAUDE.md files are quietly working against themselves. Four failure modes compound:

1. **Token bloat** — reasoning degrades starting at ~3,000 tokens; most prompts exceed this
2. **Position waste** — the first 50 tokens drive ~80% of behavioral change; filler here costs most
3. **Contradictions** — two conflicting rules produce mediocre compromises, not intelligent tradeoffs
4. **Cache waste** — static and dynamic content mixed together breaks prompt caching, losing the [90% token discount](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching). [LLMs also favor boundaries over middle content](https://arxiv.org/abs/2307.03172) — anything buried in the middle is partially ignored.

## How It Works

Two commands: `audit` (read-only analysis) then `optimize` (rewrite). The rewrite reorganizes content into the **KERNEL structure** — Identity → Critical → Static → (cache boundary) → Dynamic → Constraints — and applies nine compression rules: front-loading, filler removal, Caveman-style directives, deduplication, contradiction resolution, and static/dynamic separation.

## Installation

```
cp SKILL.md ~/.claude/skills/trim.md
```

Then reference it in your CLAUDE.md:
```
@skills/trim.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/trim:audit` | Analyze target file — token count, contradictions, redundancy, cache boundary (read-only) |
| `/trim:audit CLAUDE.md` | Audit a specific file |
| `/trim:optimize` | Rewrite using KERNEL structure; backs up original first |
| `/trim:optimize CLAUDE.md` | Optimize a specific file |

## Who Benefits

**Anyone maintaining a CLAUDE.md or system prompt that has grown organically** — added rule by rule over months, never restructured. Also useful before sharing a system prompt with a team, or before hitting token limits in a context window.

## Token Cost

~1,494 tokens (5,976 chars ÷ 4). Pays for itself on the first optimize run.

## Example

**Audit output:**
```
trim:audit — CLAUDE.md
──────────────────────
Tokens (est):    4,820  ← target: <2,000
Contradictions:  2 found
  ↳ L12 "be concise" conflicts with L47 "always provide full examples"
Redundancy:      6 rules duplicated or restated
Cache boundary:  None detected (static/dynamic mixed)
```

**After `/trim:optimize`:**
```
Before: 4,820 tokens
After:  1,640 tokens
Reduction: 66%
Cache boundary set at line 34 (saves ~3,180 tokens/session at 90% discount)
```

## Research

- [Anthropic prompt caching docs](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) — static prefix must be identical across calls to hit cache
- [Lost in the Middle (arxiv 2307.03172)](https://arxiv.org/abs/2307.03172) — LLMs favor information at prompt boundaries; middle content is underweighted
