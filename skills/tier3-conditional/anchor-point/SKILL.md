---
name: anchor-point
description: >
  Multi-turn state management. Forces periodic state consolidation to prevent
  the 39% performance degradation that accumulates across long conversations.
  Dormant under 5 turns. Activate with /anchor:now. Disable with /anchor:off.
---

Track state. Re-anchor every 5 turns. Surface all assumption changes explicitly.

> **Why this exists:** All LLMs show [39% average performance drop in multi-turn vs single-turn](https://arxiv.org/abs/2505.06120) conversations. Scaling doesn't fix it. Root causes: premature assumptions locked in, over-reliance on prior responses, [attention bias toward first and last turns](https://arxiv.org/html/2602.07338v1). Periodic re-anchoring is the direct countermeasure.

## When active

Dormant for conversations under 5 turns — zero overhead. Activates automatically at turn 5, then every 5 turns after. A "turn" = one user message (assistant responses are not counted). Also triggers when user direction noticeably shifts mid-conversation.

## Re-anchor format

At turn 5 (and every 5 turns after), prepend a brief anchor block before the response:

```
— Anchor —
Goal: [1-2 sentences on what the user is trying to accomplish]
Assumptions: [bullet list of key things taken as given so far]
Needs revision: [anything from earlier turns that may no longer hold, or "None"]
Proceeding with updated understanding.
————————
```

Keep anchor blocks to 3–5 lines. This is a state check, not a summary essay.

## On contradictions

When the user contradicts or updates an earlier statement:
1. Acknowledge the change explicitly: "Noted — that changes the earlier assumption that [X]."
2. Re-evaluate any conclusions that depended on the old assumption.
3. Do not silently update. Silent updates cause downstream drift.

Example:
> User (turn 2): "This is a Postgres database."
> User (turn 8): "Actually it's MySQL."
>
> Correct response: "Noted — switching to MySQL. That changes the connection string and the window function syntax from turns 4 and 6. Revised versions below."

## Direction shifts

If the user's goal or scope changes mid-conversation — new constraints, different target, scope reduction — trigger an immediate re-anchor even if 5 turns haven't passed. State what changed and what that invalidates.

## Goal Check

If the re-anchor reveals a significant mismatch between assumed goal and actual goal, ask one clarifying question before proceeding. Resume normal flow after.

## Slash commands

| Command | Effect |
|---------|--------|
| `/anchor:now` | Force immediate state summary at any turn |
| `/anchor:off` | Disable for remainder of session |
