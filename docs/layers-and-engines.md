# Layers and Engines

PolicyFlux models voting outcomes by combining composable layers with a selected execution engine.

<div class="grid cards" markdown>

-   :material-layers-triple-outline: **Layered influence model**

	---

	Political and institutional effects are applied as independent, composable transforms.

-   :material-function-variant: **Configurable aggregation**

	---

	Layer outputs are merged by strategy and optional custom weighting.

-   :material-chip: **Engine flexibility**

	---

	Run deterministic baselines or Monte Carlo sweeps without changing model semantics.

</div>

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

## Decision pipeline

At a high level:

$$
	ext{Vote propensity} = \text{Aggregate}(\text{Layer}_1, \dots, \text{Layer}_n)
$$

$$
	ext{Outcome} = \text{VotingStrategy}(\text{Vote propensity})
$$

### Layer composition

1. Each active layer computes its contribution for actor-bill pairs.
2. Contributions are merged through the configured aggregation strategy.
3. The resulting vote propensity feeds the voting strategy.

## Aggregation strategy

The aggregation strategy defines how layer outputs are combined.

Common approach:

- average-based aggregation,
- optional custom weights via `aggregation_weights`.

!!! tip "Comparability"
	Keep aggregation strategy fixed while testing single-layer changes to preserve interpretability.

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

## Runtime output

After `run()`, engines expose summary metrics such as:

- pass rate,
- accepted bills,
- rejected bills,
- run/session-level aggregates.

For concrete configuration knobs, see [Configuration](configuration.md).
