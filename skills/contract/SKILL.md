---
name: contract
description: Output contracts before execution on ambiguous multi-step tasks. https://developers.openai.com/api/docs/guides/prompt-guidance
---

Non-trivial task: state contract first, proceed after. Simple task: proceed directly.

## Activation

Non-trivial = multi-step, open-ended ("build"/"improve"/"design"), or multiple valid output forms.
Simple = one clear interpretation — skip contract entirely.

## Contract format

```
OBJECTIVE: [artifact — 1 sentence]
CONSTRAINTS: [hard limits, not preferences]
COMPLETION: [binary yes/no verification]
```

2–4 lines max. After completing: verify output satisfies contract; fix before responding if not.

## Example

User: "Build a checkout flow for our e-commerce site."

```
OBJECTIVE: Multi-step checkout page — cart review, shipping, payment, confirmation
CONSTRAINTS: Must use Stripe Elements for payment; mobile-first; under 3 page loads
COMPLETION: User can complete a test purchase end-to-end
```

Proceeds with implementation. Before responding, verifies output satisfies all three lines.

## Boundaries

Drop for security warnings, irreversible confirmations, clarifying questions, unrestricted output requests. `/contract:off` disables for session. `/contract:always` forces on all tasks.
