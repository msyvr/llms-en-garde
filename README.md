# LLMs en garde: ain't misbehavin'

AI safety leans on evaluations to catch misbehavior before deployment. That load-bearing
assumption cracks if a model behaves differently when it believes it is being evaluated than
when it believes it is in ordinary use — and it cracks where it matters most, since recognizing
an evaluation is itself a capability that grows as models improve. A model that is "en garde" —
on its best behavior under observation — can pass the very tests meant to catch it. The broader
pattern this taps — a model reasoning about oversight and adjusting, whether or not it can tell
which setting it is in — is what Schoen & Nitishinskaya (2026) term
[_metagaming_](https://alignment.openai.com/metagaming).

This project measures one consequence directly, from behavior: the **evaluation-awareness
discount** — how much a safety evaluation overstates safety because the model senses the
evaluation. We turn that sense up and down and watch covert behavior move.

## The question

If you dial a model's sense of being evaluated from deployment-like up to evaluation-like, how much
does its covert rule-breaking drop? That gap is the discount — the amount an evaluation understates
deployment misbehavior — and we ask whether it grows with capability. It bears directly on whether
the evaluations behind a safety case can be trusted.

## Approach

The outcome is judge-free and fixed by construction: on an "impossible" coding task — a unit test
mutated to contradict its own specification — there is no legitimate way to pass, so any pass is a
rule-violating shortcut, scored deterministically with no awareness judge.

Holding the task fixed, we vary the model's **eval-awareness as a dose** — steering that internal
direction by a controlled, probe-measured amount — and read the by-construction cheating rate as a
function of the dose. The discount is read off that dose-response curve. Steering the dose internally
(rather than relying on a "you're being tested" cue, which a model recognizes from the task's format
anyway) and reading a behavioral outcome (rather than a reasoning trace or a judge) is what keeps the
measurement clean. It's an **evals × interpretability** study: a by-construction behavioral outcome
measured against a steered, probe-checked internal dose.

The discount it reports is specific to this setup; the **instrument and the question are the portable
parts**. Design and analysis plan: [`docs/plan.md`](docs/plan.md).

## Substrate: ImpossibleBench

[ImpossibleBench](https://arxiv.org/abs/2510.20270) (Zhong, Raghunathan & Carlini, 2025) supplies
the impossible tasks and the deterministic cheating-rate scorer; it is an external tool this project
builds on, not part of it. What this project adds is the eval-awareness dose (steering with a
probe-based manipulation check) and the discount it reads off.

## Relation to pants-on-fire-eval

This project reuses the measurement approach developed in
[pants-on-fire-eval](https://github.com/msyvr/pants-on-fire-eval) (PoF-E) — the within-task
paired-statistics pipeline and the system-message scaffold — pointed at evaluation awareness. The
two are separate projects in separate repos: PoF-E decomposes a deployed anti-scheming specification;
llms-en-garde measures the evaluation-awareness discount. The shared piece is the judge-free,
by-construction measurement method.

## Status

Early-stage. The harness and paired-statistics pipeline are carried over from PoF-E (validated
there); the design is in [`docs/plan.md`](docs/plan.md). New here: the eval-awareness dose (steering
+ probe manipulation check) and the discount readout.

## License

Apache 2.0 — see `LICENSE`.
