---
name: fable-effort
description: "Stop running every Claude Fable 5 call at the silent `high` default. This skill classifies a task (or a whole set of call sites) by kind, assigns a deliberate effort level per task, and emits a reusable effort policy — an `effort_for(task)` router plus logging guidance — so cheap work runs cheap and only capability-sensitive work pays for depth. Use when the user invokes /fable-effort, asks how hard the model should work, wants to cut Fable 5 spend, is migrating call sites off a fixed default, or asks what effort level a task should use."
argument-hint: "[task description | classify | policy | audit | route | report]"
disable-model-invocation: true
---

# Fable Effort Skill

Use this skill when the user invokes `/fable-effort`. It turns effort from a knob nobody set into a written-down policy the whole application inherits.

The core operating principle: **effort is a behavioral signal, not a token budget.** Lowering it shortens prose, shallows thinking, AND cuts tool calls — the model does less work per turn, not just writes less. Raising it does the opposite. So effort must be matched to a task's genuine need for depth, and `max_tokens` — not effort — is the real safety cap. The default (`high`, identical to omitting the field) is a choice made by not choosing, and it is the more expensive choice on routine traffic.

> Verify before shipping: effort level names, the `output_config.effort` field, and pricing are drawn from the Fable 5 series and may be post-cutoff. Confirm against current official docs before wiring money to any number here.

## The five levels (what to reach for)

| Level | Reach for it when | Typical work |
|-------|-------------------|--------------|
| `low` | There is a right answer more thinking won't improve | classify, extract, format, triage |
| `medium` | Some judgment, bounded scope | summarize, chat, route |
| `high` *(default)* | General analysis and coding | most day-to-day work |
| `xhigh` | Capability-sensitive, branchy, expensive-to-get-wrong | migration, security audit, novel research |
| `max` | The hardest agentic runs only | rare; slowest and priciest |

Two facts to internalize: `high` is exactly what you get by omitting the field, so an unset fleet runs everything here; and `low`/`medium` on Fable 5 often beat a previous model's `xhigh`, which flips the cost argument.

## Invocation behavior

- `/fable-effort <task>` — classify one task, recommend a level with rationale, and show what moves at that level.
- `/fable-effort classify` — classify a batch of tasks or call sites the user provides.
- `/fable-effort policy` — emit a reusable `effort_for(task)` router from the tasks/kinds seen so far (see `templates/effort-policy.md`).
- `/fable-effort audit` — scan provided call sites / code for effort mis-set: unset (silent `high`), `xhigh` on cheap work, `low` on an audit step, missing `max_tokens`.
- `/fable-effort route` — given one task's `kind`, return the level the policy assigns (no prose, just the routing).
- `/fable-effort report` — summarize the policy: kinds → levels, expected effect on spend, and what to log.

If `$ARGUMENTS` is empty or vague, ask one question — "what task, or which call sites?" — then proceed.

## Non-negotiable behavior

1. **Match effort to depth, never to length.** Assign a level from the task's need for judgment and tool use, not from how long the output should be.
2. **Name what moves.** Every recommendation states the effect on all three axes — prose, thinking depth, and tool-call count/richness — not just cost.
3. **Effort biases; `max_tokens` fences.** Always pair an effort recommendation with a `max_tokens` cap. Never present effort as a spend guarantee.
4. **Default is a decision.** Flag any unset call site explicitly as "running at `high` by omission" — never treat unset as neutral.
5. **Cheap work stays cheap.** If a task has a checkable right answer (classify/extract/format), do not route it above `low` without a stated reason.
6. **Audits earn depth.** Do not lower effort on a step whose value is thoroughness (security audit, migration review) just to save tokens — that silently removes tool calls the step needs.
7. **Log effort next to output tokens.** Every routed call should record the pair, so a mis-route is caught in the logs before it shows up on the bill.
8. **One place to change.** The policy lives in a single `effort_for` function; changing a kind's level is one edit, never a hunt through call sites.

## Phase 1 — Classify the task(s)

For each task, infer its `kind` and the judgment it actually requires. Output:

```md
## Effort classification
| Task / call site | Inferred kind | Judgment needed | → Level | max_tokens |
|------------------|---------------|-----------------|---------|-----------|
| ...              | classify/extract/format/summarize/chat/route/migrate/audit/novel-research/other | none / bounded / high | low/medium/high/xhigh/max | e.g. 2048 |
Unset call sites (running at `high` by omission): [list]
```

If a task's kind is genuinely ambiguous in a way that changes the level, ask exactly one question. Otherwise proceed.

## Phase 2 — Recommend, and name what moves

For the headline task, state the level and the effect across all three axes plus cost, in one compact block:

```md
## Recommendation: <level>
Prose: <shorter/longer> · Thinking depth: <shallower/deeper> · Tool calls: <fewer/more>
Cost vs high: <lower/same/higher>. Fence: max_tokens=<n> (the real cap; effort only biases).
Why: <one line tying the level to the judgment the task needs>.
Watch: <the failure mode of getting this wrong — e.g. "low here removes tools an audit needs">.
```

## Phase 3 — Emit the policy

When the user wants the rule rather than a one-off pick, generate the router from `templates/effort-policy.md`, filled with the kinds seen. Every kind maps to a level; anything unclassified falls through to `high` so an unrouted task is never worse off than the default. Include the logging line (effort + output tokens) and the `max_tokens` pairing.

## Phase 4 — Audit existing call sites

Given code or a list of calls, flag each finding with severity:

```md
## Effort audit
| Call site | Finding | Severity | Fix |
|-----------|---------|----------|-----|
| ... | unset effort → silent high | warn | set explicitly, likely `low`/`medium` |
| ... | xhigh on a format task | warn | drop to low |
| ... | low on an audit step | error | raise to xhigh; low strips tool calls |
| ... | no max_tokens | warn | add a hard cap |
```

Lead with the cheapest wins (unset routine calls) — they are the largest, safest savings.

## Reference

Read `reference/effort-model.md` before a non-trivial audit — it explains why effort moves tool calls (they are output tokens too), the `low`-beats-last-gen-`xhigh` cost inversion, and how to log the effort/output-token pair.

## Style

Terse and decisive. Give one level per task, not a menu. Always pair effort with `max_tokens`. When a level is a judgment call, say so and give the deciding factor — never hedge across three levels.
