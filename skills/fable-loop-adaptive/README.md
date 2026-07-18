# Fable Loop — Adaptive (`/fable-loop-adaptive`)

An autonomous, self-scoring improvement loop in which **Claude generates the evals itself, for the specific artifact**, instead of adapting a fixed per-domain checklist. From one goal it senses the domain, generates the top ~10 evals from a method, asks only for iterations + a target score, then builds → scores → refines on its own until the score converges, and returns a scorecard.

```
sense domain -> GENERATE top ~10 evals for this artifact -> you set iterations + target
             -> [ build -> score -> diagnose -> refine ] repeated automatically
             -> final scorecard
```

## How it differs from `fable-loop`

Both run the same autonomous scored loop with the same anti-gaming discipline (verifiable-first scoring, frozen rubric, adversarial grading, [V]/[S] trust note, honest plateaus). The difference is where the evals come from:

- **`fable-loop`** — ships worked per-domain eval banks (code, ML, prompt/agent, writing, data, sales, marketing, lead-gen, SEO) that the model adapts. More consistent and calibrated out of the box; bounded to the domains provided.
- **`fable-loop-adaptive`** (this one) — ships a generation *method* (seven lenses + [V]/[S] discipline + verifiability-ceiling rule) and only ~3 calibration exemplars. The model derives evals for the specific artifact from first principles. More general (works on any domain, listed or not) and better tailored; higher variance run-to-run.

To make the adaptive version reproducible, it adds **freeze-and-reuse**: approve an eval set once, freeze it to `templates/eval-spec.md`, and reuse it verbatim on later runs so scores are comparable and you can track improvement over time.

Pick banks (`fable-loop`) when you want a reliable, calibrated floor and mostly work in known domains. Pick adaptive (this) when you want maximum generality and per-artifact tailoring, and don't mind approving the generated evals at the gate.

## Install in Claude Code

```bash
mkdir -p ~/.claude/skills && cp -R fable-loop-adaptive ~/.claude/skills/fable-loop-adaptive
```

Then:

```text
/fable-loop-adaptive Draft and iterate a launch announcement to a target score
```

## Install in Claude.ai / Cowork

Upload the packaged `fable-loop-adaptive.skill` (or a zip of the outer folder) as a custom Skill, or click **Save skill** on the presented card.

## Subcommands

`domain-scan` · `evals` · `run` · `rescore` · `freeze` · `reuse` · `report` — see `SKILL.md`.

## Files

```
fable-loop-adaptive/
  SKILL.md                     the autonomous scored-loop workflow (model-generated evals)
  README.md                    this file
  TRIGGER.md                   invocation
  reference/
    eval-generation.md         the generation method, calibration exemplars, freeze/reuse
    eval-and-loop.md           scoring math, self-scoring validity, anti-gaming, stop rules
    question-bank.md           the minimal setup questions
  templates/
    eval-spec.md               the frozen eval set (also the freeze/reuse target)
    scorecard.md               per-iteration + trajectory + final trust note
    implementation-notes.md    working notes during the loop
    explainer-and-quiz.md      final report + quiz
```
