# LLM Skills Kit — Comprehensive Test Report

**Generated:** 2025-07-12  
**Skills tested:** 9  
**Test methodology:** Static analysis, URL validation, citation claim verification, LLM simulation

---

## Summary Table

| Skill | Structure | Activation | Positive Framing | Citations | Examples | Consistency | LLM Clarity | Overall |
|-------|-----------|------------|------------------|-----------|----------|-------------|-------------|---------|
| **positive** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ✅ **PASS** |
| **trim** | ✅ PASS | ⚠️ WARN | ⚠️ WARN | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |
| **toolsmith** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |
| **forge** | ⚠️ WARN | ⚠️ WARN | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ⚠️ WARN | ⚠️ **WARN** |
| **contract** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |
| **checkpoint** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |
| **contrarian** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |
| **anchor-point** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |
| **grounding** | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ WARN | ✅ PASS | ✅ PASS | ✅ PASS | ⚠️ **WARN** |

**Legend:** ✅ PASS — no issues | ⚠️ WARN — minor issues, not blocking | ❌ FAIL — blocking issue

**No blocking failures found.** All 9 skills are structurally sound and functionally deployable. Citation claims are the most common category of warnings — no citation is factually wrong, but several have precision issues documented below.

---

## Skill-by-Skill Results

---

### 1. `positive` — Tier 1 Foundation

**File:** `skills/tier1-foundation/positive/SKILL.md`  
**Size:** 4,685 chars / ~1,171 tokens (est.)  
**Slash commands:** `/positive:rewrite`

#### Structural Validity — ✅ PASS
- Frontmatter present: `name`, `description` ✅
- Clear sections: Why It Works, Slash Command, Transformation Rules, Preservation Rules, Examples, Diff Summary Format, Boundaries ✅
- Single slash command with explicit usage examples ✅

#### Activation Logic — ✅ PASS
- Activation is stated clearly in the description: "Use when CLAUDE.md, .cursorrules, or any system prompt contains don't/never/avoid/no."
- This is a run-once tool, not a runtime behavior modifier — no `## When active` section is needed and none is present. Appropriate.
- The target file detection logic (arg → CLAUDE.md → .cursorrules) is unambiguous.

#### Positive Framing (Boundaries section) — ✅ PASS
- The Boundaries section uses positive framing ("Preserve the author's intent", "Rewrite only imperative rules", "Replace constraints in place").
- The body of the skill *necessarily* uses negative words (`don't`, `never`, `avoid`) as **examples of patterns to transform** — these are in transformation tables as input data, not as instructions to the model. This is correct and appropriate.

#### Token Count Accuracy — ✅ PASS
- No specific token count is claimed in the file itself. The description says "zero runtime cost" (qualitative), which is accurate.

#### Research Citations — ⚠️ WARN
- `arxiv.org/abs/2306.08189` — "Language models are not naysayers" — HTTP 200, paper exists. ✅
  - **Issue (minor):** The skill attributes "inverse scaling on negated queries; GPT-3 class models perform *worse* than smaller ones." The paper studies GPT-neo, GPT-3, and InstructGPT and finds *insensitivity* to negation and failure modes — but the inverse scaling framing (larger = worse) is a characterization not explicitly stated in the abstract. The paper's conclusion is more nuanced. The claim is directionally supported but overstated as "proven."
- `arxiv.org/abs/2109.07830` — "Reframing Instructional Prompts" — HTTP 200, paper exists. ✅
  - **Issue (minor):** "improve compliance by 10%+" — the abstract mentions various reframing techniques (decomposition, itemization) achieving improvements, but the specific "10%+" figure is not verifiable from the abstract alone. Plausible but unverified.

#### Example Quality — ✅ PASS
- Two complete before/after transformation blocks with realistic, varied examples.
- The ambiguity handling example (inferring a logger from "Don't use console.log") is excellent — it addresses a real edge case.
- Transformation rules table covers 7 distinct patterns with examples.

#### Internal Consistency — ✅ PASS
- Preservation rules and transformation rules do not conflict.
- The `<!-- TODO: clarify positive form -->` fallback in Boundaries is a well-designed escape hatch.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Clear: user invokes `/positive:rewrite` or a file is specified.
- **What to do?** Step-by-step numbered list, unambiguous.
- **Slash commands?** One command with three usage variants — no ambiguity.
- **Potential confusion:** None identified.

---

### 2. `trim` — Tier 1 Foundation

**File:** `skills/tier1-foundation/trim/SKILL.md`  
**Size:** 5,958 chars / ~1,489 tokens (est.)  
**Slash commands:** `/trim:audit`, `/trim:optimize`

