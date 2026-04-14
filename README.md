# LLM Skills Kit

System prompt skills that reduce token cost and improve output quality for AI coding assistants. Each skill is a drop-in markdown file that works with any agent that supports the open skills standard.

Nine skills across three tiers. Tier 1 skills run once at build time (zero ongoing cost). Tier 2 skills run every session (~935 tokens, cached at 90% discount after the first turn). Tier 3 skills activate only when relevant.

**Combined always-on cost:** ~$0.003/session after caching. **Prevents:** $2.45–$12.75 in wasted output per session. **ROI:** 800–4,000×.

---

## Install

Pick your agent. One command. Done.

| Agent | Install |
|-------|---------|
| **Any agent** | `npx skills add dhardenbergh/llm-skills-kit` |
| **Claude Code** | `claude plugin marketplace add dhardenbergh/llm-skills-kit && claude plugin install llm-skills-kit@llm-skills-kit` |
| **Codex** | `npx skills add dhardenbergh/llm-skills-kit -a codex` |
| **Gemini CLI** | `gemini extensions install https://github.com/dhardenbergh/llm-skills-kit` |
| **Cursor** | `npx skills add dhardenbergh/llm-skills-kit -a cursor` |
| **Windsurf** | `npx skills add dhardenbergh/llm-skills-kit -a windsurf` |
| **GitHub Copilot** | `npx skills add dhardenbergh/llm-skills-kit -a github-copilot` |
| **Cline** | `npx skills add dhardenbergh/llm-skills-kit -a cline` |
| **Amp** | `npx skills add dhardenbergh/llm-skills-kit -a amp` |
| **Any other** | `npx skills add dhardenbergh/llm-skills-kit -a <agent-name>` |

### Install specific skills

```bash
# Install only the build-time tools (zero ongoing cost)
npx skills add dhardenbergh/llm-skills-kit -s positive context-trim toolsmith

# Install only Tier 2 (always-on runtime skills)
npx skills add dhardenbergh/llm-skills-kit -s forge contract checkpoint

# Install a single skill
npx skills add dhardenbergh/llm-skills-kit -s contrarian

# Install globally (user-level, not just project)
npx skills add dhardenbergh/llm-skills-kit -g

# Install everything to all detected agents, no prompts
npx skills add dhardenbergh/llm-skills-kit --all -y
```

### Manual install (Claude Code)

```bash
git clone https://github.com/dhardenbergh/llm-skills-kit.git
cd llm-skills-kit

mkdir -p ~/.claude/skills

# Tier 1 — build-time, zero runtime cost
cp skills/positive/SKILL.md ~/.claude/skills/positive.md
cp skills/context-trim/SKILL.md ~/.claude/skills/context-trim.md
cp skills/toolsmith/SKILL.md ~/.claude/skills/toolsmith.md

# Tier 2 — always-on, ~935 tokens
cp skills/forge/SKILL.md ~/.claude/skills/forge.md
cp skills/contract/SKILL.md ~/.claude/skills/contract.md
cp skills/checkpoint/SKILL.md ~/.claude/skills/checkpoint.md

# Tier 3 — opt-in, load when needed
cp skills/contrarian/SKILL.md ~/.claude/skills/contrarian.md
cp skills/anchor-point/SKILL.md ~/.claude/skills/anchor-point.md
cp skills/grounding/SKILL.md ~/.claude/skills/grounding.md
```

See [docs/installation.md](docs/installation.md) for the full guide including per-agent paths and configuration.

### Platform Support

