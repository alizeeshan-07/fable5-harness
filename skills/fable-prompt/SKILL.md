---
name: fable-prompt
description: "Run an unknowns-first Fable workflow: blind-spot pass, brainstorm/prototype, interview, plan, implement with notes, explain, and quiz — now with iterative looping and built-in evals for scoring and gating output quality."
argument-hint: "[rough goal | discover | brainstorm | prototype | plan | implement | review | quiz | loop | eval]"
disable-model-invocation: true
---

# Fable Prompt Skill

Use this skill when the user invokes `/fable-prompt` to turn a rough idea into a clarified plan and, when appropriate, an implementation.

The core operating principle: the prompt is only the map; the real work is the territory. Your job is to reduce the gap between the user's map and the real territory by discovering known knowns, known unknowns, unknown knowns, and unknown unknowns before, during, and after implementation.

Two capabilities extend the base workflow:

- **Looping** turns any phase — or the whole workflow — into a repeatable improvement cycle instead of a single pass.
- **Evals** attach explicit, scored acceptance criteria to candidates and outputs so iteration is driven by measured quality, not vibes.

Looping and evals are strongest together: generate candidates, score them with an eval, keep the best, and loop until the score clears a gate or the iteration budget runs out.

## Invocation behavior

The user may invoke this as:

- `/fable-prompt` — start from scratch and interview the user step by step.
- `/fable-prompt <rough task>` — use the task as the initial known known and begin the workflow.
- `/fable-prompt discover` — run intake, blind-spot pass, unknowns board, and interview only.
- `/fable-prompt brainstorm` — run intake, blind-spot pass, and solution brainstorming.
- `/fable-prompt prototype` — run intake, blind-spot pass, and create quick prototypes or mockups before implementation.
- `/fable-prompt plan` — produce or refine an implementation plan from the current context.
- `/fable-prompt implement` — implement an approved plan, or ask for the missing plan/approval.
- `/fable-prompt review` — produce an explainer, risk review, and quiz after implementation.
- `/fable-prompt loop [phase]` — run the named phase (or the current one) as an iterative loop. See **Looping modes**.
- `/fable-prompt eval [target]` — define criteria and score a target (a plan, prototype, candidate set, or implemented output) against them. See **Evals**.

If `$ARGUMENTS` is empty, vague, or simply `prompt`, start with the Discovery Interview. Treat `prompt` as an alias for the default `/fable-prompt` workflow, not as a separate command name.

## Non-negotiable behavior

1. Do not dump all questions at once.
2. Ask one high-leverage question at a time, then wait for the user's answer.
3. Prioritize questions where the answer would change architecture, user experience, data model, scope, safety, cost, or implementation strategy.
4. Skip questions that are irrelevant, already answered, or unlikely to affect the plan.
5. Do not implement until there is either an approved plan or an explicit instruction to proceed without further approval.
6. If you encounter an unknown during implementation, choose the conservative option, log it, and continue only if the choice is reversible and low-risk. Otherwise, stop and ask.
7. Maintain a visible Unknowns Board during discovery and planning.
8. Prefer prototypes, references, and concrete examples whenever the user appears to have "I'll know it when I see it" preferences.
9. **Every loop has a stop condition.** Before looping, state the pass gate (target eval score or explicit user acceptance) and the maximum iteration count. Never loop indefinitely; stop at the gate, at the cap, or when scores stop improving (convergence), whichever comes first.
10. **Define eval criteria before scoring, not after.** Locking the rubric before you see results prevents grading to a foregone conclusion. Show the filled scorecard every time you score.
11. **An eval gate is a real gate.** If the output fails the threshold, either loop to improve it or surface the failure to the user with the scorecard — do not quietly ship a failing result.

## Phase 0 — Start the session

Begin by creating a compact working summary in the conversation:

```md
## Fable Session
Goal: [unknown or user-provided]
Current starting point: [unknown]
Known knowns: []
Known unknowns: []
Unknown knowns to surface: []
Unknown unknowns to investigate: []
References: []
Eval criteria: [none yet]
Loop status: [not looping]
Implementation status: Not started
```

Then ask the first missing high-leverage question. Usually start with:

> What are we trying to create or change, and what would make the result feel successful?

If the user already gave a clear task, instead ask:

> What is your current starting point: what do you already know, what are you unsure about, and how familiar are you with this codebase/domain?

## Phase 1 — Discovery Interview

Ask one question at a time. Choose from this bank based on the current context.

### Goal and success

- What are we building, fixing, or deciding?
- What does "done" look like?
- What would make this feel excellent rather than merely functional?
- Is the priority speed, quality, creativity, safety, performance, polish, or learning?