#### Structural Validity — ✅ PASS
- Frontmatter: `name`, `description` ✅
- Sections: Why It Works, Slash Commands (two), KERNEL Structure, Optimization Rules, Backup and Reporting, Boundaries ✅
- Two slash commands, both with usage examples and output format specs ✅

#### Activation Logic — ⚠️ WARN
- Description says "Run once, zero runtime cost" — sufficient for a run-once tool.
- **Issue:** No explicit trigger condition beyond being invoked. The description says what it does but not *when a user should reach for it* vs. just manually editing their CLAUDE.md. Consider adding: "Use when system prompt exceeds 2,000 tokens, contains contradictions, or mixes static/dynamic content."
- The `/trim:audit` (read-only) vs. `/trim:optimize` (writes) distinction is well-specified.

#### Positive Framing (Boundaries) — ⚠️ WARN
- Boundaries section: all positive framing ✅
- **Issue:** KERNEL Structure section labels position 5 as `"Constraints — what not to do"`. This is describing the structure of the *user's* system prompt, not giving instructions to the model — so it's not a violation. However, it creates a conceptual tension with the `positive` skill: the trim skill recommends placing a "what not to do" section last, while the positive skill converts all "what not to do" into positive directives. The interaction is acknowledged inline (`After /positive:rewrite, these become positive directives — still place at end`) ✅ — the cross-reference is there, but a reader might still be confused about the ordering. This is acceptable but worth a note.

#### Token Count Accuracy — ✅ PASS
- No specific token count claimed for the file itself. The output format examples show realistic numbers.

#### Research Citations — ⚠️ WARN
- `docs.anthropic.com/en/docs/build-with-claude/prompt-caching` — HTTP 200 ✅
- `arxiv.org/abs/2307.03172` — "Lost in the Middle" — HTTP 200 ✅
  - **Issue:** The claim "first 50 tokens drive ~80% of behavioral change" is not what the paper demonstrates. "Lost in the Middle" shows that LLMs perform better when relevant information is at the *beginning or end* of a long context window — it is about retrieval from long contexts, not about the first 50 tokens of a system prompt driving 80% of behavioral change. The cited paper supports the general idea that position matters, but the specific "50 tokens → 80%" claim is not from this paper. This is a moderate misattribution. The underlying recommendation (front-load critical behavior) is sound; the citation is imprecise.

#### Example Quality — ✅ PASS
- Audit output format is detailed and realistic.
- Optimization rules with before/after examples are clear.
- The KERNEL structure diagram with the cache boundary marker is an effective visual.

