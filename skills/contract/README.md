# contract

States a verifiable output contract before executing any non-trivial task. Eliminates the most expensive failure mode in AI-assisted development: generating output that doesn't match what the user actually wanted.

## The Problem

"Build me a migration script" is not a specification. It's an invitation for the model to pick from dozens of valid interpretations — reversible or not, idempotent or not, handles nulls or not, includes rollback or not. When the output doesn't match the mental model, the user re-prompts. That re-prompt resets the context.

[Feature implementation costs 100K–200K input tokens](https://systemprompt.io/guides/claude-code-cost-optimisation). A single misaligned output that requires "no, I meant..." doubles that cost. [Precise prompts save 50,000+ tokens per task](https://systemprompt.io/guides/claude-code-cost-optimisation) compared to vague ones — Contract forces that precision automatically.

Both [OpenAI](https://developers.openai.com/api/docs/guides/prompt-guidance) and [Anthropic](https://blog.promptlayer.com/what-we-can-learn-from-anthropics-system-prompt-updates/) now explicitly recommend output contracts as the direction of production prompt engineering. `<output_contract>` blocks are in OpenAI's GPT-5.4 guidance. PromptOps treats prompts as [production code with versioning and testing](https://blog.eif.am/llm-prompt-engineering/). Contract makes this discipline automatic.

## How It Works

On any non-trivial task (multi-step, open-ended, or with multiple valid output forms), Contract states a 2–4 line specification before proceeding:

```
OBJECTIVE: [what is being produced — 1 sentence]
CONSTRAINTS: [hard limits, not preferences]
COMPLETION: [binary yes/no verification — how to confirm the task is done]
```

Simple tasks with one clear interpretation proceed directly — no overhead.

After completing: verify the output satisfies the stated contract. Fix before responding if it doesn't.

## Installation

```
cp SKILL.md ~/.claude/skills/contract.md
```

Then reference it in your CLAUDE.md:
```
@skills/contract.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/contract:off` | Disable contract statements for the remainder of the session |
| `/contract:always` | Force contract statements on all tasks, including simple ones |

## Who Benefits

**Architects and tech leads** who define high-level goals and expect precise execution — people who are frequently frustrated by "that's not what I meant" more than by token cost. Also useful for any developer working on ambiguous or complex tasks where a wrong first pass is more expensive than a brief alignment check.

Particularly valuable for: database migrations, API design, refactoring instructions, multi-file changes, anything where "done" has a specific meaning that's easy to verify.

## Token Cost

~150–250 tokens (the skill itself). Each contract statement adds ~50–80 tokens to that turn's output. The contract is offset by eliminating misaligned output — one prevented re-prompt cycle on a feature-scale task saves more tokens than the skill will ever cost.

**ROI:** The first task where "I meant X, not Y" is caught by the contract pays for thousands of turns of the skill being loaded.

## Example

**User:** "Write me a migration script for the users table."

**With contract:**
```
OBJECTIVE: SQL migration adding `last_login_at` timestamp column to users table
CONSTRAINTS: Idempotent (safe to run twice), reversible (includes rollback), no data loss, handles NULL for existing rows
COMPLETION: Script runs without error on both empty and populated databases; UP and DOWN sections both present

Proceeding...
```

The contract surfaces what "migration script" means in this context before any code is written. If the user wanted something different — say, a data migration rather than a schema change — they catch it here, not after 200 lines of SQL.

## Research

- [OpenAI prompt guidance](https://developers.openai.com/api/docs/guides/prompt-guidance) — recommends explicit output contracts for complex tasks
- [Anthropic system prompt evolution](https://blog.promptlayer.com/what-we-can-learn-from-anthropics-system-prompt-updates/) — system prompts moving from guidelines to hard triggers and explicit completion criteria
- [Claude Code cost optimization](https://systemprompt.io/guides/claude-code-cost-optimisation) — precise prompts save 50,000+ tokens per task vs. vague ones
- [PromptOps as production code](https://blog.eif.am/llm-prompt-engineering/) — the discipline of treating prompts as versioned, testable specifications