### User starting point

- What do you already know about the problem?
- What are you aware you have not figured out yet?
- Are you new to this codebase/domain, or do you already know the patterns?
- Are there parts where you want me to teach you before building?

### Territory and constraints

- Where should I look first: files, folders, docs, tickets, designs, examples, or URLs?
- What must not change or break?
- What dependencies, frameworks, conventions, or product constraints matter?
- Are there performance, security, accessibility, privacy, legal, or cost constraints?

### Unknown knowns and taste

- Is this a "you'll know it when you see it" problem?
- Would prototypes, mockups, or multiple options help before implementation?
- Do you have a reference implementation, website, component, screenshot, repo, video, or document I should study?
- What should the output feel like: premium, minimal, playful, cinematic, enterprise, fast, calm, dense, etc.?

### Implementation strategy

- Should I do a blind-spot pass across the repo/docs before proposing a plan?
- Should I brainstorm several approaches before choosing one?
- What level of autonomy do you want: ask before every major decision, or proceed with conservative choices and log deviations?
- How should we verify success: tests, screenshots, local run, demo, review checklist, metrics, or manual QA?

### Success criteria for evals

When the answer to "how should we verify success" is concrete, capture it as eval criteria immediately (see **Evals**). Good discovery answers are the raw material for a good rubric.

## Phase 2 — Blind Spot Pass

When the user approves or the task clearly requires it, perform a blind-spot pass.

For code tasks:

1. Inspect relevant files, directories, README, tests, package/config files, and existing patterns.
2. Identify hidden constraints, risky assumptions, prior art, edge cases, conventions, and likely failure modes.
3. Report findings using this structure:

```md
## Blind Spot Pass
What I inspected:
- ...

Likely unknown unknowns:
- ...

Risks and constraints:
- ...

Questions that would change the plan:
1. ...

Recommended next step:
- Brainstorm / Prototype / Plan / Ask one more question
```

For non-code tasks, inspect the provided material and domain context in the same way.

## Phase 3 — Brainstorm or Prototype

Use this phase when the solution space is unclear or the user has unknown knowns.

### Brainstorm mode

Generate 5–10 approaches from cheapest to most ambitious. For each approach include:

- What it is
- Why it might work
- Tradeoffs
- Risk level
- When to choose it

End by recommending 1–3 options and asking the user which resonates.

### Prototype mode

When the user needs to react visually or experientially:

1. Create the smallest useful prototype before touching production code.
2. Use fake data if needed.
3. Make options meaningfully different, not minor variants.
4. Ask the user which direction feels closest and what to change.

For web/product/UI work, prefer a single HTML prototype when practical.

### Generate–eval–select (looping)

When the user wants the strongest option rather than a fast pick, run brainstorm/prototype as a **generate–eval–select loop** (see **Looping modes** → *Generate–eval–select*): produce N candidates, score each with the eval rubric, keep the best, and either refine the winner or regenerate the weakest dimensions.

## Phase 4 — Implementation Plan

Before implementation, produce a plan that leads with decisions the user is likely to care about.

Use this structure:

```md
## Implementation Plan

### Goal
...

### Key decisions for you to approve
1. Data model / interfaces: ...
2. User-facing behavior: ...
3. UX flow / copy / visuals: ...
4. Edge cases: ...
5. Verification plan: ...

### Eval criteria (acceptance gate)
- Criterion → how measured → pass threshold
- ...

### Proposed steps
1. ...
2. ...
3. ...

### What I will not change
- ...

### Risks
- ...

### Approval gate
Reply `implement` to proceed, or tell me what to change.
```

Bury mechanical refactoring details near the bottom unless they carry risk. The eval-criteria block is the contract the final output will be scored against — write it even if scoring will be lightweight.

## Phase 5 — Implementation

Only enter this phase when the user explicitly approves the plan or directly instructs implementation.

Before changing files, create or update `implementation-notes.md` unless the project already has a better temporary notes location. Use the template in `templates/implementation-notes.md`.

During implementation:

1. Follow the approved plan.
2. Keep changes scoped.
3. Prefer reversible, conservative choices.
4. Log deviations under `Deviations`.
5. Log open questions under `Open Questions`.
6. Add or update tests when appropriate.
7. Run the smallest meaningful verification loop.
8. If a high-risk unknown appears, stop and ask the user before proceeding.

For iterative build-and-check work — failing tests, a metric to hit, a benchmark to beat — drive implementation with the **code/metric eval loop** (see **Looping modes** → *Full-workflow loop* and **Evals** → *Code/metric eval loop*): change, run the eval, read the score, adjust. Stop at the gate, the cap, or convergence.

