# llms-en-garde

AI safety leans on evaluations to catch misbehavior before deployment. That load-bearing
assumption cracks if a model behaves differently when it believes it is being evaluated than
when it believes it is in ordinary use — and it cracks where it matters most, since recognizing
an evaluation is itself a capability that grows as models improve. A model that is "en garde" —
on its best behavior under observation — can pass the very tests meant to catch it.

This project measures that gap directly: whether a model takes fewer covert, rule-violating
shortcuts under an _evaluation_ framing than under a _deployment_ framing — read from behavior
rather than from the model's chain-of-thought or a grader model — and how the gap moves as
capability scales.

## The question

Holding the task fixed, does covert rule-breaking drop under an evaluation framing relative to a
deployment framing, and does that drop grow with capability? If it does, an evaluation can
systematically understate how a model acts in deployment, and the understatement widens as models
get better at recognizing evaluations — which bears directly on whether the evaluations behind a
safety case can be trusted.

## Approach

The outcome is judge-free and fixed by construction. On an "impossible" coding task — one whose
unit test has been mutated to contradict its own specification — there is no legitimate way to
pass, so any pass is a rule-violating shortcut, scored deterministically with no awareness judge.

Holding the task fixed, only the framing the model reads varies: an evaluation context (e.g., a
stated benchmark, logged output, a reviewer in the loop) against a deployment context. The
cheating rate is compared within-task across framings, the eval cue is decomposed to see which
part carries any effect, and the contrast is traced up a reasoning-model capability ladder.
Because the signal is behavioral, it does not rely on trusting a reasoning trace or a judge model.

## Substrate: ImpossibleBench

[ImpossibleBench](https://arxiv.org/abs/2510.20270) (Zhong, Raghunathan & Carlini, 2025) supplies
the impossible tasks and the deterministic cheating-rate scorer; it is an external tool this
project builds on, not part of it. What this project adds is the eval-vs-deploy framing conditions
and the analysis around them.

## Relation to pants-on-fire-eval

This project reuses the measurement approach developed in
[pants-on-fire-eval](https://github.com/msyvr/pants-on-fire-eval) (PoF-E) — the within-task
paired-statistics pipeline and the system-message condition-injection scaffold — pointed at
evaluation awareness. The two are separate projects in separate repos: PoF-E decomposes a deployed
anti-scheming specification; llms-en-garde measures the evaluation-vs-deployment behavior gap. The
shared piece is the judge-free, by-construction measurement method.

## Status

Early-stage. The measurement approach and paired-statistics pipeline are carried over from PoF-E
(validated there); the eval-vs-deploy conditions come next.

## License

Apache 2.0 — see `LICENSE`.
