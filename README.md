# Fable 5 Harness

Companion skills and interactive explainers for the **Fable 5 Harness** series — production patterns for building on Claude Fable 5. Each article in the series ships one skill and its live interactive demo.

## Skills

| Skill | What it does | Article |
|-------|--------------|---------|
| `fable-prompt` | Unknowns-first prompting workflow: discover → plan → implement → review, with looping and evals | 1 — effort (companion) |
| `fable-effort` | Route `output_config.effort` from the task's kind instead of paying `high` on everything | 1 — effort |
| `fable-loop` | Autonomous self-scoring convergence loop with a fixed domain eval bank | 2 — loops (companion) |
| `fable-loop-adaptive` | Same loop, but the model generates the evals from first principles | 2 — loops (companion) |
| `fable-verify` *(planned)* | Fresh-context verifier subagent — audit claims against artifacts, not self-critique | 2 — loops |
| `fable-memory` *(planned)* | Lesson-per-file memory with load discipline and poisoning defenses | 3 — memory |
| `fable-fallback` *(planned)* | Refusal-aware calling: wire fallbacks, branch on `stop_reason`, count `usage.iterations` | 4 — refusal |
| `fable-scan` *(planned)* | Fable-native linter: drift categories + hard-400 migration checks | 5 — production |
| `fable-jobpacket` *(planned)* | Four-stage prep → plan/audit → execute → verify runner | 6 — projects |

## Install (Claude Code)

```bash
/plugin marketplace add <your-github-username>/fable5-harness
/plugin install fable5-skills@fable5-harness
```

Or copy a single skill folder:

```bash
git clone https://github.com/<your-github-username>/fable5-harness
cp -r fable5-harness/skills/fable-effort ~/.claude/skills/
```

## Live interactives

The `docs/` folder is published via GitHub Pages. Each article links to its interactive here:

- Effort dial — `docs/effort-dial.html`
- (more as the series ships)

## Verify before relying on it

Skill bodies encode Fable 5 API specifics (effort levels, fallback headers, `usage.iterations`, memory-tool ops). Every skill carries a "verify against current docs" note; confirm against [platform.claude.com](https://platform.claude.com/docs) before wiring to production. The plugin/marketplace manifest format follows the [Claude Code plugin-marketplace docs](https://code.claude.com/docs/en/plugin-marketplaces).

## License

`[choose a license before publishing — MIT is a reasonable default for skills you want reused]`

---

*By Ali Zeeshan — Head of AI, Adept Tech Solutions. Concept, framing, and interactive figures are original; underlying Fable 5 API facts are from Anthropic's documentation.*
