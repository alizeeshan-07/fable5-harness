# Scorecard: [task]

Domain: [...]   Artifact: [...]
Budget: max [N] iterations.   Target: [score/100 or "converge"].
Frozen eval set: see eval-spec.md.   Convergence metric: weighted total (0–100).

## Per-iteration log

### Iteration 1/N — total: __/100 (Δ —) | [V] __/__ pts | [S] __/__ pts
| # | Eval | Score | V/S | Evidence (one line, real result for [V]) |
|---|------|-------|-----|------------------------------------------|
| 1 |  | /5 |  |  |
| ... |
Lowest evals: #_, #_. Root-cause hypothesis: ...
Decision: continue / stop-target / stop-plateau / stop-cap / stop-risk / stop-verified-max

### Iteration 2/N — total: __/100 (Δ __) | [V] __/__ pts | [S] __/__ pts
| # | Eval | Score | V/S | Evidence |
|---|------|-------|-----|----------|
Change made this pass: ...  (each point gained must map to this change)
Decision: ...

## Final scorecard

Best iteration: __ (total __/100). Iterations run: __ of __. Stop reason: ...

Trajectory: it1 __ -> it2 __ -> it3 __ ...

| # | Eval | Final | V/S | Evidence |
|---|------|-------|-----|----------|

Trust note: __ of 100 points externally verified [V]; __ self-assessed [S] — weight accordingly.
Residual weaknesses: ...
Would raise the score further (out of budget): ...
Accepted shortfalls (if user agreed to ship below target): ...
