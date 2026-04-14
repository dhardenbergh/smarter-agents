# positive

Rewrites negative constraints in system prompts into positive equivalents. Run once. Zero runtime cost.

## The Problem

LLMs don't handle negation cleanly. Autoregressive models predict the next token by amplifying high-probability patterns — "don't do X" requires the model to first activate concept X, then suppress it. That two-step process degrades. Worse: [larger models show inverse scaling on negated queries](https://arxiv.org/abs/2306.08189) — GPT-3-class models perform *worse* than smaller ones on negated factual statements. [Positive reframes improve compliance by 10%+](https://arxiv.org/abs/2109.07830) across model families.

"Never uppercase names" loses. "Always lowercase names" wins. At every model scale.

## How It Works

One-time rewrite of your CLAUDE.md, `.cursorrules`, or any system prompt file. No runtime cost — you pay the token cost once, at editing time. The skill scans for negation patterns (`don't`, `never`, `avoid`, `no`, `refrain from`) and converts each into a concrete positive directive, inferring the intended behavior from context.

Original file is backed up as `<filename>.original.md`.

## Installation

```
cp SKILL.md ~/.claude/skills/positive.md
```

Then reference it in your CLAUDE.md:
```
@skills/positive.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/positive:rewrite` | Auto-detect CLAUDE.md or .cursorrules and rewrite in place |
| `/positive:rewrite CLAUDE.md` | Rewrite a specific file |
| `/positive:rewrite docs/system-prompt.md` | Rewrite any markdown prompt file |

## Who Benefits

**Developers maintaining CLAUDE.md files** — anyone who has accumulated a list of "don't do this" rules in their AI config and noticed inconsistent compliance. Also useful for teams inheriting someone else's system prompt.

## Token Cost

~1,165 tokens (4,660 chars ÷ 4). One-time load, zero per-session cost after the rewrite is done.

## Example

**Before:**
```
- Don't use default exports
- Never commit API keys to the repo
- Avoid deeply nested callbacks; use async/await
- Do not add console.log statements unless debugging
```

**After:**
```
- Use named exports for all modules
- Store API keys in .env files; add .env to .gitignore
- Use async/await for all asynchronous operations
- Remove console.log before committing; use the logger in debug mode
```

Same intent. Clearer signal. The model doesn't have to suppress anything — it just follows.

## Research

- [Negation benchmark analysis (arxiv 2306.08189)](https://arxiv.org/abs/2306.08189) — inverse scaling on negated queries; larger models perform worse
- [Reframing Instructional Prompts (arxiv 2109.07830)](https://arxiv.org/abs/2109.07830) — positive reframes improve compliance by 10%+
