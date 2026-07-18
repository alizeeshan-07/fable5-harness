# Looping and Evals — Reference

Detailed procedures for the `loop` and `eval` capabilities of the Fable Prompt skill. Load this when the user invokes `/fable-prompt loop` or `/fable-prompt eval`, or whenever a phase should iterate against measured quality.

---

## Part A — Looping

A loop is a repeatable pass with a declared stop condition. The three modes differ in *scope*: a single phase, a candidate set, or the whole workflow.

### Universal loop protocol

1. **Declare the loop** before starting:
   - Mode (phase-refinement / generate–eval–select / full-workflow).
   - Pass gate (target eval score, or explicit user acceptance).
   - Iteration cap (default 3 unless the user sets one).
   - Convergence rule (stop after 2 iterations with no meaningful gain).
2. **Iterate.** Each pass, produce the new/updated artifact, then show:
   ```
   Iteration k/N — score X/Y (Δ +Z vs prev) — gate: ≥T
   ```
3. **Decide.** Continue, stop-at-gate, stop-at-cap, or stop-on-convergence. State which.
4. **Report on exit.** Why the loop ended, final artifact, final scorecard, and what remains open.

Never loop without a cap. Never silently resume a paused loop. Never grade to justify continuing — the rubric is fixed before iteration 1.

### Mode 1 — Phase-refinement loop

Re-run one phase until it is good enough.

