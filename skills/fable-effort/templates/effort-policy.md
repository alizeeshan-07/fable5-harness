# Template — effort policy (`effort_for`)

The whole cost policy in one function. Every call site inherits it; changing a kind's level is one edit, not a hunt.

```python
def effort_for(task):
    kind = task.get("kind")
    if kind in ("classify", "extract", "format"):
        return "low"
    if kind in ("summarize", "chat", "route"):
        return "medium"
    if kind in ("migrate", "audit", "novel-research"):
        return "xhigh"
    return "high"  # the default for everything else — never worse than unset
```

Thread it through the call, keep the fallback wiring, and log the pair you want:

```python
def run(task):
    effort = effort_for(task)
    msg = client.beta.messages.create(
        model=FABLE,
        max_tokens=task.get("max_tokens", 2048),   # the real fence
        output_config={"effort": effort},
        betas=[BETA], fallbacks=FALLBACKS,
        messages=[{"role": "user", "content": task["prompt"]}],
    )
    return {
        "kind": task.get("kind"),
        "effort": effort,
        "output_tokens": msg.usage.output_tokens,   # log effort next to tokens
    }
```

Fill in for your app:

- Kinds you actually run: `______`
- Any kind that deserves a non-default level: `______`
- Default `max_tokens` per kind: `______`
- Where the effort/output-token pair is logged: `______`

Confirm `output_config.effort`, the fallback beta header, and `usage.output_tokens` against current docs before relying on them.
