# Installation Guide

Install skills with a single command using [`npx skills add`](https://github.com/webrix-ai/add-skills), or copy files manually. Skills work with 36+ AI coding agents.

---

## Quick Install (Recommended)

```bash
# Install all 9 skills for your agent
npx skills add dhardenbergh/smarter-agents

# Install for a specific agent
npx skills add dhardenbergh/smarter-agents -a claude-code
npx skills add dhardenbergh/smarter-agents -a cursor
npx skills add dhardenbergh/smarter-agents -a codex
npx skills add dhardenbergh/smarter-agents -a windsurf
npx skills add dhardenbergh/smarter-agents -a github-copilot
npx skills add dhardenbergh/smarter-agents -a cline
npx skills add dhardenbergh/smarter-agents -a amp
```

### Install specific skills only

```bash
# Tier 1 — build-time tools (zero runtime cost)
npx skills add dhardenbergh/smarter-agents -s positive context-trim toolsmith

# Tier 2 — always-on runtime skills
npx skills add dhardenbergh/smarter-agents -s forge contract checkpoint

# Single skill
npx skills add dhardenbergh/smarter-agents -s contrarian
```

### Flags

| Flag | Effect |
|------|--------|
| `-a <agent>` | Target a specific agent (claude-code, cursor, codex, windsurf, etc.) |
| `-s <name>` | Install specific skill(s) by name |
| `-y` | Skip confirmation prompts |
| `-g` | Install globally (all projects) |
| `--list` | Preview available skills without installing |

---

## Manual Install (Claude Code)

```bash
git clone https://github.com/dhardenbergh/smarter-agents.git
cd smarter-agents
mkdir -p ~/.claude/skills

# Tier 1 — build-time, zero runtime cost
cp skills/positive/SKILL.md ~/.claude/skills/positive.md
cp skills/context-trim/SKILL.md ~/.claude/skills/context-trim.md
cp skills/toolsmith/SKILL.md ~/.claude/skills/toolsmith.md

# Tier 2 — always-on, ~935 tokens
cp skills/forge/SKILL.md ~/.claude/skills/forge.md
cp skills/contract/SKILL.md ~/.claude/skills/contract.md
cp skills/checkpoint/SKILL.md ~/.claude/skills/checkpoint.md

# Tier 3 — conditional, opt-in
cp skills/contrarian/SKILL.md ~/.claude/skills/contrarian.md
cp skills/anchor-point/SKILL.md ~/.claude/skills/anchor-point.md
cp skills/grounding/SKILL.md ~/.claude/skills/grounding.md
```

Then reference in your CLAUDE.md:

```markdown
@skills/positive.md
@skills/context-trim.md
@skills/forge.md
```

---

## Platform-Specific Notes

### Claude Code Plugin

If you use the Claude Code plugin system:

```bash
claude plugin marketplace add dhardenbergh/smarter-agents
claude plugin install smarter-agents@smarter-agents
```

### Codex

The repo includes an `AGENTS.md` file. Codex loads it automatically when the repo is cloned or referenced.

### Gemini CLI

```bash
gemini extensions install https://github.com/dhardenbergh/smarter-agents
```

### Cursor / Windsurf / Others

Use the `npx skills add` command with the appropriate `-a` flag (see Quick Install above). The CLI handles path detection automatically for each agent.

---

## Recommended Setup: Tier by Tier

Start with the build-time tools, verify the improvements, then add runtime skills as needed.

### Phase 1: Build-Time Tools (Start Here)

These run once and cost nothing at runtime. Install, run the commands, then remove the skill references.

```bash
npx skills add dhardenbergh/smarter-agents -s positive context-trim toolsmith
```

Run the optimization commands:

```
/positive:rewrite        # Rewrites negative constraints in CLAUDE.md
/trim:audit              # Shows current token count, contradictions, cache boundary
/trim:optimize           # Restructures CLAUDE.md for token efficiency
/toolsmith:audit         # Analyzes loaded MCP tool schemas
/toolsmith:optimize      # Rewrites tool descriptions in SHARP format
```

After running these, remove the Tier 1 skill references from CLAUDE.md — they've done their job.

### Phase 2: Runtime Skills (Tier 2)

Combined overhead: ~935 tokens, cached after the first turn.

```bash
npx skills add dhardenbergh/smarter-agents -s forge contract checkpoint
```

**Guidance:**
- Start with `forge` if you write production code
- Add `contract` if you frequently re-prompt with "that's not what I meant"
- Add `checkpoint` only if you run agentic workflows (extended autonomous sessions)

### Phase 3: Conditional Skills (Tier 3)

Add only when your workflow matches:

| Skill | Add when | Skip when |
|-------|----------|-----------|
| **contrarian** | You work solo with no code reviewer | You have a team reviewing your work |
| **anchor-point** | Sessions regularly exceed 10 turns | You use `/clear` frequently |
| **grounding** | Factual tasks where a wrong claim has consequences | Pure code generation sessions |

```bash
npx skills add dhardenbergh/smarter-agents -s contrarian anchor-point grounding
```

---

## Disabling Skills Mid-Session

All runtime skills support slash commands to toggle them without restarting:

| Skill | Disable | Re-enable |
|-------|---------|-----------|
| forge | `/forge:off` | Restart session |
| contract | `/contract:off` | `/contract:always` |
| checkpoint | `/checkpoint:off` | Restart session |
| contrarian | `/contrarian:off` | Restart session |
| anchor-point | `/anchor:off` | `/anchor:now` |
| grounding | `/grounding:off` | Restart session |

---

## Verifying Installation

Start a new session and ask:

```
What skills are currently loaded?
```

If skills aren't recognized, check:

1. Skill files exist in the correct directory for your agent
2. References use the correct format (e.g., `@skills/skill-name.md` for Claude Code)
3. You started a fresh session after adding references

---

## Global vs. Project-Level

**Global** (recommended for always-on skills):
```bash
npx skills add dhardenbergh/smarter-agents -g
```
Skills install to your global config directory and apply to all projects.

**Per-project** (for testing or project-specific setups):
```bash
npx skills add dhardenbergh/smarter-agents
```
Skills install to the current project directory. Project-level skills layer on top of global ones.

---

## Troubleshooting

**Skill not activating:**
- Verify the file path matches the reference in your config
- Restart the session after adding references
- Check for typos in the `@skills/` path

**Token usage seems high:**
- Run `/trim:audit` on your CLAUDE.md to check for bloat
- Confirm prompt caching is active (static content before dynamic content)
- Consider whether you need all Tier 3 skills simultaneously

**Conflicting behaviors:**
- `contrarian` + `grounding` together can make responses verbose — pick one per session
- `contract` + `forge` + `checkpoint` is the maximum always-on stack — use all three only for production-grade agentic workflows
