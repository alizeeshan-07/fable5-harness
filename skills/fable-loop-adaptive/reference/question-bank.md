# Fable Loop Question Bank

The skill is designed to minimize prompting. In the default flow there is exactly **one** required exchange (the setup gate). Ask more only when an answer would change the eval set or the loop's safety.

## The one required exchange — setup gate

Ask these together, after presenting the proposed top-10 evals:

1. Which of these 10 evals should I keep, drop, edit, or reweight?
2. What's the maximum number of iterations? (default 3–5)
3. What score should I stop early at, on the 0–100 scale — or should I just run until it converges? (default: converge)

## Only if the domain scan is genuinely ambiguous (ask at most one)

- Are you producing [X] or [Y]? They'd be scored on different things.
- Is the priority correctness, speed, polish, cost, or safety? It changes the weights.
- What's the single most important thing this must get right?

## Only if it affects the score's validity

- Can I run your tests / build / linter, or should those evals be self-judged instead?
- Is there a baseline (current behavior or prior version) I should score against?
- What input would prove this is broken? (becomes a verifiable eval)

## Mid-loop — only when forced to stop

- I've hit a plateau at [score]; [what's capping it]. Ship as-is, change approach, or spend more iterations?
- This next step is high-risk/irreversible: [decision]. How do you want me to proceed?

## After the loop — outer cycle

- Should any new failure become a permanent regression eval before the next cycle?
- Ship this, or run another cycle with the grown eval set?
