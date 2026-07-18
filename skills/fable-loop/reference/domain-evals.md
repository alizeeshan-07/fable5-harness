# Domain Sensing and the Top-10 Evals — Reference

This is how the skill answers "what does *good* mean here?" before it starts looping. The output of this step is a frozen, weighted eval set.

## Contents

1. Domain taxonomy
2. How to derive the top ~10 evals
3. Verifiable vs self-judged (why the mix matters)
4. Worked example banks

---

## 1. Domain taxonomy

Classify the goal + artifact into the closest domain; this seeds the eval bank. Common domains:

- **Backend / systems code** — services, APIs, scripts, data pipelines.
- **Frontend / UI** — components, pages, visual/interaction work.
- **ML / model work** — training, fine-tuning, evaluation, inference.
- **Prompt / agent design** — prompts, tools, agent workflows, other skills.
- **Data analysis** — queries, notebooks, statistical findings.
- **Technical writing** — docs, specs, runbooks, explainers.
- **Research** — literature synthesis, reports with claims and sources.
- **Business / persuasive** — proposals, decks, briefs.
- **Sales outreach** — cold emails, sequences, follow-ups, call scripts.
- **Marketing content** — blog posts, landing pages, social, email campaigns, ads.
- **Lead generation** — prospect lists, ICP targeting, enrichment, list QA.
- **SEO** — on-page optimization and content built for search intent.

If the artifact spans two domains (e.g. an ML training script), pull evals from both and keep the highest-signal ~10. Do not force a fit — if none matches, derive evals from first principles using the method below.

## 2. How to derive the top ~10 evals

For any domain, generate candidate evals by walking these lenses, then rank and cut to ~10:

- **Correctness** — does it do what it claims, on the happy path?
- **Robustness** — edge cases, malformed/empty/extreme inputs, failure handling.
- **Constraints** — the hard requirements and "must-not-break" invariants the user named.
- **Fit to intent** — does it match what the user actually wanted, not a generic version?
- **Quality of craft** — the domain's notion of good (readability, latency, clarity, rigor…).
- **Safety / correctness of claims** — no fabrication, no unsafe output, sources real.
- **Cost** — the domain's resource axis (runtime, tokens, length, complexity).

Ranking rule: keep the evals that most **discriminate** good work from bad and that you can most **verify**. Drop evals that would pass regardless of effort (non-discriminating) — they are scorecard filler. Aim for weights summing to 100, heavier on the axes the user emphasized. Prefer a 0–5 scale per eval (enough resolution, not false precision).

Every candidate must be tagged **[V] verifiable** (checkable by running something) or **[S] self-judged** (needs a rubric). Push for [V] coverage — see section 3.

## 3. Verifiable vs self-judged

The whole loop's credibility rests on this split. A **[V]** eval is decided by executing something — tests, a build, a linter, a benchmark, a metric script, a schema check. Its score is evidence, not opinion. An **[S]** eval is scored by the model against a written rubric (clarity, tone, elegance, persuasiveness); it is useful but soft, and it is exactly what a self-scoring loop will inflate if unchecked.

Rules of thumb:
- Convert [S] to [V] wherever possible. "Reads clearly" is soft; "passes `markdownlint`, all links resolve, reading grade ≤ 10 via a script" is hard.
- Keep at least half the weight on [V] evals when the domain allows. If a domain is mostly subjective (persuasive copy, aesthetics), say so up front and set expectations that the final score is largely self-assessed.
- The final scorecard always reports the [V]/[S] point split so the user can calibrate trust.

## 4. Worked example banks

Adapt these — do not paste blindly. Weights illustrative.

### Backend / systems code
1. [V] Tests pass — full suite green (weight 20, 0–5).
2. [V] Builds/type-checks clean — no errors/warnings (10).
3. [V] Handles edge/empty/malformed input without crashing (15).
4. [V] Lint/format clean (5).
5. [V] Performance within target (latency/throughput/memory) (10).
6. [S] Readability & structure — a reviewer could maintain it (10).
7. [V] No secrets/PII in code or logs (5).
8. [S] Matches the specified interface/behavior, not a generic one (15).
9. [V] Errors are handled and surfaced, not swallowed (5).
10. [S] Scope discipline — no unrequested changes (5).

### ML / model work
1. [V] Target metric ≥ threshold on held-out set (accuracy/F1/…​) (20).
2. [V] No train/test leakage; split is honest (15).
3. [V] Runs end-to-end reproducibly (seed, deterministic where possible) (10).
4. [V] Baseline captured and beaten (or matched with justification) (15).
5. [V] Compute/latency/params within budget (10).
6. [S] Ablation/error analysis is meaningful, not decorative (10).
7. [V] No metric gaming (e.g. degenerate outputs that score well) (10).
8. [S] Result claims are supported by the numbers shown (10).