Use when the direction is right and only the execution needs polish (a plan that's 80% there, a prototype with the right layout but wrong spacing, one implementation slice).

```
Loop: phase-refinement on [phase]
Gate: [eval ≥ T] or [user approves]
Cap: [n]

For each iteration:
  1. Apply the smallest change set that targets the current weakest area.
  2. (If scoring) re-score only what changed, plus any criterion it could regress.
  3. Show diff-of-intent: what changed, why, updated tally.
  4. Ask: continue, adjust target, or accept?
```

Failure mode to avoid: rewriting the whole artifact each pass. Refinement is incremental; large rewrites mean you should be in generate–eval–select instead.

### Mode 2 — Generate–eval–select loop

Produce several genuinely different candidates, score all against one rubric, select or synthesize a winner.

Use for decisions with real quality/taste variance: design directions, architecture options, prompts, naming, copy, plan alternatives.

```
Loop: generate–eval–select
N candidates: [default 3]
Rubric: [locked before generation]
Gate: [a candidate ≥ T] or [user picks]
Cap: [n rounds]

Round r:
  1. Generate N candidates that differ on a meaningful axis (not cosmetic variants).
  2. Score every candidate on the SAME rubric (see scorecard template).
  3. Rank. Present the comparison table.
  4. Choose an action:
     a. Ship winner (clears gate), OR
     b. Refine winner's weak dimensions (drop to phase-refinement), OR
     c. Regenerate only the losers with lessons from the winner, OR
     d. Synthesize a new candidate combining the best dimensions of several.
```

Guidance:
- Candidates must be *meaningfully* different. If three candidates score within noise of each other on every criterion, you generated variants, not alternatives — widen the axis.
- Synthesis (option d) is often the strongest move: take criterion-best pieces and combine.
- Keep a running best-so-far; a later round is only "better" if it beats it on the rubric.

### Mode 3 — Full-workflow loop

Treat `discover → plan → implement → review` as one iteration of a larger cycle. For multi-pass work: spikes, migrations, refactors delivered in slices, model/agent/RL tuning, research probes.

```
Loop: full-workflow
Acceptance eval: [the project-level gate]
Budget: [iterations | time | cost]

Each cycle:
  1. Run (a slice of) the standard phases.
  2. Update the Unknowns Board: what moved unknown→known this cycle.
  3. Update the acceptance scorecard: score trend across cycles.
  4. Decide: another cycle, or stop (gate met / budget spent / diminishing returns).
```

The point of carrying the Unknowns Board and scorecard forward is compounding: each cycle should start with fewer unknowns and a higher floor. If the score is flat across two cycles and unknowns aren't shrinking, stop and re-scope — more iterations won't help.

---

## Part B — Evals

An eval = rubric + scoring procedure + pass threshold. Define it before producing or scoring output. Pick the lightest tier that yields a trustworthy signal.

### Tier 1 — Rubric self-scoring

Best for: plans, copy, designs, docs, subjective quality with no tooling.

Procedure:
1. Choose 3–6 criteria that actually discriminate good from bad for this task.
2. Assign weights (sum to 100%). Weight by how much each criterion matters to success.
3. Score each 0–5 (0 absent, 3 acceptable, 5 excellent). Anchor each score with one sentence of evidence.
4. Weighted score = Σ(score/5 × weight). Gate default: ≥ 80%.

Bias control: lock criteria and weights before seeing output; score each criterion independently before computing the total; do not adjust weights after seeing the score.

### Tier 2 — Structured eval harness

Best for: things checkable case-by-case — API behavior, parsers, transforms, spec conformance, edge-case coverage.

Procedure:
1. Enumerate test cases as `input → expected property` (not necessarily exact output — properties are more robust).
2. Include edge/adversarial cases surfaced in the blind-spot pass.
3. Run the target against each case; mark pass/fail with the observed result.
4. Gate on pass rate (e.g., 100% of must-pass, ≥ 90% of should-pass).

Keep the case list as a living artifact — it doubles as a regression suite for later loops.

### Tier 3 — LLM-as-judge

Best for: open-ended generation, prompts, agent behavior, tone/voice — where rules are hard to enumerate.

Procedure:
1. Write an explicit judge prompt: the criteria, the scoring scale, and what evidence to cite.
2. Prefer **pairwise** (A vs B) or **reference-anchored** judging over lone absolute scores when stakes are high — comparisons are more reliable than absolute ratings.
3. Score. Cite evidence per criterion.

Named failure modes to mitigate and to record in the scorecard:
- **Position bias** — judges favor the first (or last) option. Randomize or swap order and average.
- **Verbosity bias** — longer answers rated higher regardless of quality. Normalize for length or instruct the judge to ignore it.
- **Self-preference** — a judge favors outputs stylistically like its own. Note it; use pairwise against a strong reference.
- **Anchoring** — the scale drifts. Provide anchored examples for 1/3/5.

An LLM-as-judge score is an estimate, not ground truth. When a cheap objective signal exists, prefer Tier 2/4.

### Tier 4 — Code/metric eval loop

Best for: code changes, and model/agent/RL work where a number is the source of truth.

Procedure:
1. Identify the metric(s): test pass rate, accuracy/F1, latency p50/p95, throughput, cost/call, reward, win-rate, token count.
2. Establish the baseline before changing anything.
3. Run the *actual* command each iteration. Record command → metric → baseline → delta.
4. Gate on the target. Watch for overfitting to the eval set — hold out cases or rotate.

Reporting rule: never present a Tier-4 number without the exact command that produced it and the baseline it beats. A metric with no reproduction command is not an eval result.

### Composing tiers

Tiers stack across the workflow:
- Plan → Tier 1 (rubric).
- Prototype/candidates → Tier 1 or Tier 3.
- Implementation → Tier 2 (behavior) + Tier 4 (metrics).

Start at the lowest tier that gives a signal you'd act on; escalate only where the cheaper signal is untrustworthy or the stakes justify the cost.

---

## Quick reference

| Situation | Loop mode | Eval tier |
|---|---|---|
| Plan is close, polish it | Phase-refinement | Tier 1 |
| Pick the best of several directions | Generate–eval–select | Tier 1 or Tier 3 |
| Multi-pass project / tuning | Full-workflow | Tier 4 (+ Tier 1 per pass) |
| Behavior must be correct | Phase-refinement | Tier 2 |
| Prompt / agent / voice quality | Generate–eval–select | Tier 3 |
| Hit a metric / beat a benchmark | Full-workflow or phase-refinement | Tier 4 |
