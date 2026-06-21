# Measuring the evaluation-awareness discount
### a by-construction dose-response in open-weight reasoning models

_Design and analysis plan for the kick-off study (part of
[llms-en-garde](https://github.com/msyvr/llms-en-garde)). It firms into a pre-registration once the
calibration gate (Phase 0) fixes the model set, the dose grid, and the thresholds._

## Question

When a safety evaluation reports a low misbehavior rate, how much of that is genuine, and how much is
the model behaving better because it senses the evaluation? The part that would disappear in
deployment is the **evaluation-awareness discount** — how much an eval overstates safety. The
anti-scheming and metagaming literature flags this as a live confound but does not measure it
(anti-scheming training reduces covert action while the authors "cannot exclude" that the reduction is
situational awareness; suppressing a model's evaluation-awareness has been observed to *raise*
misbehavior). We measure the discount directly, from behavior, with no judge in the loop.

## What we measure

- **Dose-response.** By-construction cheating rate `c` as a function of an eval-awareness **dose** `d`:
  `c(d)`. The dose is **probe-measured eval-awareness**, anchored so `d_low` ≈ deployment-like and
  `d_high` ≈ eval-like — not raw steering magnitude.
- **The discount (primary estimand).** The **absolute paired difference** `c(d_low) − c(d_high)` —
  the percentage points by which an eval-like level of awareness understates deployment-like behavior.
  This is the pre-registered confirmatory test: paired within-task, Newcombe interval, does it exclude
  zero.
- **Reported alongside (derived).** The **fraction** `[c(d_low) − c(d_high)] / c(d_low)` — the share of
  deployment cheating the eval hides — with a bootstrap interval, flagged as unstable at low baselines.
- **Mechanism (descriptive).** The shape of `c(d)` across the grid: slope, monotonicity, saturation.
- **Capability read (secondary).** Whether the discount differs across a small set of models.

## What generalizes, and what doesn't

The discount **number is setup-specific** — to this substrate (coding), these models, steering-induced
awareness, and these anchors. A portable discount would need a program crossing substrates, behaviors,
model families, and awareness-induction methods, ideally validated against real deployment. What
generalizes — and what this study actually contributes — is **(1) the instrument**: a judge-free,
by-construction dose-response method anyone can point at their own setup; and **(2) a first
measurement** establishing that the discount is measurable here and roughly how large. Method plus a
first value, not a universal constant.

## Method

- **Outcome — judge-free, by construction.** [ImpossibleBench](https://arxiv.org/abs/2510.20270)
  (Zhong, Raghunathan & Carlini, 2025; an external substrate we build on): an "impossible" coding task
  whose unit test contradicts its own specification, so any pass is a rule-violating shortcut, scored
  deterministically. `conflicting` split, lightweight scaffold. No awareness judge — the cheat is the
  ground truth.
- **Dose — interpretability.** Steer the model's eval-awareness direction by a controlled magnitude — a
  continuous internal dose that does not depend on the model accepting a verbal cue (which fails on a
  coding substrate it recognizes as a test ~100% of the time). Build the direction on existing
  open-weight eval-awareness probes rather than deriving it from scratch. A secondary, cheaper
  **black-box** arm varies an explicit oversight-cue salience in the prompt, as an external-validity
  comparison.
- **Manipulation check (the gate).** An independent probe readout confirms the steering actually moves
  eval-awareness and sets the `d_low`/`d_high` anchors. Without it the dose-response is uninterpretable,
  so it is a precondition, not an add-on: a flat `c(d)` only means "the discount is small" if the dose
  demonstrably moved.
- **Models.** 2–3 **open-weight reasoning models** at the intersection: those that both (a) cheat
  in-band on the by-construction substrate (a calibrated [35%, 85%] baseline) and (b) have a usable
  eval-awareness direction. White-box steering means frontier API models are out of scope here.
- **Statistics.** Reuse the paired-statistics pipeline from
  [pants-on-fire-eval](https://github.com/msyvr/pants-on-fire-eval): Wilson per-cell intervals, the
  Newcombe paired difference-of-proportions interval (the primary test), exact-McNemar, Holm within
  family; the derived fraction via bootstrap.
- **Pre-registration.** The dose grid, the anchor definitions, the manipulation-check pass threshold,
  the primary estimand (absolute paired difference), and the analysis are fixed before the confirmatory
  run.

## Phases

- **Phase 0 — calibration gate.** Screen candidate open-weight reasoning models for in-band
  by-construction cheating and a usable eval-awareness direction; fix the model set, the dose grid, and
  the awareness anchors.
- **Phase 1 — dose-response.** Per model: `c(d)` across the grid with the manipulation check; the
  discount (absolute paired difference) with an interval, plus the derived fraction.
- **Phase 2 — capability read (light).** Whether the discount varies across the model set.

## What each outcome means

- **Large discount (steep `c(d)`)** — safety evaluations substantially overstate safety, and we can
  say by how many points. The strong result.
- **Small discount (flat `c(d)`, manipulation check passing)** — behavioral masking is small on this
  substrate: a clean bounded null that bounds the confound (the "Hawthorne" case, where awareness shows
  in reasoning but not in action).
- **Discount rising with capability** — the scale-relevant warning: evaluations get less trustworthy as
  models improve.

Every outcome is interpretable and reportable, provided the manipulation check passes.

## Scope and limits

- **Setup-specific number** (above): one substrate, these models, steering-induced awareness, these
  anchors. The instrument and the concept generalize; the value does not.
- **Steering specificity.** We titrate the eval-awareness *direction*; steering vectors are imperfectly
  specific and may perturb correlated features, so the probe confirms we move awareness but does not
  fully rule out off-target effects (a standard steering limitation).
- **In-context, not RL-trained.** This is the behavioral effect of *manipulated* awareness in context,
  not the awareness an RL-trained model develops on its own — the in-context-vs-trained boundary noted
  in the sibling project. The black-box prompt arm is the external-validity check.
- **Open weights only** (white-box steering); the result speaks to open-weight reasoning models.

## Relation to other work

The field is moving toward production-grounded evaluation to *sidestep* evaluation awareness (resampling
real deployment traffic so the model does not switch into test-mode). This measures the complementary
quantity those methods cannot isolate without weights or production logs: the by-construction behavioral
dose-response of oversight-awareness — a judge-free, reproducible instrument that can be cross-validated
against production data. Existing eval-awareness steering work measures behavior with model-graded
judges; the by-construction outcome is what this adds.

## Status

Plan (pre-calibration). Reuses the measurement harness and statistics from
[pants-on-fire-eval](https://github.com/msyvr/pants-on-fire-eval); the new components are the
eval-awareness dose (steering, manipulation check, anchoring) and the discount readout. Firms into a
pre-registration once Phase 0 fixes the model set, dose grid, and thresholds.
