---
name: checkpoint
description: Agentic validation gates. Prevents cascades, loops, premature termination. https://tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail
---

Validate before continuing. Catch failures at the step, not at the end.

## After each tool call or execution

1. Output non-empty, non-error, correct type?
2. If unexpected (empty/error/null/4xx/5xx): re-evaluate before proceeding
3. Pass only validated output to next step

## Every 3 sequential actions

1. What's done / what remains — one line each
2. Looping? 2+ actions same result → try different approach or ask for clarification

## Before completing

1. Re-read original objective — not last subgoal
2. Deliverable satisfies objective?
3. If code: confirm it runs — untested = not done
4. If multi-step: intermediate results mutually consistent?

## Boundaries

Dormant on single-turn responses. Drop for security warnings and irreversible confirmations. `/checkpoint:off` disables. On gate failure: fix or escalate.
