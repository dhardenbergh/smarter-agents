# forge

Silent quality gate for code, analysis, and architecture output. Checks output against a domain-specific constitution before responding. Fixes silently; surfaces issues only when user input is required.

## The Problem

LLMs produce plausible-looking output that fails on specifics: missing error handling, hardcoded values, unacknowledged counterarguments, undocumented failure modes. These aren't reasoning failures — they're omissions the model doesn't know to flag.

The cost is re-prompting. A rejected code review that requires a fix cycle costs 30,000–50,000 tokens (context re-read + new generation + explanation). [Rationale-bearing instructions dramatically improve adherence](https://www.reddit.com/r/ClaudeCode/comments/1scml8e/) because the model can generalize the intent, not just follow the letter. Forge embeds those rationales directly into the quality check.

[Anthropic's building-effective-agents research](https://www.anthropic.com/research/building-effective-agents) identifies task-specific behavioral specification as one of the highest-leverage interventions in agentic systems — better alignment reduces safety failures but cannot anticipate domain-specific quality requirements.

## How It Works

Forge maintains three quality constitutions — one each for code, analysis/writing, and architecture/design. Before outputting any non-trivial response in those domains, the model checks the output against the relevant constitution and fixes issues silently.

**Code constitution:** Error path coverage, single-responsibility naming, null/edge/overflow/concurrency handling, no hardcoded values, readability for new team members.

**Analysis/writing constitution:** Factual claims cite their basis, counterarguments acknowledged, known vs. inferred labeled, "so what" stated.

**Architecture/design constitution:** Failure modes named with mitigations, 10x/100x scaling stated, security boundaries explicit, migration path defined.

The gate is dormant on conversational responses and simple tasks. No overhead when there's nothing to check.

## Installation

```
cp SKILL.md ~/.claude/skills/forge.md
```

Then reference it in your CLAUDE.md:
```
@skills/forge.md
```

## Commands

| Command | Description |
|---------|-------------|
| `/forge:off` | Disable the quality gate for the remainder of the session |

## Who Benefits

**Developers writing production code** — anyone whose output goes through code review, whose analysis informs decisions, or whose architecture designs get built on. The gate catches the most common failure patterns before the model commits to output, reducing fix cycles without requiring you to enumerate rules yourself.

Also useful for: technical leads reviewing proposals, architects documenting designs, anyone who has noticed "Claude agreed with me but missed the obvious problem."

## Token Cost

~200–400 tokens (the three domain constitutions). Cached after the first turn of each session — amortized cost approaches near-zero across a multi-turn session.

**ROI:** One prevented re-prompt cycle saves 30,000–50,000 output tokens. At Sonnet 4.6 pricing ($15/M output), that's $0.45–$0.75 per cycle. The skill's cached input cost is ~$0.001. That's a 450–750x return per prevented failure.

## Example

**Without forge — code output:**
```python
def get_user(user_id):
    result = db.query(f"SELECT * FROM users WHERE id = {user_id}")
    return result[0]
```

**With forge — same request:**
```python
def get_user(user_id: int) -> dict | None:
    """Fetch a single user by ID. Returns None if not found."""
    if not isinstance(user_id, int) or user_id <= 0:
        raise ValueError(f"Invalid user_id: {user_id}")
    results = db.query(
        "SELECT * FROM users WHERE id = ?",
        (user_id,)
    )
    return results[0] if results else None
```

SQL injection handled. Input validated. Return type declared. None case covered. Readable by a new team member. The constitution enforced all five code checks without being asked.

## Research

- [Building effective agents (Anthropic)](https://www.anthropic.com/research/building-effective-agents) — task-specific behavioral specification is as important as alignment
- [Constitutional AI (arxiv 2212.08073)](https://arxiv.org/abs/2212.08073) — constitution-based self-critique improves output quality without human oversight at each step
- [Community testing: rationale-bearing instructions](https://www.reddit.com/r/ClaudeCode/comments/1scml8e/) — instructions with "why" dramatically improve adherence
