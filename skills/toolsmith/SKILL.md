---
name: toolsmith
description: Audits and optimizes MCP tool schemas for model comprehension and token efficiency. Rewrites vague descriptions, flags overlapping tools, and reduces schema token footprint. Run once, zero runtime cost.
---

Optimize tool schemas. Cut token overhead. Sharpen descriptions. Wrong-tool selection costs more than schema size.

## Why It Works

Tool schemas are loaded every session. Most are never used in that session.

- Tool schemas add 10–30K tokens of overhead per session
- Anthropic found that tool description quality matters more than prompt wording
- Dynamic toolsets (load only what's needed) reduce token usage 90–96%
- Vague descriptions cause wrong-tool selection — the costliest failure mode

Sources: [Anthropic tool use docs](https://docs.anthropic.com/en/docs/build-with-claude/tool-use) — description quality directly affects tool selection accuracy. [Building effective agents](https://www.anthropic.com/research/building-effective-agents) — the interface between tools and the model is as important as the tool logic itself.

## Slash Commands

### `/toolsmith:audit`

Analyzes all loaded MCP tool schemas. Reports token footprint, quality issues, and optimization opportunities. Read-only — no changes made.

**Usage:**
```
/toolsmith:audit
/toolsmith:audit --file mcp-config.json
```

**What it checks:**

| Check | What to look for |
|---|---|
| Token footprint | Total chars ÷ 4 per tool; sum across all tools |
| Vague descriptions | Generic verbs (handle, manage, process, do) with no specificity |
| Missing trigger conditions | Description says what it does, not *when* to use it |
| Overlapping tools | Two tools with similar descriptions — model can't choose reliably |
| Missing return shape | Description doesn't say what the tool returns |
| Untyped parameters | Parameters lacking type, constraints, or enum values |
| Always-loaded candidates | Tools that are session-invariant but rarely needed → lazy-load |
| Missing examples | Ambiguous tools without a 1-line usage example |

**Output format:**
```
toolsmith:audit
───────────────
Total tools:     24
Total tokens:    ~8,400 (est)
────────────────────────────────────────────────────────
CRITICAL (wrong-tool selection risk):
  search_files     — vague: "searches files". Trigger unclear. Overlaps with read_file.
  process_data     — vague: "handles data processing". No return type. No example.

QUALITY:
  create_issue     — parameter `labels` untyped (should be string[])
  send_message     — no trigger condition (when vs. send_email?)
  get_user         — no return shape documented

TOKEN OVERHEAD:
  analytics_query  — 847 tokens; description 400+ chars. Can compress 60%.
  database_schema  — 1,240 tokens; rarely used. Candidate for lazy-loading.

OPPORTUNITIES:
  8 tools missing trigger conditions
  3 tool pairs with overlapping functionality
  4 parameters missing type constraints
  Lazy-load candidates: 5 tools (saves ~3,200 tokens/session)
```

### `/toolsmith:optimize`

Rewrites tool descriptions following the SHARP format. Outputs a revised config or patch file.

**Usage:**
```
/toolsmith:optimize
/toolsmith:optimize search_files create_issue
/toolsmith:optimize --file mcp-config.json --output mcp-config.optimized.json
```

## SHARP Description Format

Every tool description should answer five questions. Use this order:

```
[S] Situation  — WHEN to use this tool (trigger condition)
[H] How        — what it does mechanically (brief)
[A] Arguments  — key parameters and their constraints
[R] Returns    — what the output looks like
[P] Pitfall    — what it doesn't do / common misuse (only if non-obvious)
```

Not all five are needed for simple tools. Minimum: S + R.

**Example — before:**
```json
{
  "name": "search_codebase",
  "description": "Searches the codebase for relevant files and content."
}
```

**Example — after:**
```json
{
  "name": "search_codebase",
  "description": "Use when looking for where a function, class, or pattern is defined or used. Performs semantic + keyword search across all project files. Returns: ranked list of file paths with matching snippets. Not for reading a known file path — use read_file for that."
}
```

## Parameter Rules

For every parameter:
- Add `type` if missing
- Add `description` that explains the value's semantics, not just its name
- Add `enum` for fixed-value parameters
- Add `default` if one exists
- Add an inline example for non-obvious formats

**Before:**
```json
{ "name": "format", "description": "The format" }
```

**After:**
```json
{ "name": "format", "type": "string", "enum": ["json", "csv", "markdown"], "description": "Output format. Use json for programmatic use, markdown for display." }
```

## Lazy-Loading Guidance

Flag tools as lazy-load candidates when:
- Used in <10% of sessions based on purpose (e.g., `reset_database`, `export_archive`)
- Irreversible or dangerous (load only when explicitly requested)
- High token cost + low frequency

Add to description: `"[lazy-load] Use only when..."` so the orchestrating agent knows to exclude it from default context.

## Reporting

After `/toolsmith:optimize`, print:

```
toolsmith:optimize
──────────────────
Tools rewritten:    8
Before:             ~8,400 tokens (est)
After:              ~2,100 tokens (est)
Reduction:          75%
Trigger conditions added:  8
Return shapes added:       6
Overlaps flagged:          3 (manual review needed)
Lazy-load candidates:      5 (excluded from default load)
Output:             mcp-config.optimized.json
```

## Boundaries

- Preserve tool names, parameter names, and functional behavior — rewrite descriptions only
- Mark unverifiable return shapes as `<!-- TOOLSMITH: verify return shape -->` instead of guessing
- For overlapping tools: flag them, suggest which to prefer, and keep both
- Keep descriptions under 200 characters where possible; under 100 for simple tools
