---
name: trim
description: Audits and compresses system prompts for token efficiency and model comprehension. Restructures into KERNEL order, strips redundancy, resolves contradictions. Target: 30–50% reduction. Run once, zero runtime cost.
---

Compress system prompts. Cut tokens. Preserve behavior. Fix contradictions. Front-load what matters.

## Why It Works

Four mechanisms cause system prompt degradation:

1. **Token bloat** — reasoning degrades starting at ~3,000 tokens; most CLAUDE.md files exceed this
2. **Position matters** — the first tokens set the behavioral frame; filler here wastes prime real estate
3. **Contradictions** — two conflicting rules produce mediocre compromises, not intelligent tradeoffs
4. **Cache waste** — static and dynamic content mixed together breaks prompt caching (90% token cost discount lost)

Sources: [Anthropic prompt caching docs](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) — static prefix must be identical across calls to hit cache. [Lost in the Middle](https://arxiv.org/abs/2307.03172) — LLMs favor information at prompt boundaries over middle content.

## Slash Commands

### `/trim:audit`

Analyzes the target file without modifying it. Reports:

- **Token estimate** — character count ÷ 4 (1 token ≈ 4 chars)
- **Contradictions** — rules that directly conflict (flagged with line numbers)
- **Position analysis** — what's in the first 50 tokens vs. what should be
- **Cache boundary** — where static content ends and dynamic content begins
- **Redundancy** — duplicate rules, synonymous instructions, restated constraints

**Usage:**
```
/trim:audit
/trim:audit CLAUDE.md
/trim:audit docs/system-prompt.md
```

**Output format:**
```
trim:audit — CLAUDE.md
──────────────────────
Tokens (est):    4,820  ← target: <2,000
First 50 tokens: "You are a helpful assistant that..."  ← WASTED (generic)
Cache boundary:  None detected (static/dynamic mixed)
Contradictions:  2 found
  ↳ L12 "be concise" conflicts with L47 "always provide full examples"
  ↳ L31 "never use bullet points" conflicts with L89 "use lists for steps"
Redundancy:      6 rules duplicated or restated
Top savings:
  - Remove 3 redundant role-declaration sentences  (~120 tokens)
  - Deduplicate style rules                         (~200 tokens)
  - Move dynamic context to end                     (enables caching)
```

### `/trim:optimize`

Rewrites the target file using the KERNEL structure. Backs up original first.

**Usage:**
```
/trim:optimize
/trim:optimize CLAUDE.md
```

## KERNEL Structure

Rewrite in this order:

```
[KERNEL]
1. Identity     — 1 sentence: what this agent is and its primary directive
2. Critical     — must-follow behaviors (ranked by consequence of violation)
3. Static       — style, tone, format rules that never change  ← CACHE BOUNDARY
─────────────────────────────────────────────────────────────────────────────
4. Dynamic      — project-specific context, current task, variable instructions
5. Guardrails  — behavioral boundaries (recency bias benefit; placed last)
```

The cache boundary sits between static and dynamic content. Everything above it is identical across sessions and eligible for the 90% token discount.

## Optimization Rules

Apply all of the following:

**1. Front-load critical behavior**
The opening tokens set the behavioral frame. Lead with the agent's identity and single most important directive. Cut greetings, disclaimers, and preamble.

**2. Strip filler**
Remove: `please`, `make sure to`, `it's important that`, `you should`, `always remember`, `as an AI`, `I want you to`, `feel free to`, `certainly`, `of course`.

**3. Drop articles from instructions** (Caveman-style for directives)
`"Always include a summary at the end"` → `"Include summary at end"`
`"Use the correct format for all responses"` → `"Use correct format"`

**4. Deduplicate**
Find rules stated more than once. Keep the clearest version. Delete the rest.

**5. Resolve contradictions**
Flag each contradiction. Ask which rule takes precedence, or apply the more specific rule (specific beats general). Document the resolution with a comment.

**6. Group static content**
All session-invariant rules go above the cache boundary: tone, output format, language, coding style.

**7. Isolate dynamic content**
Project name, current sprint, active task, user preferences — move to after cache boundary.

**8. Place constraints last**
Constraint rules belong at the end. Recency bias means they're more likely to be followed when closest to the generation point. (After `/positive:rewrite`, these become positive directives — still place at end.)

**9. Compress redundant rationale**
`"Use TypeScript types because types improve readability, catch bugs early, and make refactoring safer"` → `"Use TypeScript types (readability + safety)"`

## Backup and Reporting

Before any write: save original as `<filename>.original.md`.

After rewriting, print:

```
trim:optimize — CLAUDE.md
─────────────────────────
Backup:           CLAUDE.md.original.md
Before:           4,820 tokens (est)
After:            1,640 tokens (est)
Reduction:        66%
Contradictions:   2 resolved (see inline comments)
Cache boundary:   Set at line 34 (saves ~3,180 tokens/session at 90% discount)
```

## Boundaries

- Preserve all code blocks, file paths, URLs, and technical identifiers exactly
- Leave uncertain content unchanged and add a note for manual review
- Keep sections from different concerns separate; restructure within each, preserve boundaries
- If resolving a contradiction requires a judgment call, flag it with `<!-- TRIM: ambiguous — kept L12 over L47, verify -->`
