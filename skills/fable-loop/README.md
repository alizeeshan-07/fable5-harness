# Fable Loop Skill (`/fable-loop`)

Turn one request into an **autonomous, self-scoring improvement loop**. Instead of you prompting "now score it / now improve it / do it again," the skill runs the whole cycle itself and hands back a scored deliverable.

## What it does

From a single goal, in one setup exchange, then hands-off:

```
sense the domain  ->  propose the top ~10 evals for that domain
                  ->  you set: max iterations + target score
                  ->  [ build -> score -> diagnose -> refine ] repeated automatically
                  ->  final scorecard
```

The only thing you provide after the goal is: which of the proposed evals to keep, how many iterations to cap at, and the score to stop at (or "converge"). The loop does the rest and shows its score every pass.

## Why it's different

Most skills produce an artifact and stop. This one **defines what "good" means in your domain, then iterates against it until the score stops climbing** — and it does so honestly:

- **Verifiable-first scoring.** Wherever a property can be checked by running something (tests, build, lint, a metric, a script), it runs it and uses the real result. Every eval is labelled **[V] verifiable** or **[S] self-judged**, and the final scorecard reports the split so you know how much to trust the number.
- **Anti-gaming by design.** The eval rubric is frozen before the loop (no editing evals to pass), scoring is adversarial (hunt for deductions), every score carries evidence, and a plateau below target is reported honestly rather than papered over with a fake 100.

That honesty layer is the point — a self-scoring loop that inflates its own grades is worthless; this one is built not to.

## Install in Claude Code

```bash
# project-only
mkdir -p .claude/skills && cp -R fable-loop .claude/skills/fable-loop
# or personal, across projects
mkdir -p ~/.claude/skills && cp -R fable-loop ~/.claude/skills/fable-loop
```

Then:

```text
/fable-loop Refactor the auth module and iterate to a quality bar
```

## Install in Claude.ai / Cowork

If your plan supports custom Skills, upload the packaged `fable-loop.skill` (or a zip of the outer `fable-loop/` folder) as a custom Skill, or click **Save skill** on the presented skill card.

## Publishing for others

The folder is self-contained and safe to share. Zip the outer `fable-loop/` folder (or produce a `.skill` archive) and distribute it; recipients install via the paths above or the **Save skill** button. To rename, change `name:` in `SKILL.md`, the folder name, and the command references — nothing else depends on the name.

## Subcommands

`domain-scan` · `evals` · `run` · `rescore` · `report` — see `SKILL.md`.

## Files

```
fable-loop/
  SKILL.md                     the autonomous scored-loop workflow
  README.md                    this file
  TRIGGER.md                   invocation
  reference/
    domain-evals.md            domain taxonomy + how to derive the top-10 + example banks
    eval-and-loop.md           scoring math, self-scoring validity, anti-gaming, stop rules
    question-bank.md           the minimal setup questions
  templates/
    eval-spec.md               the frozen top-10 weighted eval set
    scorecard.md               per-iteration + trajectory + final trust note
    implementation-notes.md    working notes during the loop
    explainer-and-quiz.md      final report + quiz
```

## When to use it

Reach for it when correctness is uncertain, regressions are costly, or the output benefits from being pushed to a measurable bar — code, model/prompt/agent work, analyses, docs, and go-to-market work (sales outreach, marketing content, lead-gen lists, SEO). For quick throwaway asks it's overhead; use plain prompting there.

Domain eval banks ship for: backend/systems code, ML, prompt/agent design, technical writing, data analysis, sales outreach, marketing content, lead generation, and SEO — with worked example scorecards for each in `reference/domain-evals.md`.
