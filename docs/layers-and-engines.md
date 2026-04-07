# Layers and Engines

PolicyFlux models voting outcomes by combining composable layers with a selected execution engine.

This section explains how influences are composed, how aggregation affects interpretation,
and how to choose execution engines for exploratory work versus robust comparative studies.

## Quick mental model

- Layers define *why* actor preferences shift.
- Aggregation defines *how* those shifts combine.
- Engine defines *how often and at what scale* the simulation is executed.

Keeping these concerns separate makes experiments easier to explain and reproduce.

## Decision layers

Layers transform baseline vote probabilities based on political and institutional effects.

Core layers include:

- ideal point,
- public pressure,
- lobbying,
- media pressure,
- party discipline,
- government agenda,
- neural layers (optional dependencies).

### What each layer typically controls

| Layer family | Typical effect | Practical use |
| --- | --- | --- |
| Ideal point | spatial preference alignment | baseline ideological consistency |
| Public opinion | mass support pressure | sensitivity to electorate mood |
| Lobbying | targeted influence by groups | elite pressure experiments |
| Media pressure | narrative amplification/suppression | attention and framing hypotheses |
| Party discipline | alignment with party line | caucus cohesion analysis |
| Government agenda | executive/speaker agenda shaping | institutional gatekeeping studies |

You usually get the most interpretable results by activating a small subset first,
then adding one layer at a time.

## Decision pipeline

At a high level:

$$
\text{Vote propensity} = \text{Aggregate}(\text{Layer}_1, \dots, \text{Layer}_n)
$$

$$
\text{Outcome} = \text{VotingStrategy}(\text{Vote propensity})
$$

### Layer composition

1. Each active layer computes its contribution for actor-bill pairs.
2. Contributions are merged through the configured aggregation strategy.
3. The resulting vote propensity feeds the voting strategy.

### Order effects

When using order-sensitive aggregation (for example sequential composition),
`layer_names` can change outcomes even with identical strengths.

Use order sensitivity intentionally:

1. define and document one canonical layer order,
2. run one alternative order as a stress test,
3. report whether conclusions survive the order change.

## Aggregation strategy

The aggregation strategy defines how layer outputs are combined.

Common approach:

- average-based aggregation,
- optional custom weights via `aggregation_weights`.

!!! tip "Comparability"
	Keep aggregation strategy fixed while testing single-layer changes to preserve interpretability.

### Strategy interpretation guide

| Strategy | Interpretation | When to use |
| --- | --- | --- |
| `average` | each active layer contributes evenly | first baselines and broad comparisons |
| `weighted` | some mechanisms are explicitly more influential | theory-driven studies with justified priors |
| `sequential` | downstream layers act on already-shifted preferences | process-oriented institutional pipelines |
| `multiplicative` | interaction can magnify or dampen effects | nonlinear synergy/antagonism hypotheses |

For publication-quality runs, store your aggregation strategy and weight vector
alongside every result artifact.

## Engines

PolicyFlux provides multiple execution backends:

- deterministic engine,
- sequential Monte Carlo,
- parallel Monte Carlo,
- session management utilities.

## Engine selection guide

| Engine type | Best for | Trade-off |
| --- | --- | --- |
| Deterministic | baseline checks, reproducible debugging | no distributional uncertainty |
| Sequential Monte Carlo | medium-sized sweeps, easier traceability | slower than parallel at scale |
| Parallel Monte Carlo | large comparative experiments | higher resource usage |

### Deterministic engine

Use when you want a single reproducible run with fixed inputs.

### Monte Carlo engines

Use when you want distributions of outcomes across repeated sessions:

- sequential for simpler debugging and predictable runtime behavior,
- parallel for faster large sweeps on multi-core systems.

## Experiment patterns

### Pattern A: Baseline then intervention

1. Build a baseline with one stable seed.
2. Change only one layer parameter (for example `public_support`).
3. Re-run with the same seed.
4. Compare pass-rate delta and accepted/rejected counts.

### Pattern B: Institutional comparison

1. Use two presets (for example parliamentary vs presidential).
2. Hold `num_actors`, `policy_dim`, `iterations`, and seed constant.
3. Run deterministic or sequential Monte Carlo.
4. Compare central tendency and spread.

### Pattern C: Robustness sweep

1. Fix one configuration.
2. Run a seed range.
3. Track min/max/mean pass rate.
4. Promote to parallel Monte Carlo when sample size grows.

## Performance and scaling

If runtime grows quickly, scale in stages:

1. prototype with small `num_actors` and `iterations`,
2. validate behavior at medium scale,
3. run final large sweeps only after sanity checks.

Practical tips:

- prefer deterministic engine while debugging config correctness,
- switch to sequential Monte Carlo for traceable repeated runs,
- switch to parallel Monte Carlo for throughput after validation.

## Reproducibility checklist

Before comparing outcomes, ensure these are controlled:

- seed policy (fixed or explicitly sampled),
- actor count and policy dimensionality,
- active layer set and layer order,
- aggregation strategy and weights,
- engine type and session count.

If one of these changes, treat it as a new experiment family, not a direct continuation.

## Runtime output

After `run()`, engines expose summary metrics such as:

- pass rate,
- accepted bills,
- rejected bills,
- run/session-level aggregates.

For concrete configuration knobs, see [Configuration](configuration.md).

## Example: deterministic vs Monte Carlo readout

```python
from statistics import mean

from policyflux import build_engine, create_presidential_config

# Deterministic baseline
cfg = create_presidential_config(num_actors=100, policy_dim=2, iterations=200, seed=42)
eng = build_engine(cfg)
eng.run()
print(f"deterministic pass_rate={eng.pass_rate:.1%}")

# Small robustness sweep
rates = []
for seed in range(42, 52):
    cfg = create_presidential_config(num_actors=100, policy_dim=2, iterations=200, seed=seed)
    eng = build_engine(cfg)
    eng.run()
    rates.append(eng.pass_rate)

print(f"mc min={min(rates):.1%}, max={max(rates):.1%}, mean={mean(rates):.1%}")
```

Use deterministic output for debugging and directional checks, then use repeated runs
to quantify uncertainty before drawing comparative conclusions.
