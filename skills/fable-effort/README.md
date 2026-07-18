# Fable Effort Skill (`/fable-effort`)

Stop paying `high` on everything. This skill turns Claude Fable 5's effort dial from a knob nobody set into a **written-down policy** your whole application inherits.

## The problem it fixes

On Fable 5, `high` effort is the default — and it is *exactly* what you get by omitting the field. So a fleet that never sets effort runs every call at `high`, including the classification and formatting calls that would return an identical useful answer at `low` for a fraction of the tokens. The default is not free; it is the more expensive choice on routine traffic.

And effort is not a token budget with a new name. Lower it and three things move together — prose gets shorter, thinking gets shallower, and the model makes **fewer tool calls with leaner arguments**. That third effect is the one people miss: run an agent at `low` to save money and you get an agent that does less work per turn. Right for triage, wrong for an audit.

## What it does

```
classify each task by kind  ->  assign a deliberate level (low..max)
                            ->  name what moves (prose / depth / tool calls)
                            ->  emit effort_for(task): one router, one edit to change
                            ->  pair every call with max_tokens (the real fence)
```

- **Cheap work runs cheap.** classify / extract / format → `low`.
- **Only hard work pays for depth.** migrate / audit / novel-research → `xhigh`.
- **Unset is flagged, never neutral** — it means "silent high."
- **Effort biases, `max_tokens` fences** — effort is never presented as a spend guarantee.

## Live demo

`demo/effort-dial.html` — drag the dial from `low` to `max` and watch prose, thinking depth, and tool calls move together while the `max_tokens` fence stays fixed. This is the article's hero visual.

## Install in Claude Code

```bash
# project-only
cp -r fable-effort ~/.claude/skills/
```

## Verify before shipping

Effort level names, the `output_config.effort` field, and pricing come from the Fable 5 series and may be post-cutoff. Confirm against current official docs before wiring any number to spend.