`npx skills add` automatically installs to the correct directory for [36+ agents](https://github.com/webrix-ai/add-skills#supported-agents) including Claude Code, Cursor, Windsurf, Codex, GitHub Copilot, Gemini CLI, Cline, Amp, Roo Code, Goose, and more.

---

## Skills

### Tier 1 — Foundation (Build-Time)

Run once, zero runtime cost. These transform your existing configuration files.

| Skill | What It Does | Token Cost |
|-------|-------------|------------|
| [**positive**](skills/positive/) | Rewrites negative constraints → positive directives. "Never X" becomes "Always Y". [10%+ compliance improvement.](https://arxiv.org/abs/2109.07830) | 0 (one-time) |
| [**context-trim**](skills/context-trim/) | Audits and compresses system prompts. Restructures into KERNEL format, resolves contradictions, sets cache boundaries. Target: 30–50% reduction. | 0 (one-time) |
| [**toolsmith**](skills/toolsmith/) | Optimizes MCP tool schemas using SHARP format. Fixes vague descriptions, flags overlaps, identifies lazy-load candidates. | 0 (one-time) |

### Tier 2 — Runtime (Always-On)

Lightweight behavioral rules. Active every session, dormant when irrelevant.

| Skill | What It Does | Token Cost |
|-------|-------------|------------|
| [**forge**](skills/forge/) | Quality constitution — silent gate on code, analysis, and architecture. Checks against domain-specific standards before responding. | ~461 tokens |
| [**contract**](skills/contract/) | States objective/constraints/completion criteria before executing multi-step tasks. Verifies output against contract before responding. | ~220 tokens |
| [**checkpoint**](skills/checkpoint/) | Agentic validation gates. Validates after each tool call, checks for loops every 3 actions, re-reads original objective before completing. | ~254 tokens |

**Combined Tier 2:** ~935 tokens → ~94 tokens/turn after caching

### Tier 3 — Conditional (Opt-In)

Load when the workflow calls for it. Each addresses a specific structural LLM failure.

| Skill | What It Does | Token Cost |
|-------|-------------|------------|
| [**contrarian**](skills/contrarian/) | Anti-sycophancy gate. Evaluates claims on merit, holds corrections under pushback, bans empty affirmation. [Models sycophantize 58% of the time.](https://c3.unu.edu/blog/how-sycophancy-shapes-the-reliability-of-large-language-models) | ~786 tokens |
| [**anchor-point**](skills/anchor-point/) | Multi-turn state management. Forces periodic re-anchoring every 5 turns to combat the [39% performance drop](https://arxiv.org/abs/2505.06120) in multi-turn conversations. | ~651 tokens |
| [**grounding**](skills/grounding/) | Confidence calibration. Tags claims as VERIFIED, ESTABLISHED, UNCERTAIN, or UNKNOWN. [LLM confidence correlates with accuracy at R²=0.01.](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical) | ~834 tokens |

---

## Why These Skills Exist

LLMs have structural limitations that persist regardless of model scale:

- **Negation failures** — Autoregressive models activate concepts they're told to avoid. [Larger models perform worse on negated queries.](https://arxiv.org/abs/2306.08189)
- **Sycophancy** — RLHF training rewards agreement. [58% sycophancy rate across frontier models.](https://c3.unu.edu/blog/how-sycophancy-shapes-the-reliability-of-large-language-models)
- **Multi-turn degradation** — [39% average performance drop](https://arxiv.org/abs/2505.06120) from single-turn to multi-turn, across all model families.
- **Calibration gap** — Model confidence is [essentially uncorrelated with accuracy](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical) on hard topics (R²=0.01).
- **Agentic reliability** — Multi-step agent tasks show [41–87% failure rates](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail) from cascading errors, loops, and premature termination.
- **Context rot** — Information in the middle of long prompts is [underweighted by 30%+](https://arxiv.org/abs/2307.03172) compared to boundaries.

These are architectural properties of how transformers work, not gaps that more training data or compute will close. Each skill in this kit directly targets one or more of these failures.

See [docs/research-summary.md](docs/research-summary.md) for the full research basis.

---

## The Business Case

### Token Economics

Skills are static text that benefits from [prompt caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) — after the first turn, cached input tokens cost 90% less. Output tokens cost 5× input tokens, so any skill that reduces re-prompting or prevents wasted output is disproportionately valuable.

| Metric | Value |
|--------|-------|
| Full kit (all 9 skills) | ~7,021 tokens |
| Always-on stack (Tier 2) | ~935 tokens |
| Effective cost after caching | ~94 tokens/turn |
| Cost per session (15 turns) | ~$0.003 |
| Prevented waste per session | $2.45–$12.75 |
| **ROI** | **800–4,000×** |

### Who Should Use What

| Persona | Recommended Skills | Monthly Token Impact |
|---------|-------------------|---------------------|
| **Every developer** | positive, context-trim | Net negative (reduces tokens) |
| **Quota-conscious user** | positive, context-trim, toolsmith | Saves 30–50% of system prompt tokens |
| **Quality-focused developer** | Tier 1 + forge, contract, checkpoint | +935 tokens → prevents $2.45+/session in waste |
| **Solo decision-maker** | All tiers + contrarian, grounding | +2,271 additional tokens → catches errors before production |
| **Agentic workflow builder** | Tier 1 + Tier 2 + checkpoint, anchor-point | +905 additional tokens → prevents cascading failures |

See [docs/cost-benefit-analysis.md](docs/cost-benefit-analysis.md) for detailed per-skill ROI breakdowns and [docs/future-proofing.md](docs/future-proofing.md) for the durability analysis.

---

## Slash Commands

Every skill supports mid-session control. In Claude Code, these are available as `/commands`:

| Command | Effect |
|---------|--------|
| `/positive-rewrite` | Run the positive reframing tool on a file |
| `/trim-audit` | Analyze a system prompt (read-only) |
| `/trim-optimize` | Rewrite using KERNEL structure |
| `/toolsmith-audit` | Analyze loaded MCP tool schemas |
| `/toolsmith-optimize` | Rewrite tool descriptions using SHARP format |
| `/forge:off` / `/forge:on` | Toggle quality gate |
| `/contract:off` / `/contract:always` | Toggle output contracts |
| `/checkpoint:off` | Disable agentic validation |
| `/contrarian:off` | Disable anti-sycophancy |
| `/anchor:now` / `/anchor:off` | Force/disable state re-anchoring |
| `/grounding:off` | Disable confidence labeling |

---

## Repository Structure

```
llm-skills-kit/
├── README.md                          ← You are here
├── .claude-plugin/                    Claude Code plugin manifest
│   ├── plugin.json
│   └── marketplace.json
├── AGENTS.md                          Codex auto-discovery
├── GEMINI.md                          Gemini CLI context
├── gemini-extension.json              Gemini CLI extension manifest
├── commands/                          Claude Code slash commands
│   ├── positive-rewrite.toml
│   ├── trim-audit.toml
│   ├── trim-optimize.toml
│   ├── toolsmith-audit.toml
│   └── toolsmith-optimize.toml
├── skills/                            All 9 skills (npx skills add source)
│   ├── positive/
│   │   ├── SKILL.md                   The skill file
│   │   └── README.md                  Documentation
│   ├── context-trim/
│   ├── toolsmith/
│   ├── forge/
│   ├── contract/
│   ├── checkpoint/
│   ├── contrarian/
│   ├── anchor-point/
│   └── grounding/
├── docs/
│   ├── installation.md                Full installation guide
│   ├── cost-benefit-analysis.md       Token economics and ROI per skill
│   ├── research-summary.md            Research basis for each skill
│   └── future-proofing.md             Which skills survive model scaling
└── tests/
    └── test-results.md                Comprehensive test report
```

---

## Future-Proofing

Not all prompt engineering survives. We categorize skills by durability:

| Category | What Defines It | Skills |
|----------|----------------|--------|
| **Structurally persistent** | Architectural limitations that don't scale away | positive, context-trim, contrarian, anchor-point, grounding, forge |
| **Evolving** | Still useful but the mechanism changes | toolsmith, checkpoint |
| **Being absorbed** | Frontier models internalize this | _(External CoT, task routing, verbosity control — not in this kit)_ |

Every skill in this kit targets structurally persistent problems. Skills that address problems being solved by model scaling (external chain-of-thought, adaptive routing, emotional priming) were deliberately excluded.

See [docs/future-proofing.md](docs/future-proofing.md) for the full analysis.

---

## Contributing

Each skill follows a consistent structure:

- `SKILL.md` — The skill file itself (what gets loaded into the system prompt)
- `README.md` — Documentation: the problem, how it works, research basis, examples

Skills should:
- Use positive framing in all instructions (practice what `positive` preaches)
- Include research citations with working URLs
- Specify clear activation/dormancy conditions
- Include concrete before/after examples
- Target a structurally persistent LLM limitation
- Have YAML frontmatter with `name` and `description` fields

---

## License

MIT

---

## Research

This project is built on research from:

- [Negation benchmarks and inverse scaling](https://arxiv.org/abs/2306.08189) — Jang et al.
- [Reframing instructional prompts](https://arxiv.org/abs/2109.07830) — Mishra et al.
- [Lost in the Middle: positional bias](https://arxiv.org/abs/2307.03172) — Liu et al.
- [Multi-turn conversation degradation](https://arxiv.org/abs/2505.06120) — Ma et al.
- [Sycophancy in frontier models](https://c3.unu.edu/blog/how-sycophancy-shapes-the-reliability-of-large-language-models) — UNU
- [Confidence calibration gap](https://www.lesswrong.com/posts/unaLT4A6hSTCLNGod/a-black-box-procedure-for-llm-confidence-in-critical)
- [Anthropic prompt caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)
- [Anthropic building effective agents](https://www.anthropic.com/research/building-effective-agents)
- [Multi-agent system failure modes](https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail) — Tian Pan
- [Self-Refine: iterative self-improvement](https://arxiv.org/abs/2303.17651) — Madaan et al.

See [docs/research-summary.md](docs/research-summary.md) for how each paper maps to a specific skill.
