---
name: positive
description: Rewrites negative constraints in system prompts into positive equivalents. "Never X" → "Always Y". Run once, zero runtime cost. Use when CLAUDE.md, .cursorrules, or any system prompt contains don't/never/avoid/no.
---

Rewrite negative constraints as positive directives. All technical substance stays. Only negation framing dies.

## Why It Works

Autoregressive LLMs predict the next token by amplifying high-probability patterns. Negation ("don't do X") requires the model to first activate the concept X, then suppress it — a two-step process that degrades with scale. Larger models are *more* insensitive to negation, not less.

Research: [Analysis of LLMs on negation benchmarks](https://arxiv.org/abs/2306.08189) — larger LMs show inverse scaling on negated queries; GPT-3 class models perform *worse* than smaller ones on negated factual statements. [Reframing Instructional Prompts](https://arxiv.org/abs/2109.07830) — positive reframes improve compliance by 10%+. Bottom line: "Always lowercase names" outperforms "Don't uppercase names" at every model scale.

## Slash Command

### `/positive:rewrite`

Scans the specified file (or auto-detects CLAUDE.md / .cursorrules in project root) and rewrites all negative constraints into positive equivalents.

**Steps:**
1. Locate target file (arg provided → use it; else check CLAUDE.md, then .cursorrules)
2. Back up original as `<filename>.original.md`
3. Apply transformation rules (see below)
4. Write rewritten version in place
5. Print before/after diff summary

**Usage:**
```
/positive:rewrite
/positive:rewrite CLAUDE.md
/positive:rewrite .cursorrules
/positive:rewrite docs/system-prompt.md
```

## Transformation Rules

Apply in order. For each negative constraint, identify the implied positive behavior and state it directly.

| Pattern | Transform | Example |
|---|---|---|
| `Don't use X` | `Use Y instead` | "Don't use var" → "Use const or let" |
| `Never X` | `Always Y` | "Never return null" → "Always return undefined or throw" |
| `Avoid X` | `Prefer Y` | "Avoid long functions" → "Keep functions under 30 lines" |
| `Do not X unless Y` | `Default to Z; use X only when Y` | "Do not mutate state unless necessary" → "Default to immutable state; mutate only when performance requires it" |
| `No X` | `Use Y` | "No inline styles" → "Use CSS classes" |
| `Don't X or Y` | `Always Z` | "Don't use tabs or spaces inconsistently" → "Always use 2-space indentation" |
| `Refrain from X` | `Instead, do Y` | "Refrain from adding comments" → "Write self-documenting code; add comments only for non-obvious logic" |

**When the positive alternative is ambiguous:** infer it from context. If a rule says "Don't use `console.log`", the project almost certainly wants a logger — write "Use the project logger (`src/logger.ts`) for all output."

## Preservation Rules

Preserve exactly:
- Code blocks (` ``` ` fenced or indented)
- Inline code (backtick-wrapped)
- File paths, URLs, and package names
- Technical terms and proper nouns
- Section headings and document structure
- Numeric values, version numbers, and identifiers

## Examples

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

---

**Before:**
```
Don't write tests that depend on external services.
Never use any as a TypeScript type.
Avoid PRs over 400 lines.
```

**After:**
```
Mock all external services in tests.
Type all values explicitly; use unknown when type is uncertain.
Keep PRs under 400 lines; split larger changes into stacked PRs.
```

## Diff Summary Format

After rewriting, print:

```
positive:rewrite — CLAUDE.md
─────────────────────────────
Backup:    CLAUDE.md.original.md
Rewrites:  12 constraints transformed
Skipped:   2 (already positive)
Unchanged: code blocks, file paths, headings

Changes:
  L14: "Don't use var" → "Use const or let"
  L22: "Never mutate props" → "Treat all props as read-only"
  ...
```

## Boundaries

- Preserve the author's intent — if the positive alternative isn't clear from context, leave a `<!-- TODO: clarify positive form -->` comment and skip the line
- Rewrite only imperative rules; leave prose descriptions and rationale text unchanged
- Replace constraints in place; preserve section structure as-is