## Phase 6 — Post-implementation Explainer and Quiz

After implementation, produce a reviewer-friendly report. Use the template in `templates/explainer-and-quiz.md`.

Include:

- What changed
- Why it changed
- Files touched
- Key decisions
- Deviations from the plan
- Final eval scorecard (criteria, scores, pass/fail vs. threshold)
- How to verify
- Known limitations
- Follow-up recommendations
- A short quiz to confirm the user understands the change before merging or publishing

The quiz should test the actual behavior and tradeoffs, not trivia.

## Looping modes

A loop is a repeatable pass with an explicit stop condition. Announce the mode, the pass gate, and the iteration cap before you start. Detailed procedures and worked examples live in `reference/looping-and-evals.md`.

### 1. Phase-refinement loop

Re-run one phase (brainstorm, prototype, plan, or a specific implementation slice) until the user approves or the eval clears its gate. Lowest ceremony — use when the shape is right but the details need polish.

- Stop when: user says "good," eval score ≥ threshold, or cap reached.
- Each iteration: show what changed, why, and (if scoring) the updated scorecard.

### 2. Generate–eval–select loop

Produce **N** meaningfully different candidates in parallel, score every candidate against the same rubric, then select or synthesize a winner. Use for design directions, plan alternatives, prompts, copy, or any decision with real taste/quality variance.

- Default N = 3 unless the user asks otherwise.
- After scoring, either (a) ship the winner, (b) refine the winner's weak dimensions, or (c) regenerate only the losing candidates with lessons learned.
- Stop when: a candidate clears the gate, scores converge, or cap reached.

### 3. Full-workflow loop

Treat discovery → plan → implement → review as one iteration of a larger cycle for multi-pass projects (spikes, migrations, model/agent tuning). Carry the Unknowns Board and eval criteria forward between iterations so each pass starts smarter.

- Each cycle closes by updating the Unknowns Board (what moved from unknown to known) and the eval scorecard (trend across cycles).
- Stop when: the acceptance eval passes, the budget (time/iterations/cost) is exhausted, or returns diminish.

### Iteration hygiene (all modes)

- State the stop condition out loud before iterating.
- Keep a visible tally: `Iteration k of N — score X/Y — Δ vs last`.
- On convergence (no meaningful gain for two iterations), stop and report rather than burning the cap.
- Never silently restart a loop the user paused.

## Evals

An eval is an explicit rubric plus a scoring procedure plus a pass threshold. Define criteria **before** producing or scoring output. Record them in the Fable Session summary and, when scoring, fill the scorecard in `templates/eval-scorecard.md`. Choose the lightest tier that gives a trustworthy signal.

### Tier 1 — Rubric self-scoring

3–6 weighted criteria, each scored 0–5, with a stated pass threshold (e.g., weighted ≥ 80%). No external tooling. Default tier for plans, copy, designs, and docs. Fast, subjective — mitigate bias by locking the rubric first and scoring criteria independently.

### Tier 2 — Structured eval harness

Enumerate concrete test cases or checks up front (inputs → expected properties), run the target against each, mark pass/fail, and gate on a pass rate. Use when "correct" is checkable case-by-case: API behavior, parsers, transformations, edge-case coverage, spec conformance.

### Tier 3 — LLM-as-judge

Write an explicit judge prompt that scores the output against the criteria (optionally comparing candidates pairwise). Use for prompt engineering, agent behavior, tone/voice, and open-ended generation where rules are hard to enumerate. Guard against known failure modes: position bias (randomize order), verbosity bias, and self-preference — state these mitigations in the scorecard. Prefer pairwise or reference-anchored judging over lone absolute scores when stakes are high.

### Tier 4 — Code/metric eval loop

Bind the eval to real signals: `pytest`/unit suites, a held-out set, latency/throughput, cost, task-specific metrics (accuracy, F1, reward, win-rate, tokens). Run the actual command, read the number, gate on the target. Use for code changes and for model/agent/RL work where a metric is the ground truth. Report the command, the metric, the baseline, and the delta — never the score without the command that produced it.

### Choosing a tier

- Subjective quality, no tooling → Tier 1.
- Checkable case-by-case → Tier 2.
- Open-ended, judgment-heavy → Tier 3.
- A number is the source of truth → Tier 4.

Tiers compose: a plan can pass Tier 1, its implementation gated by Tier 2 + Tier 4. When in doubt, start at Tier 1 and escalate only where the signal is untrustworthy.

## Final response style

Be concise but useful. Maintain momentum. When looping or scoring, always show the current iteration tally and the scorecard. Whenever possible, end with one clear next question or one clear next action.
