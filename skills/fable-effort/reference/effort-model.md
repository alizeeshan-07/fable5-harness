# Reference — how effort actually behaves

Read this before a non-trivial audit. The recommendations in `SKILL.md` fall out of these four facts.

## 1. The default is a decision

`high` is the default effort, and it is identical to omitting `output_config.effort`. A fleet that never sets effort is not "neutral" — it runs every call at `high`, which is the more expensive choice on routine traffic. Treat an unset call site as a finding, not a baseline.

## 2. Effort moves more than length

A token budget capped one thing: how much the model could think. Effort is a behavioral signal that shapes the whole response. Lower it and three things move together:

- **Prose length** — shorter output.
- **Thinking depth** — shallower reasoning.
- **Tool calls** — fewer calls, leaner arguments. Tool-call tokens are output tokens too, and the same signal governs them.

The third is the one people miss. Running an agent at `low` doesn't just make it terser — it makes it reach for fewer tools and do less work per turn. That is right for a triage step that should stay shallow and wrong for an audit step that needs every tool it can justify.

## 3. Effort biases; it does not fence

Because effort is a behavioral signal and not a hard cap, it does not guarantee a token ceiling. Keep `max_tokens` as the actual safety limit. Tune effort to the task's need for depth; use `max_tokens` to bound spend.

## 4. The cost inversion

`low` and `medium` on Fable 5 often beat what a previous model produced at `xhigh`. That flips the usual cost argument: the expensive model at low effort can be the cheap option. So `xhigh` is not the everyday setting — reserve it for the hardest coding and agentic runs, where a wrong answer is expensive to get.

## What to log

Record `effort` next to `output_tokens` on every routed call. This is the pair that catches a mis-route: a task you routed to `low` that spends like `high` is either mis-classified or mis-routed, and you want that visible in the logs before it shows up on the bill. `report` and `audit` both lean on this signal.

## Verify

Level names (`low`/`medium`/`high`/`xhigh`/`max`), `high`-as-default, the field name `output_config.effort`, and pricing derive from the Fable 5 series and may be post-cutoff. Confirm against current official documentation before treating any of them as settled.
