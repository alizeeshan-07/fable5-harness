# Trigger

Packaged in a top-level folder named `fable-effort`; `SKILL.md` uses `name: fable-effort`.

Invoke with:

```text
/fable-effort
```

Or pass a task directly:

```text
/fable-effort Classify 40k support tickets into 12 intent labels
```

Subcommands:

```text
/fable-effort classify   # classify a batch of tasks or call sites
/fable-effort policy     # emit a reusable effort_for(task) router
/fable-effort audit      # flag effort mis-set across call sites
/fable-effort route      # given a kind, return the assigned level
/fable-effort report     # summarize the policy and its spend effect
```

`disable-model-invocation: true` — runs only on explicit `/fable-effort` invocation, not automatically.
