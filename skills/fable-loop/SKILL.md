---
name: fable-loop
description: Turn one request into an autonomous, self-scoring improvement loop. The skill senses the domain the user is working in, proposes the top ~10 evals that matter for that domain, asks only for a max iteration count and a target score, then repeatedly builds -> scores -> diagnoses -> refines on its own until the score converges or the cap is hit, and returns a final scorecard. Use when the user invokes /fable-loop, or wants Claude to iterate to a quality bar without hand-holding, define and run its own evals, keep improving an output until it stops getting better, or get a scored, self-critiqued deliverable instead of a one-shot answer. Replaces the manual "now score it / now improve it / now again" prompt chain.
argument-hint: "[rough goal | domain-scan | evals | run | rescore | report]"
disable-model-invocation: true
---

# Fable Loop Skill

Use this skill when the user invokes `/fable-loop`. It converts a single request into a **self-scoring convergence loop** so the user does not have to hand-drive iteration with repeated prompts.

The core operating principle: the prompt is only the map; the real work is the territory — and you do not get to declare the territory conquered on your own say-so. So this skill (1) figures out what "good" means *in the user's actual domain*, (2) turns that into a fixed set of scored evals, and (3) iterates against those evals autonomously, showing its scores each pass, until the score stops climbing. One invocation does the work of a whole prompt chain.

## The one-shot promise

After a single setup exchange, the skill runs the entire loop itself:

```
sense domain -> propose top ~10 evals -> user sets iterations + target
             -> [ build -> score -> diagnose -> refine ] x N   (autonomous)
             -> final scorecard
```

The only thing the skill asks the user for mid-flight is a decision it is not allowed to make alone: a high-risk irreversible choice, or an eval it genuinely cannot score without them.

## Invocation behavior

- `/fable-loop <goal>` — run the full autonomous flow on the goal.
- `/fable-loop` — ask once for the goal, then run the full flow.
- `/fable-loop domain-scan` — do only the domain sensing and stop.
- `/fable-loop evals` — propose (or re-propose) the top-10 eval set for the current work and stop.
- `/fable-loop run` — start/continue the scored loop against an approved eval set.
- `/fable-loop rescore` — re-score the current artifact against the frozen eval set without changing it.
- `/fable-loop report` — emit the final scorecard for the current work.

If `$ARGUMENTS` is empty or vague, ask one question to get the goal, then proceed.

## Non-negotiable behavior — loop integrity

A self-scoring loop is worthless if the scorer inflates its own grades. These rules are what make the score mean something; treat them as load-bearing, not decoration. The reasoning behind each is in `reference/eval-and-loop.md` (read it before running a non-trivial loop).

1. **Freeze the rubric before the loop.** The eval set, weights, and scales are fixed at setup. You may not edit an eval to make failing work pass. If an eval is genuinely broken, stop, tell the user, and fix it in the open — a correction never counts as a score gain.
2. **Prefer verifiable scorers; label the rest.** Whenever a property can be checked by running something (tests, build, lint, a metric, a script), run it and use the real result — do not estimate. Mark every eval as **[V] verifiable** or **[S] self-judged**. The final scorecard must state how many points came from each, so the user knows how much to trust the number.
3. **Score to lose points, not to win them.** Each iteration, evaluate adversarially: actively look for reasons to deduct. Every score must carry a one-line piece of evidence. A total may only rise when a concrete change justifies it.
4. **Never fake monotonic improvement.** A plateau is an honest, reportable result. Do not nudge scores upward to manufacture progress or to reach the target. If the work converges below target, say so and explain why.
5. **Show every pass.** Print the per-eval scores, the weighted total, and the delta from the previous iteration on every iteration. A loop the user cannot see is a loop they cannot trust or halt.
6. **Diagnose before refining.** Before each refinement, write a one-line root-cause hypothesis for the lowest-scoring evals. Blind tweaking is the main way loops waste iterations.
7. **Respect the budget and the stop rules.** Stop at target, at the iteration cap, on a 2-iteration plateau, or on a high-risk unknown. Do not loop past the cap without asking.
8. **Conservative on unknowns.** If an unknown appears mid-loop, take the reversible low-risk option and log it; if it is irreversible or high-risk, stop and ask.

## Phase 1 — Sense the domain

From the goal plus any files, links, or context provided, infer where the user is working and what they are producing. Use the taxonomy and derivation method in `reference/domain-evals.md`. Output a short frame:

