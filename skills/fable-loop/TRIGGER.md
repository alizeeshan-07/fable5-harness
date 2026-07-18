# Trigger

Packaged in a top-level folder named `fable-loop`; `SKILL.md` uses `name: fable-loop`.

Invoke with:

```text
/fable-loop
```

Or pass a goal directly:

```text
/fable-loop Build a data-cleaning script and iterate it to a target score
```

Subcommands:

```text
/fable-loop domain-scan   # sense the domain only
/fable-loop evals         # propose the top-10 eval set only
/fable-loop run           # start/continue the scored loop
/fable-loop rescore       # re-score the current artifact against the frozen evals
/fable-loop report        # emit the final scorecard
```

`disable-model-invocation: true` — runs only on explicit `/fable-loop` invocation, not automatically.
