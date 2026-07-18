# Eval Scorecard

Fill one scorecard per scoring event. Lock criteria and weights **before** scoring.

## Header

- Target: [what is being scored — plan / candidate N / implementation / output]
- Tier: [1 rubric | 2 harness | 3 judge | 4 code/metric]
- Pass gate: [e.g., weighted ≥ 80% | 100% must-pass | metric ≥ target]
- Iteration: [k of N]
- Date/context: 

---

## Tier 1 — Rubric self-scoring

| Criterion | Weight % | Score 0–5 | Evidence (one line) |
|---|---|---|---|
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
| **Total** | **100** | **weighted %** |  |

Weighted score = Σ(score/5 × weight). Result: __ % — **PASS / FAIL** vs gate.

---

## Tier 2 — Structured harness

| # | Case (input → expected property) | Must/Should | Result | Pass? |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |

Pass rate: __ / __ (must-pass: __%, should-pass: __%) — **PASS / FAIL** vs gate.

---

## Tier 3 — LLM-as-judge

- Judge prompt used: [summary or link]
- Mode: [absolute | pairwise A vs B | reference-anchored]
- Bias mitigations applied: [position ▢ verbosity ▢ self-preference ▢ anchoring ▢]

| Criterion | Score | Evidence cited |
|---|---|---|
|  |  |  |
|  |  |  |

Verdict: [winner / score] — **PASS / FAIL** vs gate.

---

## Tier 4 — Code / metric

| Metric | Command run | Baseline | Result | Δ | Target | Pass? |
|---|---|---|---|---|---|---|
|  |  |  |  |  |  |  |

Notes on overfitting / held-out coverage: 

---

## Decision

- Outcome: [ship / refine weak dimensions / regenerate / another cycle / escalate to user]
- Weakest dimension to target next: 
- Loop tally: iteration k/N, Δ vs previous, convergence? [y/n]
