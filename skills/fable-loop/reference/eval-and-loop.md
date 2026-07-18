# Scoring and the Convergence Loop — Reference

Read before running a non-trivial loop. This covers how scores are computed, how the loop decides to stop, and — most importantly — how to keep a self-scoring loop honest.

## Contents

1. Scoring math
2. Self-scoring validity (the central problem)
3. Adversarial scoring procedure
4. Stop rules and convergence
5. Anti-patterns

---

## 1. Scoring math

Each eval `i` has a raw score `s_i` on its scale (default 0–5) and a weight `w_i` (weights sum to 100). Normalize each to its scale max `m_i` and combine:

```
total = Σ ( w_i × s_i / m_i )        → a 0–100 weighted score
```

Report two subtotals every iteration: points from **[V] verifiable** evals and points from **[S] self-judged** evals. This split is the trust signal — a total of 80 that is 70 [V] / 10 [S] is worth far more than 80 that is 20 [V] / 60 [S].

Scales: prefer 0–5. Finer scales (0–100 per eval) imply precision the scorer does not have, especially for [S] evals. For [V] evals with a natural continuous metric (latency, F1), score against the threshold: meets target = full marks, and partial credit only if the eval defines a band.

## 2. Self-scoring validity (the central problem)

The model proposes the evals, produces the work, *and* grades it. Left unchecked this collapses into three failure modes:

- **Grade inflation** — scores drift up because the grader is the author.
- **Manufactured monotonicity** — the total "improves" every iteration because that is what a loop is expected to do, not because the work got better.
- **Goodhart / gaming** — the artifact is optimized to satisfy the scorer (especially an [S] judge) rather than the real goal.

The defenses, in priority order:

1. **Verifiable-first.** Every point that can come from executing something (tests, build, lint, metric, schema, link-check) must. Running code cannot be flattered. This is the strongest defense — maximize [V] weight.
2. **Frozen rubric.** Eval set, weights, and scales are fixed at setup. Editing an eval to pass is the cardinal sin. Genuine rubric bugs are fixed in the open, announced to the user, and do not count as a score gain.
3. **Adversarial grading** (section 3).
4. **Evidence per score.** No number without a one-line justification. A score with no evidence is an opinion; treat it as the floor, not the grade.
5. **Honest plateau.** Convergence below target is a valid outcome. Never nudge to hit a number.
6. **Trust disclosure.** The final report states the [V]/[S] split so the user calibrates.

## 3. Adversarial scoring procedure

Grade each iteration as if trying to reject the work, not bless it:

- For each eval, first ask "what is the strongest reason this loses points?" Deduct for it, then justify any credit above the floor.
- Run [V] checks for real; paste the actual result (test counts, metric value, lint output) as evidence. Never estimate a [V] score.
- For [S] evals, score against the written rubric only. If the rubric is ambiguous, that is a rubric problem — do not resolve it in the artifact's favor.
- Cross-check against the previous iteration: if the total rose, name the specific change that earned each additional point. If you cannot, the rise is not real — revert the score.
- For [S] evals on high-stakes gates, sample: score two or three times and take the lower/median. A score that swings between passes is not a grade.

## 4. Stop rules and convergence

Announce the iteration cap and target before looping. Exit on the first of:

- **TARGET** — weighted total ≥ the user's target.
- **CAP** — iteration budget exhausted.
- **PLATEAU** — total improves by less than epsilon (default ~2 points) for 2 consecutive iterations, or oscillates (fixing A breaks B and back). This is convergence, not failure — report the plateau level and what is capping it.
- **RISK** — a high-risk or irreversible unknown appeared; stop and ask.
- **VERIFIED-MAX** — all [V] evals are maxed and only [S] evals remain soft. Stop and say so, rather than grinding subjective scores upward, which is where gaming lives.

Track one convergence number (the weighted total) per iteration. Diagnose before each refinement: a one-line root cause for the lowest evals. If you cannot state a hypothesis, gather signal (log, inspect, add a narrower check) instead of tweaking blindly.

## 5. Anti-patterns

- **Rubric drift** — quietly changing evals/weights mid-loop so the number climbs.
- **[S]-heavy scorecards** — most weight on self-judged evals; the score is then mostly self-flattery. Convert to [V] or disclose loudly.
- **Estimated [V] scores** — claiming tests pass without running them. Always execute.
- **Monotonicity theater** — every iteration up by a suspiciously smooth amount, with no concrete change behind the gains.
- **Perfect-score fixation** — grinding to 100 when the honest ceiling is lower; a defensible 82 with evidence beats a fabricated 100.
- **Judge gaming** — optimizing wording to please an [S] judge while real quality stalls or drops. If [V] evals stagnate while [S] climbs, you are gaming.
- **Non-discriminating evals** — cases that pass no matter what. They pad the score; cut them.
