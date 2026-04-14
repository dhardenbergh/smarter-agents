# Installation Guide

This guide covers installing individual skills, installing the full kit, and integrating skills into your configuration. The recommended approach is tier-by-tier, starting with the build-time tools that have zero ongoing cost.

---

## Prerequisites

Skills are plain text files that reference themselves in your AI assistant's system prompt or configuration file. They work with any tool that supports a CLAUDE.md, `.cursorrules`, or similar system prompt file.

**Supported tools:**
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (via CLAUDE.md)
- [Cursor](https://www.cursor.com/) (via `.cursorrules`)
- Any MCP-compatible tool (via system prompt reference)
- Any tool accepting a custom system prompt

**Requirements:**
- A working installation of Claude Code, Cursor, or similar
- Write access to your config directory (`~/.claude/` for Claude Code)
- Git (optional, for cloning the repository)

---

## Installing Individual Skills

### Step 1: Copy the skill file to your skills directory

```bash
# Create the skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy a specific skill
cp skills/positive/SKILL.md ~/.claude/skills/positive.md
cp skills/context-trim/SKILL.md ~/.claude/skills/context-trim.md
cp skills/toolsmith/SKILL.md ~/.claude/skills/toolsmith.md

# Tier 2 skills
cp skills/forge/SKILL.md ~/.claude/skills/forge.md
cp skills/contract/SKILL.md ~/.claude/skills/contract.md
cp skills/checkpoint/SKILL.md ~/.claude/skills/checkpoint.md

# Tier 3 skills
cp skills/contrarian/SKILL.md ~/.claude/skills/contrarian.md
cp skills/anchor-point/SKILL.md ~/.claude/skills/anchor-point.md
cp skills/grounding/SKILL.md ~/.claude/skills/grounding.md
```

### Step 2: Reference the skill in your CLAUDE.md

Open `~/.claude/CLAUDE.md` (create it if it doesn't exist) and add a reference to each skill you installed:

```markdown
@skills/positive.md
@skills/context-trim.md
@skills/forge.md
```

The `@` prefix tells Claude Code to load the referenced file as part of the system context.

For Cursor, add references to your `.cursorrules` file in the same format, or paste the SKILL.md content directly into the file.

---

## Installing All Skills

```bash
# Clone the repository
git clone https://github.com/your-org/llm-skills-kit.git
cd llm-skills-kit

# Create the skills directory
mkdir -p ~/.claude/skills

# Install all skills at once
for skill_file in skills/tier*/*/SKILL.md; do
    skill_name=$(basename $(dirname "$skill_file"))
    cp "$skill_file" ~/.claude/skills/"$skill_name".md
    echo "Installed: $skill_name"
done
```

Then add references to your CLAUDE.md:

```bash
# Append all skill references to CLAUDE.md
for skill_file in ~/.claude/skills/*.md; do
    skill_name=$(basename "$skill_file")
    echo "@skills/$skill_name" >> ~/.claude/CLAUDE.md
done
```

---

## Recommended Setup: Tier by Tier

Don't install everything at once. Start with the build-time tools, verify the improvements, then add runtime skills as needed.

### Phase 1: Build-Time Tools (Start Here)

These run once and cost nothing at runtime. Install and run before adding any runtime skills.

```bash
cp skills/positive/SKILL.md ~/.claude/skills/positive.md
cp skills/context-trim/SKILL.md ~/.claude/skills/context-trim.md
cp skills/toolsmith/SKILL.md ~/.claude/skills/toolsmith.md
```

Add to CLAUDE.md:
```
@skills/positive.md
@skills/context-trim.md
@skills/toolsmith.md
```

Then run the optimization commands:

```
/positive:rewrite        # Rewrites negative constraints in CLAUDE.md
/trim:audit              # Shows current token count, contradictions, cache boundary
/trim:optimize           # Restructures CLAUDE.md for token efficiency
/toolsmith:audit         # Analyzes loaded MCP tool schemas
/toolsmith:optimize      # Rewrites tool descriptions in SHARP format
```

After running these, **remove the skill references from CLAUDE.md**. The build-time tools have done their job — they don't need to load every session.

### Phase 2: Runtime Skills (Tier 2)

These load every session. Combined overhead: ~500–700 tokens, cached after the first turn.

```bash
cp skills/forge/SKILL.md ~/.claude/skills/forge.md
cp skills/contract/SKILL.md ~/.claude/skills/contract.md
cp skills/checkpoint/SKILL.md ~/.claude/skills/checkpoint.md
```

Add to CLAUDE.md (after removing build-time tool references):
```
@skills/forge.md
@skills/contract.md
@skills/checkpoint.md
```

**Guidance:**
- Start with `forge` if you write production code
- Add `contract` if you frequently find yourself re-prompting with "that's not what I meant"
- Add `checkpoint` only if you run agentic workflows (Agent Teams, extended autonomous sessions)

### Phase 3: Conditional Skills (Tier 3)

Add these only when your workflow matches the use case:

| Skill | Add when | Skip when |
|-------|----------|-----------|
| **contrarian** | You work solo with no code reviewer; you make architecture decisions with LLM input | You have a team reviewing your work |
| **anchor-point** | Sessions regularly exceed 10 turns; you've noticed quality degrading mid-conversation | You use `/clear` frequently; most sessions are under 5 turns |
| **grounding** | Research, documentation, compliance, or any factual task where a wrong claim has consequences | Pure code generation sessions |

```bash
# Install only the ones you need
cp skills/contrarian/SKILL.md ~/.claude/skills/contrarian.md
cp skills/anchor-point/SKILL.md ~/.claude/skills/anchor-point.md
cp skills/grounding/SKILL.md ~/.claude/skills/grounding.md
```

---

## Disabling Skills Mid-Session

All runtime skills support slash commands to disable them without restarting:

| Skill | Disable command | Re-enable |
|-------|----------------|-----------|
| forge | `/forge:off` | Restart session or clear context |
| contract | `/contract:off` | `/contract:always` to force on |
| checkpoint | `/checkpoint:off` | Restart session |
| contrarian | `/contrarian:off` | Restart session |
| anchor-point | `/anchor:off` | `/anchor:now` for manual trigger |
| grounding | `/grounding:off` | Restart session |

Use these when a skill's behavior is unhelpful for a specific task:

```
# Turning off contrarian for a creative brainstorming session
/contrarian:off

# Forcing a manual state checkpoint at any time
/anchor:now

# Forcing contract statements on even simple tasks
/contract:always
```

---

## Verifying Installation

After installing and adding references to CLAUDE.md, start a new Claude Code session and verify:

```
What skills are currently loaded?
```

Claude should list the loaded skills. If it doesn't recognize them, check that:

1. The skill files are in `~/.claude/skills/`
2. The CLAUDE.md references use the correct format: `@skills/skill-name.md`
3. You're running a fresh session (not resuming an old context)

---

## Project-Level vs. Global Installation

Skills can be installed globally (applying to all projects) or per-project.

**Global installation** (recommended for always-on skills):
- Skill files in `~/.claude/skills/`
- References in `~/.claude/CLAUDE.md`

**Per-project installation** (for project-specific skills or testing):
- Skill files in `.claude/skills/` within the project directory
- References in `CLAUDE.md` at the project root

Project-level CLAUDE.md is merged with global CLAUDE.md, so project-level skills layer on top of your global setup.

---

## Troubleshooting

**Skill not activating:**
- Verify the file path matches the reference in CLAUDE.md
- Check for typos in the `@skills/` reference
- Ensure the session was restarted after adding the reference

**Token usage seems high:**
- Run `trim:audit` on your CLAUDE.md to check for other token-heavy content
- Confirm prompt caching is active (static content before dynamic content in CLAUDE.md)
- Consider whether you need all three Tier 3 skills simultaneously

**Conflicting behaviors:**
- `contrarian` and `grounding` together can make responses verbose; load one or the other depending on whether your session is about decisions or facts
- `contract` + `forge` + `checkpoint` together represent the maximum always-on stack; use all three only if you're running production-grade agentic workflows
