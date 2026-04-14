# toolsmith

Audits and optimizes MCP tool schemas for model comprehension and token efficiency. Run once, zero runtime cost.

## The Problem

Tool schemas are loaded every session. Most are never used in that session. The overhead is real:

- Tool schemas add 10–30K tokens of overhead per session
- Vague descriptions cause wrong-tool selection — the costliest failure mode in agentic systems
- Dynamic toolsets reduce token usage [90–96%](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)
- Per Anthropic: ["the interface between tools and the model is just as important as the tool logic itself"](https://www.anthropic.com/research/building-effective-agents)

A tool described as "handles data processing" gives the model nothing to route on. Selection errors compound across multi-step tasks.

## How It Works

Two commands: `audit` (read-only analysis across all loaded MCP schemas) then `optimize` (rewrite using the **SHARP format**). SHARP structures each description to answer: **S**ituation (when to use it), **H**ow it works mechanically, **A**rguments and constraints, **R**eturns (what the output looks like), **P**itfall (what it doesn't do). Lazy-load candidates are flagged for exclusion from default context.

## Installation

```
cp SKILL.md ~/.claude/skills/toolsmith.md
```

Then reference it in your CLAUDE.md:
```
@skills/toolsmith.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/toolsmith:audit` | Analyze all loaded MCP tool schemas — token footprint, quality issues, overlaps |
| `/toolsmith:audit --file mcp-config.json` | Audit a specific config file |
| `/toolsmith:optimize` | Rewrite all tool descriptions using SHARP format |
| `/toolsmith:optimize search_files create_issue` | Optimize specific tools by name |
| `/toolsmith:optimize --file mcp-config.json --output mcp-config.optimized.json` | Output to new file |

## Who Benefits

**Developers building or maintaining MCP tool configs** — especially anyone running 10+ tools where selection errors have started appearing, or anyone hitting context window limits from schema overhead.

## Token Cost

~1,537 tokens (6,149 chars ÷ 4). Typical schema optimization recovers 3–6x that in savings per session.

## Example

**Before:**
```json
{
  "name": "search_codebase",
  "description": "Searches the codebase for relevant files and content."
}
```

**After:**
```json
{
  "name": "search_codebase",
  "description": "Use when looking for where a function, class, or pattern is defined or used. Performs semantic + keyword search across all project files. Returns: ranked list of file paths with matching snippets. Not for reading a known file path — use read_file for that."
}
```

Trigger condition explicit. Return shape defined. Boundary with similar tool stated. The model can now route correctly.

## Research

- [Anthropic tool use docs](https://docs.anthropic.com/en/docs/build-with-claude/tool-use) — description quality directly affects tool selection accuracy
- [Building effective agents](https://www.anthropic.com/research/building-effective-agents) — tool interface quality is as important as tool logic
