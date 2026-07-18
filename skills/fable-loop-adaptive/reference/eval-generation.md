# Generating the Evals — Reference

This variant does not carry a fixed checklist of evals per domain. It carries a **method** for generating them and a small set of **calibration exemplars** that show the shape and quality bar. The evals themselves are produced for the specific artifact in front of you.

Why this way: a fixed bank can only cover domains someone enumerated, and it anchors you toward generic checks instead of what *this* task actually needs. Generating from a method is general (any domain, listed or not) and specific (tailored to the artifact). The cost is variance and a temptation toward soft, self-flattering evals — sections 1–3 exist to control both.

## Contents

1. The generation method (the lenses)
2. Verifiable vs self-judged — bias the set toward [V]
3. Always name the verifiability ceiling
4. Calibration exemplars (shape, not a menu)
5. Reproducibility — freeze and reuse

---

## 1. The generation method (the lenses)

For any artifact, walk these lenses to produce candidate evals, then rank and cut to ~10:

- **Correctness** — does it do what it claims on the intended path?
- **Robustness** — edge cases, malformed/empty/extreme input, failure handling.
- **Constraints** — the hard requirements and "must-not-break" invariants the user named.
- **Fit to intent** — matches what the user actually wanted, not a generic version of it.
- **Craft** — this artifact's notion of quality (readability, latency, clarity, rigor, persuasiveness…).
- **Safety / truth** — no fabrication, no unsafe output, claims and data are real and sourced.
- **Cost** — the artifact's resource axis (runtime, tokens, length, complexity, spend).

Then shape the set:

- **Rank by discriminating power × verifiability.** Keep evals that separate good from bad *and* that you can check by running something. Cut evals that pass regardless of effort — non-discriminating evals are scorecard padding.
- **~10 evals, weights sum to 100**, heavier on the axes the user emphasized. Score each on **0–5** (enough resolution without false precision).
- **Tag every eval [V] verifiable or [S] self-judged** (section 2).
- **Turn the user's "what would prove this is broken?" answers into concrete evals** — those are your highest-signal cases.

If the set you generate is mostly soft [S] "is it good?" checks, it has failed the bar — regenerate with harder, runnable checks. This is the single most important discipline when the model authors its own evals.

## 2. Verifiable vs self-judged — bias toward [V]

A **[V]** eval is decided by executing something — tests, a build, a linter, a metric script, a schema/format check, a link-check, a word/char count. Its score is evidence, not opinion. An **[S]** eval is scored against a written rubric (clarity, tone, persuasiveness, elegance); useful but soft, and exactly what a self-scoring loop inflates if unchecked.

- Convert [S] to [V] wherever you can. "Reads clearly" → "reading grade ≤ 10 via a script, all links resolve, lints clean."
- Aim for at least half the weight on [V] when the domain allows. If the artifact is inherently subjective, say so and set expectations that the score is largely self-assessed.
- The scorecard always reports the [V]/[S] point split so the user can calibrate trust.

## 3. Always name the verifiability ceiling

Every domain has a real outcome the evals cannot directly measure. Name it explicitly so the score is never mistaken for the outcome. Examples of the *kind* of ceiling to state (not an exhaustive list — derive the one that fits):

- Sales outreach: message quality is scorable; **reply/booking rate needs a live send.**
- Marketing/SEO content: on-page quality is scorable; **engagement, conversion, and ranking are external and time-lagged.**
- Lead lists: format/ICP-match is scorable; **deliverability and whether a contact is real need an external verification tool — never fabricate a contact to raise a score.**
- ML: held-out metrics are scorable; **real-world/production performance is not.**
- Research/writing: internal accuracy and structure are scorable; **real-world correctness of every claim may not be.**

State the ceiling in one line at the bottom of the generated eval set.

## 4. Calibration exemplars (shape, not a menu)

These three show the *shape and quality bar* of a good generated set across the verifiable-to-subjective spectrum. **Do not pick from them.** Generate for the actual artifact; use these only to check that your set is as concrete, weighted, tagged, and discriminating as these are.

**Verifiable-heavy (e.g. backend code / SEO / lead list):**
```
| # | Eval | Scorer | V/S | Weight |
| 1 | Test suite passes (all green)        | test   | V | 20 |
| 2 | Handles empty/malformed input safely | test   | V | 15 |
| 3 | Meets the named interface/spec       | test   | V | 15 |
| 4 | Builds / type-checks / lints clean   | build  | V | 10 |
| 5 | Performance within target            | metric | V | 10 |
| 6 | ... (readability/structure [S], scope discipline [S], no secrets [V]) |
Ceiling: passing local checks ≠ correct in production.
```

**Mixed (e.g. sales email / analysis):**
```
| 1 | Within length limits; one clear CTA           | script | V | 15 |
| 2 | All personalization tokens filled             | script | V | 10 |
| 3 | No banned/absolute claims (checkable list)    | script | V | 10 |
| 4 | Hook is specific, not generic                 | judge  | S | 15 |
| 5 | Value prop concrete & prospect-specific       | judge  | S | 15 |
| 6 | ... (tone fit [S], objection pre-empted [S], reading grade [V]) |
Ceiling: reply rate needs a live send; this scores message quality.
```

**Subjective-heavy (e.g. persuasive copy / narrative):**
```
| 1 | Meets format/length spec         | script | V | 10 |
| 2 | No fabricated facts/stats        | check  | V | 15 |
| 3 | Opening earns the next line      | judge  | S | 15 |
| 4 | Audience fit, not brand ego      | judge  | S | 20 |
| 5 | Differentiated, not template     | judge  | S | 20 |
| 6 | ... (structure/skimmability [S], readability [V]) |
Ceiling: mostly self-judged — the number is a structured self-critique, not a measurement. Say so.
```

## 5. Reproducibility — freeze and reuse

Dynamic generation means two runs can yield different eval sets, so scores are not comparable run-to-run. When the user wants comparable, trackable scoring:

- **Freeze** the approved set — evals, weights, scales, scorer definitions, and [S] rubrics — into `templates/eval-spec.md` (or a project file the user names). That file becomes the canonical frozen rubric.
- **Reuse** it verbatim on later runs (`reuse`) instead of regenerating. Same rubric = comparable scores and honest trend tracking.
- **Regenerate** only when the artifact or goal changes enough that the frozen set no longer fits — and say so, because it breaks comparability with prior runs.

Rule of thumb: generate while exploring; freeze once the eval set is trusted and you want to measure improvement over time.