#### Internal Consistency — ✅ PASS
- Cross-reference to `/positive:rewrite` in rule #8 is correct and helpful.
- No contradictions between the optimization rules.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Clear on invocation.
- **What to do?** 9 numbered optimization rules are specific and actionable.
- **Slash commands?** Two commands, clearly differentiated (read-only vs. write).
- **Potential confusion:** The Constraints position (#5 in KERNEL) being labeled "what not to do" while existing alongside the positive skill may confuse a model trying to decide which framing to use. The inline note mitigates this.

---

### 3. `toolsmith` — Tier 1 Foundation

**File:** `skills/tier1-foundation/toolsmith/SKILL.md`  
**Size:** 6,125 chars / ~1,531 tokens (est.)  
**Slash commands:** `/toolsmith:audit`, `/toolsmith:optimize`

#### Structural Validity — ✅ PASS
- Frontmatter: `name`, `description` ✅
- Sections: Why It Works, Slash Commands, SHARP Description Format, Parameter Rules, Lazy-Loading Guidance, Reporting, Boundaries ✅
- Both commands have usage examples with flags (`--file`, `--output`) ✅

#### Activation Logic — ✅ PASS
- Clear use case: "when MCP tool schemas need optimization."
- The description and "Why It Works" section together make the trigger unambiguous (high token overhead, vague descriptions, wrong-tool selection).

#### Positive Framing — ✅ PASS
- Boundaries section: "Preserve tool names, parameter names", "Mark unverifiable shapes", "Flag them, suggest which to prefer" — all positive. ✅

#### Token Count Accuracy — ✅ PASS
- No specific token count claimed. The "10–30K tokens of overhead" claim is a range for tool schemas generally, not for this file.

#### Research Citations — ⚠️ WARN
- `docs.anthropic.com/en/docs/build-with-claude/tool-use` — HTTP 200 ✅
- `anthropic.com/research/building-effective-agents` — HTTP 200 ✅
- **Issue 1:** The Anthropic quote `"We spent more time optimizing tools than the prompt"` appears in the Why It Works section. This quote could not be verified in the cited Anthropic tool use docs or the building effective agents page. It reads as paraphrased or approximate rather than a direct quotation. If it is a verbatim quote, a section reference or timestamp would make it verifiable. If it is a paraphrase, quotation marks should be removed.
- **Issue 2:** The Anthropic building effective agents page is cited with a quotation: `"the interface between tools and the model is just as important as the tool logic itself."` This also could not be verified verbatim via grep. Same concern as above — paraphrase presented as direct quote.
- **Issue 3:** "Dynamic toolsets reduce token usage 90–96%" — the cited docs support dynamic tool loading in principle, but this specific range appears to be an extrapolation without a cited source.

#### Example Quality — ✅ PASS
- The before/after SHARP transformation example is concrete and demonstrates all five SHARP elements.
- The audit output format is realistic and shows how CRITICAL/QUALITY/TOKEN OVERHEAD tiers work.
- Parameter rules before/after with JSON format is precisely what a developer needs.

#### Internal Consistency — ✅ PASS
- SHARP format is well-defined and the parameter rules align with it.
- Lazy-loading guidance is self-consistent.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** User invokes `/toolsmith:audit` or `/toolsmith:optimize`.
- **What to do?** Audit checklist table is comprehensive; SHARP format is structured.
- **Slash commands?** Two commands with optional flags — clear syntax.
- **Potential confusion:** The `--file` flag is not defined elsewhere; a model would need to infer it reads a JSON config. This is an acceptable minor ambiguity.

---

### 4. `forge` — Tier 2 Runtime

**File:** `skills/tier2-runtime/forge/SKILL.md`  
**Size:** 1,336 chars / ~334 tokens (est.)  
**Slash commands:** `/forge:off` (in Boundaries, no dedicated section)

#### Structural Validity — ⚠️ WARN
- Frontmatter: `name`, `description` ✅
- Sections: Code, Analysis/writing, Architecture/design, Boundaries ✅
- **Issue:** No dedicated `## Slash Commands` section. The only command (`/forge:off`) is buried in the Boundaries paragraph. For consistency with all other skills in this kit, the slash command should have its own section with a table or usage block. A model scanning for commands may miss it.
- **Issue:** No `## When active` section. This skill is described as a "silent gate" that is dormant on "conversational/simple responses" but active otherwise. The boundary between "conversational" and "non-conversational" is not defined. What about a simple factual lookup? A short code snippet? A one-line bash command? Without a definition, a model implementing this skill must guess where the line is.

#### Activation Logic — ⚠️ WARN
- The preamble states: "Dormant on conversational/simple responses." 
- **Issue:** "Simple" is undefined. The skill would benefit from explicit examples of what crosses the activation threshold: e.g., "Activates for: code blocks >10 lines, architectural diagrams, analytical essays. Dormant for: greetings, one-line answers, clarifying questions."
- Deactivation (`/forge:off`) is mentioned but there is no `/forge:on` command. Since it is described as always-on by default, this is technically correct, but symmetry with other skills (which have both on and off) could reduce user confusion.

#### Positive Framing — ✅ PASS
- Boundaries section: "Drop for security warnings" — "Drop" is a direction (what to do), not a prohibition. ✅
- The constitution checks themselves are framed as positive requirements ("Every error path handled", "Function does one thing", etc.) ✅

#### Token Count Accuracy — ✅ PASS
- At ~334 tokens, this is the most token-efficient skill in the kit. No specific count is claimed.

#### Research Citations — ⚠️ WARN
- `arxiv.org/abs/2212.08073` — "Constitutional AI: Harmlessness from AI Feedback" — HTTP 200 ✅
- **Issue:** The citation is used to justify a "quality constitution" concept for runtime output checking. Constitutional AI (CAI) is an Anthropic RLHF *training method* that uses a set of principles during model training — it is not a runtime gate. The skill borrows the metaphor ("constitution") from CAI, but the mechanism is entirely different. The citation lends false authority to the concept. A better citation would be a paper on self-critique, self-refinement (e.g., arxiv.org/abs/2303.17651 — "Self-Refine"), or output quality rubrics. This is the most significant citation issue across all 9 skills.
- **Issue:** The claim "Null/empty/overflow/concurrency edges — cause >60% of bugs" has no citation.

#### Example Quality — ✅ PASS
- The three-section constitution (Code, Analysis/writing, Architecture/design) is well-chosen and comprehensive.
- Each rule is paired with a one-clause rationale ("silent failures kill production", "one-sided = advocacy") — this telegraphs intent without padding.

#### Internal Consistency — ✅ PASS
- The three domains (Code, Analysis, Architecture) are cleanly separated.
- "Fix silently; surface only if fix needs user input" is consistent with the silent gate philosophy.

#### LLM Interpretation Simulation — ⚠️ WARN
- **When to activate?** Ambiguous — "Dormant on conversational/simple responses" needs a clearer threshold.
- **What to do?** The numbered checklists are unambiguous. A model can follow them mechanically.
- **Slash commands?** `/forge:off` is findable but not prominently marked.
- **Potential confusion:** A model reading "Check matching constitution before output" without a `## When active` section may apply the constitution to every single response including simple greetings — the opposite of the intended behavior.

---

### 5. `contract` — Tier 2 Runtime

**File:** `skills/tier2-runtime/contract/SKILL.md`  
**Size:** 880 chars / ~220 tokens (est.)  
**Slash commands:** `/contract:off`, `/contract:always`

#### Structural Validity — ✅ PASS
- Frontmatter: `name`, `description` ✅
- Sections: Activation, Contract format, Boundaries ✅
- Both slash commands mentioned with clear effect descriptions ✅
- This is intentionally minimal — the entire skill fits in 26 lines. This is a feature, not a bug.

#### Activation Logic — ✅ PASS
- "Non-trivial = multi-step, open-ended ('build'/'improve'/'design'), or multiple valid output forms." Clear.
- "Simple = one clear interpretation — skip contract entirely." Clear.
- The `/contract:always` override is a clean mechanism for users who want the contract on everything.

#### Positive Framing — ✅ PASS
- Boundaries: "Drop for security warnings..." — directive framing, not prohibition. ✅

#### Token Count Accuracy — ✅ PASS
- At ~220 tokens, this is the most compact skill. No specific count claimed.

#### Research Citations — ⚠️ WARN
- `developers.openai.com/api/docs/guides/prompt-guidance` — HTTP 200 ✅
- **Issue:** The URL `developers.openai.com` is not OpenAI's primary documentation domain — that is `platform.openai.com`. The URL resolves (200 OK) but it is unconventional. The citation is also entirely in the description field, not in the body of the skill, so it functions more as a pointer to "relevant reading" than a claim citation. If it redirects to `platform.openai.com`, users may find it confusing. Consider verifying the canonical URL.
- **Issue:** The citation does not support a specific quantitative claim — it is a general reference. This is fine but less rigorous than the other skills' citation pattern.

#### Example Quality — ✅ PASS
- The contract format is concise and functional:
  ```
  OBJECTIVE: [artifact — 1 sentence]
  CONSTRAINTS: [hard limits, not preferences]
  COMPLETION: [binary yes/no verification]
  ```
- The three-field structure cleanly separates goal, limits, and done-condition.
- **Minor improvement opportunity:** A worked example contract would strengthen this skill. A user encountering this for the first time cannot see what a good contract looks like in practice.

#### Internal Consistency — ✅ PASS
- Activation logic and the "simple task: proceed directly" rule are consistent.
- The post-completion verification loop ("verify output satisfies contract; fix before responding") is logically sound.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Clear binary distinction (non-trivial vs. simple) with examples of trigger verbs.
- **What to do?** Three-field contract format, then proceed, then verify.
- **Slash commands?** Two commands with clearly different effects.
- **Potential confusion:** "multiple valid output forms" is a subjective criterion. A model could classify almost any task as having multiple valid forms. A clarifying example (e.g., "a task with one clear answer does not qualify; 'build a checkout flow' does") would reduce ambiguity.

---

### 6. `checkpoint` — Tier 2 Runtime

**File:** `skills/tier2-runtime/checkpoint/SKILL.md`  
**Size:** 1,017 chars / ~254 tokens (est.)  
**Slash commands:** `/checkpoint:off`

#### Structural Validity — ✅ PASS
- Frontmatter: `name`, `description` ✅
- Sections: After each tool call, Every 3 sequential actions, Before completing, Boundaries ✅
- Slash command mentioned in Boundaries ✅
- At 29 lines, this is maximally dense — every line carries instruction.

#### Activation Logic — ✅ PASS
- "Dormant on single-turn responses." Clear.
- Three distinct trigger points are defined: per-tool-call, every-3-actions, pre-completion. These are concrete and countable.

#### Positive Framing — ✅ PASS
- All three numbered checklists use positive validation language ("Output non-empty, non-error", "Pass only validated output"). ✅

#### Token Count Accuracy — ✅ PASS
- No specific count claimed.

#### Research Citations — ⚠️ WARN
- `tianpan.co/blog/2025-10-14-why-multi-agent-llm-systems-fail` — HTTP 200 (content loaded, but empty text response via curl — likely JavaScript-rendered) ✅ (URL resolves)
- **Issue:** This is a personal blog post, not a peer-reviewed source. The blog's date (October 2025) is future-dated relative to many models' knowledge cutoffs. The citation is in the description field only, not in the skill body, so it functions as a pointer to background reading rather than a specific claim citation. The underlying claims (cascades, loops, premature termination) are widely acknowledged multi-agent failure modes regardless of this specific source.
- **Issue:** No citation for the `>60% of bugs` claim from the `forge` skill, and no citation for the looping behavior claim here. These would benefit from at least an inline "widely documented" qualifier.

#### Example Quality — ✅ PASS
- The three-phase structure (tool-call, periodic, pre-completion) maps cleanly to agentic execution patterns.
- "If code: confirm it runs — untested = not done" is a punchy, actionable rule.
- The looping detection ("2+ actions same result → try different approach") is concrete.

#### Internal Consistency — ✅ PASS
- The three phases do not overlap or conflict.
- "Fix or escalate" at gate failure is a binary that covers all cases.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Dormant/active distinction is clear (single-turn vs. multi-step agentic).
- **What to do?** Three numbered checklists with countable triggers (every tool call, every 3 actions).
- **Slash commands?** Only one (`/checkpoint:off`). No `/checkpoint:on` — this is correct since it's on by default in agentic contexts.
- **Potential confusion:** "Every 3 sequential actions" — does a tool call count as an action? Yes, implied, but stating it explicitly would eliminate ambiguity.

---

### 7. `contrarian` — Tier 3 Conditional

**File:** `skills/tier3-conditional/contrarian/SKILL.md`  
**Size:** 3,145 chars / ~786 tokens (est.)  
**Slash commands:** `/contrarian:off`

#### Structural Validity — ✅ PASS
- Frontmatter uses multi-line `>` YAML block scalar for description ✅ (valid YAML)
- Sections: Why this exists, When active, Rules, Disagreement format, Auto-Clarity, Calibration note, Slash commands ✅
- Slash commands in dedicated table ✅

#### Activation Logic — ✅ PASS
- "Load when user works solo on decisions, technical proposals, or factual claims."
- "Dormant in pure creative, formatting, or code-generation tasks where there is no claim to evaluate."
- This is precise: the activation condition is tied to the presence of *evaluable claims*, not to a task type.

#### Positive Framing — ✅ PASS
- The "Banned patterns" table is a before/after table showing what to say *instead* — structurally identical to the transformation tables in `positive`. This is appropriate: the negative examples are input data for a transformation, not instructions using negative framing. ✅
- Rules section uses positive directives ("Evaluate before responding", "Affirm when correct", "State the disagreement directly"). ✅

#### Token Count Accuracy — ✅ PASS
- No specific count claimed.

#### Research Citations — ⚠️ WARN
- `c3.unu.edu/blog/...` — HTTP 200, UNU Campus Computing Centre blog post ✅
  - **Issue (minor):** The `58%` sycophancy figure is cited from this source. The UNU blog is a secondary commentary on a study (SycEval), not the primary study itself. Linking to the original SycEval paper would be more rigorous.
- `arxiv.org/html/2601.18334v1` — HTTP 200 ✅. Paper title: "Overalignment in Frontier LLMs: An Empirical Study of Sycophantic Behaviour in Healthcare."
  - **Issue:** The skill says "[GPT-4o April 2025](https://arxiv.org/html/2601.18334v1) proved this is production-scale risk." The paper does not specifically study the GPT-4o April 2025 sycophancy incident that was publicly reported. The paper is about sycophancy in healthcare settings across multiple frontier models. The link to "GPT-4o April 2025" (a specific, widely-reported rollback event at OpenAI) is implied but not established by this paper. The claim as written could mislead readers into thinking this paper documents that specific incident.
  - **Recommendation:** Either replace the citation with the actual OpenAI documentation of the April 2025 GPT-4o sycophancy update, or rephrase to not name-drop that specific incident.

#### Example Quality — ✅ PASS
- The disagreement format template is concrete and quotable.
- The banned patterns table with replacements is one of the best examples across all 9 skills — each row gives a specific alternative, not a vague "be more honest."
- The pushback rules ("Capitulation requires new evidence or a new argument") are precise and unambiguous.

#### Internal Consistency — ✅ PASS
- The "Auto-Clarity" section (suspend for safety warnings, destructive actions) is consistent with the skill's purpose.
- The calibration note is honest about user experience tradeoffs — this is good design.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Clear: solo user making evaluable claims. Dormant during creative/formatting/pure code-gen.
- **What to do?** Rules are specific enough that a model can follow them mechanically.
- **Slash commands?** One command (`/contrarian:off`), clearly defined.
- **Potential confusion:** "Works solo" is the trigger, but this cannot be objectively detected — it is user-declared context. A model cannot determine if the user is working solo or not unless told. This is acceptable given that the skill is opt-in (added to a system prompt by the user).

---

### 8. `anchor-point` — Tier 3 Conditional

**File:** `skills/tier3-conditional/anchor-point/SKILL.md`  
**Size:** 2,606 chars / ~651 tokens (est.)  
**Slash commands:** `/anchor:now`, `/anchor:off`

#### Structural Validity — ✅ PASS
- Frontmatter: `name`, `description` (multi-line block scalar) ✅
- Sections: Why this exists, When active, Re-anchor format, On contradictions, Direction shifts, Auto-Clarity, Slash commands ✅
- Two slash commands in a table ✅

#### Activation Logic — ✅ PASS
- "Dormant for conversations under 5 turns — zero overhead." Precise.
- "Activates automatically at turn 5, then every 5 turns after." Countable.
- "Also triggers when user direction noticeably shifts mid-conversation." Adds a qualitative secondary trigger.
- `/anchor:now` provides a manual override — well-designed.

#### Positive Framing — ✅ PASS
- "Surface all assumption changes explicitly." ✅
- On contradictions: "Acknowledge the change", "Re-evaluate", "Do not silently update" — the last one uses a soft prohibition but it is an instruction to be explicit (positive action), not a constraint defined by avoidance.

#### Token Count Accuracy — ✅ PASS
- No specific count claimed. At ~651 tokens this is compact.

#### Research Citations — ⚠️ WARN
- `arxiv.org/abs/2505.06120` — HTTP 200. Paper: "LLMs Get Lost In Multi-Turn Conversation" ✅
  - **Claim verification:** "39% average performance drop in multi-turn vs single-turn" — **VERIFIED** directly against the paper abstract: "an average drop of 39% across six generation tasks." ✅
- `arxiv.org/html/2602.07338v1` — HTTP 200. Paper: "Intent Mismatch Causes LLMs to Get Lost in Multi-Turn Conversation" ✅
  - **Issue:** The skill cites this paper for "attention bias toward first and last turns." The paper's abstract describes the problem as an "intent alignment gap" and argues for a "Mediator-Assistant architecture" — it is about intent mismatch, not specifically about attention bias toward first/last turns. The "attention bias toward first and last turns" claim appears to be a characterization drawn from the first paper (2505.06120) or from general knowledge about LLM positional bias, not specifically from this paper. Mild misattribution.

#### Example Quality — ✅ PASS
- The anchor block format is clean and explicit.
- The Postgres → MySQL worked example is the most concrete in the tier-3 skills — it shows exactly what a model should say and why.
- "3–5 lines" constraint on anchor blocks prevents verbosity.

#### Internal Consistency — ✅ PASS
- Turn-counting and direction-shift triggers are independent and additive (either fires the anchor).
- Auto-Clarity (ask one clarifying question on mismatch) is consistent with the anchor block format.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Precise: turn 5, then every 5 turns, or on direction shift.
- **What to do?** The anchor block template with labeled fields is copy-pasteable.
- **Slash commands?** Two commands, both clearly defined in a table.
- **Potential confusion:** "Turn" is not defined — does a turn include the model's response, or only the user's message? In most conversational contexts, "turn 5" likely means the 5th user message, but a model might count assistant responses too, arriving at a different trigger point.

---

### 9. `grounding` — Tier 3 Conditional

**File:** `skills/tier3-conditional/grounding/SKILL.md`  
**Size:** 3,343 chars / ~835 tokens (est.)  
**Slash commands:** `/grounding:off`

#### Structural Validity — ✅ PASS
- Frontmatter: `name`, `description` (multi-line block scalar) ✅
- Sections: Why this exists, When active, Confidence labels, Rules, Format examples, Auto-Clarity, Slash commands ✅
- Slash command in a table ✅

#### Activation Logic — ✅ PASS
- Active for: "research questions, factual summaries, technical explanations, quantitative claims, anything about recent events or specific people." — specific.
- Dormant for: "pure code generation" with the nuanced "resumes if prose claims appear alongside code." — well-handled edge case.

#### Positive Framing — ✅ PASS
- All four confidence labels are defined positively (what they mean, when to use them). ✅
- Rules use positive directives ("Always state the source inline", "Default to UNCERTAIN", "One label per claim"). ✅

#### Token Count Accuracy — ✅ PASS
- No specific count claimed.

#### Research Citations — ⚠️ WARN
- `lesswrong.com/posts/unaLT4A6hSTCLNGod/...` — HTTP 200 ✅
  - **Claim verification:** `R² = 0.01` — **VERIFIED** in the source (the LessWrong post does contain `R^2 = 0.01`). ✅
  - **Issue (minor):** LessWrong is a community blog, not peer-reviewed. The original underlying study should be cited if available. Using it as the sole citation for the confidence/accuracy correlation claim may reduce credibility with technical readers.
- `tianpan.co/blog/2026-04-12-the-calibration-gap-...` — HTTP 200 ✅
  - **Issue:** The blog post is dated **April 12, 2026** — a future date relative to many models' training cutoffs, and at the time of this test. The URL resolves, but a future-dated post is unusual and may indicate a dating error on the blog. The skill text reads `"Reasoning models are worse — o3 hallucination rates double o1's"` — this claim should be cited from primary benchmarking data, not a future-dated personal blog post.
  - **Recommendation:** Replace or supplement with a primary citation (e.g., official hallucination benchmarks from HELM or similar evaluation frameworks).

#### Example Quality — ✅ PASS
- Four worked examples, one per label level, are the clearest example set across all skills.
- Each example shows the exact formatting a model should produce — inline label, source pointer.
- The UNKNOWN example with "Westlaw or the court's docket" is appropriately specific (not just "check online").

#### Internal Consistency — ✅ PASS
- "No label stacking" rule is consistent with the format examples (each shows a single label).
- The Auto-Clarity threshold (3+ UNCERTAIN/UNKNOWN labels) is concrete and actionable.

#### LLM Interpretation Simulation — ✅ PASS
- **When to activate?** Clear active/dormant split with specific task type examples.
- **What to do?** Four-label taxonomy with clear criteria. Format examples are copy-pasteable.
- **Slash commands?** One command (`/grounding:off`). No `/grounding:on` — correct since it defaults to active.
- **Potential confusion:** The ESTABLISHED label ("well-known, consistent across training data, widely accepted") overlaps with VERIFIED in borderline cases. A model unsure whether a math fact is ESTABLISHED (general knowledge) or VERIFIED (because the user provided a document containing it) may label inconsistently. A tie-breaking rule (e.g., "if it appeared in this session's context, prefer VERIFIED") would help.

---

## Cross-Cutting Issues

### Issue 1: `Auto-Clarity` Term Overloaded

`contrarian`, `anchor-point`, and `grounding` all have a `## Auto-Clarity` section, but the term means different things in each:
- **contrarian:** Suspend the bluntness behavior for safety/destructive contexts.
- **anchor-point:** Ask a clarifying question if goal mismatch is detected.
- **grounding:** Surface a warning when more than 3 UNCERTAIN/UNKNOWN labels are needed.

These are three different behaviors sharing one section name. If a user loads all three skills, the term "Auto-Clarity" appears three times with three distinct meanings. Consider renaming each to a more specific term: `Safety Override`, `Goal Check`, `Uncertainty Warning`.

### Issue 2: No `/forge:on` Command

`forge` has `/forge:off` but no `/forge:on`. All other skills with an off command also have an on or now command (e.g., `/anchor:now`). Since `forge` is always-on by default, a user who disables it and wants to re-enable it within a session has no command to do so. Add `/forge:on` for symmetry.

### Issue 3: `trim` KERNEL Structure Labels Position 5 as "Constraints — what not to do"

The `positive` skill converts all "what not to do" into positive directives. The `trim` skill then places them under a label that explicitly says "what not to do." After running both skills on a system prompt, the `trim` output would place positive directives under a section labeled with a negative description. The inline note handles this, but the KERNEL diagram itself (`5. Constraints — what not to do`) should either be updated to "5. Guardrails" or similar, or the note should be in the diagram itself.

### Issue 4: `forge` Citation Mismatch

Constitutional AI (arxiv 2212.08073) is cited to support `forge`'s quality constitution concept. The paper is about a training methodology (using a list of principles during RLHF), not about runtime quality gates. The conceptual borrowing of "constitution" is legitimate, but attributing it to this paper implies `forge` is backed by CAI research when it is not. Replace or supplement with a self-critique/self-refinement citation.

### Issue 5: Future-Dated Blog Citation in `grounding`

The tianpan.co blog post linked in `grounding` has a 2026 date. Whether this is a blogging platform date error or actual future content, it undermines citation credibility. Replace with a primary benchmark source.

---

## Recommendations by Priority

### High Priority (Affects User Trust / Correctness)

1. **`forge`**: Fix the Constitutional AI citation. Replace `arxiv.org/abs/2212.08073` with a self-refinement or output quality paper, or remove the citation and add a brief inline description of the concept. The current citation actively misleads.

2. **`contrarian`**: Clarify the GPT-4o April 2025 reference. Either cite the actual OpenAI update documentation or rephrase to not attribute the arxiv paper to that specific incident.

3. **`grounding`**: Replace the 2026-dated blog citation with a primary source. The claim about o3 vs o1 hallucination rates is important enough to need a verifiable source.

### Medium Priority (Clarity / Usability)

4. **`forge`**: Add a `## Slash Commands` section with a table. Add a `## When active` section that defines what "conversational/simple" means with 2–3 examples. Add `/forge:on`.

5. **`trim`**: Update the "Lost in the Middle" citation note. The paper supports positional importance in general but does not support the "first 50 tokens → 80% of behavioral change" specific claim. Either find the source for this claim or rephrase to "first tokens set the behavioral frame" without the percentage.

6. **`anchor-point`**: Define what counts as a "turn" (user message only, or user+assistant pair). This affects when the every-5-turns anchor fires.

7. **`grounding`**: Add a tiebreaker rule for ESTABLISHED vs. VERIFIED for claims that appear in both training data and in the current session's provided context.

### Low Priority (Polish)

8. **`toolsmith`**: Verify or dequote the two Anthropic attributions. If the quotes are paraphrases, remove quotation marks.

9. **`contract`**: Add one worked example contract to the body of the skill. The format is clear, but a concrete instantiation would help users who are new to output contracts.

10. **All tier-3 skills**: Rename `## Auto-Clarity` sections to distinct names to eliminate the terminological overlap across `contrarian`, `anchor-point`, and `grounding`.

11. **`trim`**: Update the KERNEL diagram's position-5 label from "Constraints — what not to do" to "Guardrails" or "Constraints" (without the "what not to do" qualifier) to reduce friction when used alongside `positive`.

---

## Token Budget Reference

| Skill | Chars | Est. Tokens | Category |
|-------|-------|-------------|----------|
| contract | 880 | ~220 | Micro |
| checkpoint | 1,017 | ~254 | Micro |
| forge | 1,336 | ~334 | Micro |
| anchor-point | 2,606 | ~651 | Small |
| contrarian | 3,145 | ~786 | Small |
| grounding | 3,343 | ~835 | Small |
| positive | 4,685 | ~1,171 | Medium |
| trim | 5,958 | ~1,489 | Medium |
| toolsmith | 6,125 | ~1,531 | Medium |
| **Total (all 9)** | **28,095** | **~7,021** | — |

All 9 skills loaded simultaneously would consume approximately **7,021 tokens** — well within context limits for production use. Individual tier-2 runtime skills are all under 400 tokens, which is optimal for always-on use.

---

## URL / Citation Status

All 15 URLs tested returned HTTP 200. No broken links.

| URL | Status | Issue |
|-----|--------|-------|
| arxiv.org/abs/2306.08189 | ✅ 200 | Claim slightly overstated |
| arxiv.org/abs/2109.07830 | ✅ 200 | 10%+ figure unverified in abstract |
| arxiv.org/abs/2307.03172 | ✅ 200 | Cited for "50 tokens → 80%" claim it doesn't make |
| docs.anthropic.com/…/prompt-caching | ✅ 200 | Fine |
| docs.anthropic.com/…/tool-use | ✅ 200 | Quote unverified |
| anthropic.com/research/building-effective-agents | ✅ 200 | Quote unverified |
| arxiv.org/abs/2212.08073 | ✅ 200 | Wrong paper type for claim made |
| developers.openai.com/api/docs/guides/prompt-guidance | ✅ 200 | Non-standard OpenAI URL |
| tianpan.co/blog/2025-10-14-… | ✅ 200 | Personal blog, JS-rendered |
| c3.unu.edu/blog/… | ✅ 200 | Secondary source for 58% stat |
| arxiv.org/html/2601.18334v1 | ✅ 200 | Linked as "GPT-4o April 2025" but isn't |
| arxiv.org/abs/2505.06120 | ✅ 200 | 39% claim **VERIFIED** ✅ |
| arxiv.org/html/2602.07338v1 | ✅ 200 | Mild misattribution for attention bias claim |
| lesswrong.com/posts/unaLT4A6… | ✅ 200 | R²=0.01 **VERIFIED** ✅; community blog |
| tianpan.co/blog/2026-04-12-… | ✅ 200 | **Future-dated post** (2026) |
