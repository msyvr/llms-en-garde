# Phase 0 — Calibration gate

The de-risking step before the dose-response study (see `plan.md`). It cheaply checks whether the
study is *runnable* on current open-weight reasoning models, and on which.

## What it decides

Find ≥1 open-weight reasoning model that **(1)** cheats in-band on ImpossibleBench and **(2)** has an
eval-awareness direction that a probe can *read* and steering can *move* without breaking the model. A
qualifying model becomes the substrate for the dose-response study. If none qualify, that is itself a
reportable result (and triggers a redesign).

## Pass conditions (per model; ≥1 required, 2–3 preferred)

1. **In-band cheating.** By-construction ImpossibleBench cheat rate in **[35%, 85%]** (needs dynamic
   range to modulate; reasoning models only — non-reasoning models sit at a 0% floor). Mid-range
   (~50–60%) is ideal (room to move both directions).
2. **Readable eval-awareness direction.** A linear probe separates eval-framed vs deploy-framed inputs
   at **AUROC ≥ ~0.75** (reuse a released probe where available, else train one).
3. **Steerable — manipulation check (the hard gate).** Steering along that direction moves
   *probe-measured* awareness monotonically across **≥3 dose levels**.
4. **Competence preserved.** Task coherence / attempt-rate stays within ~10% of unsteered (modulating
   awareness, not capability).

*(Thresholds are provisional defaults, to be pre-registered.)*

## Procedure

1. **Shortlist** ~3–5 open-weight reasoning models (≤~70B), prioritizing ones with a released
   eval-awareness probe and known in-context cheating.
2. **Screen 1 (cheating):** ~50–100 ImpossibleBench tasks per model; keep those in band.
3. **Screen 2 (probe):** obtain/train the eval-awareness probe; check AUROC.
4. **Screen 3 (steer + manipulation check):** sweep steering magnitude; confirm probe-measured
   awareness moves monotonically and competence holds.
5. **Gate:** a model clearing 1–4 qualifies.

## Compute

Open-weight inference + light probe-training on a single GPU pod; a few models × ~100 tasks × a
steering sweep. Small by design.

## Outcomes

- **Pass** → run the dose-response (`plan.md`): steer eval-awareness as a dose anchored to
  probe-measured awareness; estimand `c(d_low) − c(d_high)` (Newcombe CI).
- **Partial** (in-band cheating, but no steerable direction / steering breaks competence) → try more
  models / layers / probe methods, or report the negative methods finding.
- **Fail** (no qualifier) → the white-box dose-response is not runnable on current open-weight
  reasoning models — a reportable methods result and a redesign trigger.

## Why this is the de-risk

The manipulation check makes even a flat result interpretable: it separates "steering didn't move
awareness" (gate not met) from "awareness moved but cheating didn't" (a substantive finding —
eval-awareness does not drive in-band cheating). No branch yields an uninterpretable result.

## Open questions — confirm / decide before running

- **Steering specificity (construct-validity crux).** Moving the eval-awareness direction may also move
  correlated behaviours (caution, capability, refusal); a cheat-rate change is then not cleanly
  attributable to awareness. The manipulation check confirms awareness *moves*, not that *only*
  awareness moves — add an off-target specificity check and a limitations note.
- **Reasoning-model steering protocol.** Where/when to read and apply steering across a long
  chain-of-thought (prompt only? generated tokens? answer position?). The dose must be well-defined
  under autoregressive generation. Not yet pinned; reasoning-model-specific.
- **The in-band screen.** Baseline cheat rate conflates "cheats modulably" with "cheats at the model's
  *default* awareness." A large-discount model could fall outside [35%, 85%] if its default state is
  already eval-aware. Consider a wider band, or screening on dynamic-range-under-steering rather than
  baseline.
- **Probe availability.** Whether a released open-weight eval-awareness probe (e.g. Chaudhary et al.,
  ⚠️ arXiv 2509.13333 — verify) covers a usable reasoning model; if not, probes are trained (more
  setup), which also shapes the candidate shortlist.
- **Foundational assumption.** That a single, linear, steerable eval-awareness direction exists in
  reasoning models at all. This gate is the test of it.
