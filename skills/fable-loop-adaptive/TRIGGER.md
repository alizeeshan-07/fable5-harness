# Trigger

Packaged in a top-level folder named `fable-loop-adaptive`; `SKILL.md` uses `name: fable-loop-adaptive`.

Invoke with:

```text
/fable-loop-adaptive
```

Or pass a goal directly:

```text
/fable-loop-adaptive Write an on-page SEO pass for this article and iterate to a target score
```

Subcommands:

```text
/fable-loop-adaptive domain-scan   # sense the domain only
/fable-loop-adaptive evals         # generate the top-10 eval set only
/fable-loop-adaptive run           # start/continue the scored loop
/fable-loop-adaptive rescore       # re-score against the frozen set
/fable-loop-adaptive freeze        # save the eval set for reproducible reuse
/fable-loop-adaptive reuse         # load a frozen eval set instead of generating
/fable-loop-adaptive report        # emit the final scorecard
```

`disable-model-invocation: true` — runs only on explicit `/fable-loop-adaptive` invocation.
