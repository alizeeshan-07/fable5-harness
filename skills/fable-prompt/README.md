# Fable Prompt Skill (`/fable-prompt`)

A Claude Code skill for unknowns-first prompting and implementation.

## Install in Claude Code

Project-only install:

```bash
mkdir -p .claude/skills
cp -R fable-prompt .claude/skills/fable-prompt
```

Personal install across projects:

```bash
mkdir -p ~/.claude/skills
cp -R fable-prompt ~/.claude/skills/fable-prompt
```

Then start Claude Code and run:

```text
/fable-prompt
```

or:

```text
/fable-prompt Build a new onboarding dashboard and help me discover the unknowns first
```

## Upload to claude.ai

Zip the outer `fable-prompt` folder and upload it as a custom Skill in Claude settings, if your plan supports custom Skills.

## Recommended use

Use `/fable-prompt` at the start of ambiguous work. The skill will interview you one question at a time, do a blind-spot pass, brainstorm/prototype where useful, create an implementation plan, implement only after approval, keep notes, and produce a final explainer plus quiz.

## Looping and evals

Two capabilities extend the base workflow:

- **Looping** (`/fable-prompt loop [phase]`) turns any phase — or the whole workflow — into an iterative improvement cycle with a declared stop condition. Three modes:
  - *Phase-refinement* — re-run one phase until it clears its gate.
  - *Generate–eval–select* — produce N candidates, score all, keep/synthesize the best.
  - *Full-workflow* — cycle discover→plan→implement→review for multi-pass projects, carrying the Unknowns Board and scorecard forward.
- **Evals** (`/fable-prompt eval [target]`) attach explicit, scored acceptance criteria to any output. Four tiers, lightest-first:
  - *Tier 1* rubric self-scoring (0–5, weighted, gated).
  - *Tier 2* structured harness (enumerated cases → pass rate).
  - *Tier 3* LLM-as-judge (judge prompt, pairwise/anchored, bias-controlled).
  - *Tier 4* code/metric loop (real commands, baseline, delta — tests, accuracy, latency, cost, reward).

Every loop has an iteration cap and a stop condition; every eval locks its rubric before scoring and gates on a threshold. See `reference/looping-and-evals.md` for full procedures and `templates/eval-scorecard.md` for the scoring sheet.

### Files

```
fable-prompt/
  SKILL.md                         # workflow + looping/evals behavior
  README.md
  TRIGGER.md
  reference/
    question-bank.md
    looping-and-evals.md           # loop modes + eval tiers, worked procedures
  templates/
    implementation-notes.md
    explainer-and-quiz.md
    eval-scorecard.md              # one sheet per scoring event
```