```md
## Domain scan
Goal (restated): ...
Domain: [e.g. backend code / ML training / prompt-or-agent design / technical writing / data analysis / frontend-UI / research report / business doc / sales outreach / marketing content / lead generation / SEO / ...]
Artifact type: [what will actually be produced]
What "good" means here: [2–4 axes that matter most in this domain]
Assumptions I'm making: [so the user can correct them]
```

If the domain or artifact is genuinely ambiguous in a way that changes the evals, ask exactly one clarifying question. Otherwise proceed — the eval-approval step is the user's chance to correct you, so do not stall here.

## Phase 2 — Propose the top ~10 evals

Generate the ~10 highest-signal evals for this domain and artifact. Rank by how much each one discriminates good work from bad. Present them as an editable table:

```md
## Proposed evals (frozen once you approve)
| # | Eval | What it measures | Scorer | V/S | Weight | Scale |
|---|------|------------------|--------|-----|--------|-------|
| 1 | ...  | ...              | test/metric/lint/judge/manual | V or S | 15 | 0–5 |
| ... (up to ~10, weights sum to 100) |
Target score default: converge (stop when it stops improving). Verifiable coverage: X of 10 are [V].
```

Bias the set toward **[V] verifiable** evals — they are the ones that make the final score defensible. Include the failure modes and "must-not-break" properties the domain implies. See `reference/domain-evals.md` for worked example banks (code, ML/prompt-agent, technical writing, data analysis) and how to adapt them.

## Phase 3 — Setup gate (the only required input)

Ask the user, in one message, to confirm three things:

1. **Evals** — keep / drop / edit / reweight any of the proposed 10.
2. **Max iterations** — the loop's hard cap (default 3–5).
3. **Target score** — stop-early threshold on the 0–100 weighted scale, or "converge" (default: stop on plateau).

Once they answer, restate the frozen eval set + budget, then start the loop. This is the last routine prompt the user needs to send.

## Phase 4 — The scored convergence loop (autonomous)

Track iterations with `templates/scorecard.md`. Announce the budget, then loop. Each iteration:

1. **Build or refine** — iteration 1 produces the first real artifact; later iterations make the smallest change that targets the lowest-scoring evals from last pass.
2. **Score against the frozen set** — run every [V] eval for real (execute tests/metrics/lint/scripts); apply the written rubric for every [S] eval, adversarially. Attach one-line evidence to each score. Compute the weighted total (0–100). Formula and normalization are in `reference/eval-and-loop.md`.
3. **Emit the iteration scorecard:**

```md
### Iteration i/N — total: 78/100 (Δ +12)  | verified: 55/70 pts | self-judged: 23/30 pts
| # | Eval | Score | V/S | Evidence |
|---|------|-------|-----|----------|
| 1 | ...  | 4/5   | V   | 41/41 tests pass |
| ... |
Lowest evals: #4 (2/5), #7 (2/5). Hypothesis: ...
Decision: continue / stop-target / stop-plateau / stop-cap / stop-risk
```

4. **Decide via the stop rules:** target reached, iteration cap hit, plateau (weighted total improves by less than a small epsilon for 2 consecutive iterations), or a high-risk unknown. On plateau or cap without target, stop honestly — do not inflate to close the gap.

Never rewrite the rubric to move the number. If verifiable evals are maxed and only self-judged ones remain soft, say that explicitly rather than grinding subjective scores upward.

## Phase 5 — Final scorecard and report

When the loop exits, deliver the artifact plus a report built from `templates/scorecard.md` and `templates/explainer-and-quiz.md`:

```md
## Final scorecard
Best iteration: i (total S/100). Iterations run: N of M. Stop reason: ...

Trajectory: it1 54 -> it2 66 -> it3 78 -> it4 79 (plateau)

| # | Eval | Final | V/S | Evidence |
|---|------|-------|-----|----------|

Trust note: G of 100 points are externally verified [V]; the remaining H are self-assessed [S] — weight them accordingly.
Residual weaknesses: ...
What would raise the score further (out of budget): ...
```

Close with a 3–5 question quiz that tests real behavior and tradeoffs, including one question about which eval would catch a regression. Then offer the outer loop: fold new findings into the frozen set (every bug becomes a permanent regression eval) and run another cycle. The eval set only grows across cycles.

## Style

Keep each pass legible and terse: iteration number, change, score, delta, decision. Lead the setup gate with the eval table so the user can approve in one glance. Be honest about the score's limits — a defensible 82 beats a fabricated 100.