### Prompt / agent design
1. [V] Passes a held-out set of should-trigger / should-not cases (20).
2. [V] Output conforms to the required format/schema (15).
3. [V] Robust to paraphrase/adversarial inputs across N samples (15).
4. [S] Instructions are unambiguous; failure modes anticipated (10).
5. [V] No prompt-injection / jailbreak on a small red-team set (10).
6. [V] Token/latency cost within budget (10).
7. [S] Behavior matches intent on qualitative spot-checks (10).
8. [V] Deterministic checks pass across repeated runs (stability) (10).

### Technical writing
1. [V] All links/refs resolve; code samples run (15).
2. [V] Reading grade / length within target (script) (10).
3. [S] A target reader can complete the task from it alone (20).
4. [S] Accurate — no claims unsupported by the source material (20).
5. [S] Structure: findable, skimmable, correct heading hierarchy (10).
6. [V] No broken formatting / lint clean (5).
7. [S] Terminology consistent throughout (10).
8. [S] Scope matches the brief; no padding (10).

### Data analysis
1. [V] Query/computation runs and reproduces the numbers (20).
2. [V] Row counts / joins / nulls sanity-checked (15).
3. [S] Conclusion is actually supported by the data (20).
4. [V] Correct aggregation grain / no double counting (15).
5. [S] Appropriate method for the question (10).
6. [V] Handles missing/outlier data explicitly (10).
7. [S] Caveats and confidence stated honestly (10).

### Sales outreach (cold email / sequence / script)
1. [V] Within channel limits — body word count and subject ≤ ~50 chars (script) (10).
2. [V] Exactly one clear ask/CTA present (10).
3. [S] Opens with a specific, relevant hook — not a generic "I came across your company" (15).
4. [S] Value proposition is concrete and prospect-specific, not a feature dump (15).
5. [V] All personalization tokens filled — no leftover {{first_name}} / [company] (10).
6. [S] Tone fits the persona and brand voice (10).
7. [V] No banned/absolute claims or spammy trigger words (checkable list); compliant with CAN-SPAM basics like an opt-out where required (10).
8. [S] A likely objection is pre-empted or the next step is frictionless (10).
9. [V] Reading grade ≤ target; skimmable in <15s (script) (5).
10. [S] Cold-read: would a busy buyer actually reply? (5).

*Ceiling: reply/booking rate is the real outcome and is NOT self-verifiable — it needs a live send. Score the message quality, and say the score predicts quality, not results.*

### Marketing content (blog / landing / social / email / ad)
1. [S] Headline/hook earns the next line and is on-message (15).
2. [V] Meets the channel's format and length spec (script) (10).
3. [S] Audience fit — addresses the reader's problem, not the brand's ego (15).
4. [V] One clear, consistent CTA present (10).
5. [S] Differentiated angle — not interchangeable template filler (15).
6. [V] Brand/claims compliance — banned words absent, required disclaimers present (checkable list) (10).
7. [S] Structure is skimmable with a logical flow (10).
8. [V] Readability grade within target (script) (5).
9. [S] Accurate — every stat/claim is supported, none fabricated (10).

*Ceiling: engagement, conversion, and SERP performance are external outcomes; the loop optimizes on-page/on-copy quality, not live metrics.*

### Lead generation (prospect list / ICP targeting / enrichment)
1. [V] Each row meets the ICP filters — industry, size, geo (checkable against criteria) (20).
2. [V] Required fields complete — name, title, company, and at least one contact channel (15).
3. [V] Email/phone pass format validation; obvious junk removed (script) (10).
4. [V] Deduplicated — no repeat people or domains (10).
5. [V] Seniority/role match — title maps to the target decision-maker set (15).
6. [S] A real personalization signal captured per lead (a genuine reason to reach out) (10).
7. [V] Source/enrichment provenance present for each field (10).
8. [S] Ranking/prioritization of the list is defensible (10).

*Ceiling and safety: format-valid ≠ deliverable/real. Email deliverability and data truth can only be confirmed by an external verification/enrichment tool — never fabricate a contact, email, or company to raise a score. If contacts came from a source, cite it; if you cannot verify, mark the field unverified rather than inventing it.*

### SEO (on-page optimization)
1. [V] Title tag present, ≤ ~60 chars, includes the primary keyword (script) (10).
2. [V] Meta description present, ~120–160 chars (script) (5).
3. [V] Exactly one H1; valid H2/H3 hierarchy (parse) (10).
4. [V] Primary keyword in H1, first 100 words, and ≥1 subhead — present but not stuffed (density in range) (10).
5. [V] All internal/external links resolve (link-check) (10).
6. [V] Every image has alt text (parse) (5).
7. [V] Structured data / JSON-LD present and valid (schema validator) (10).
8. [V] Readability grade within target (script) (10).
9. [S] Search-intent match — the content actually answers the query behind the keyword (15).
10. [S] Genuinely useful and differentiated vs the pages currently ranking — not thin (15).

*Ceiling: on-page technical factors are highly verifiable, but ranking and traffic are external, competitive, and time-lagged. The loop maximizes on-page signal quality; it cannot verify that the page will rank.*
