# Concepts

This page explains the conceptual model used by PolicyFlux.

## Mental model

PolicyFlux simulates repeated policy decisions in a structured institutional setting:

1. actors evaluate bill positions,
2. influence layers transform baseline preferences,
3. transformed preferences are aggregated,
4. a voting strategy produces outcomes,
5. engines collect aggregate statistics.

This separation lets you test assumptions in isolation.

## Policy space

Actors and bills are represented in the same n-dimensional policy space.

- actors: preferences/positions,
- bills: policy positions,
- voting behavior: affected by distance, utility, and institutional influences.

In practice:

- lower actor-bill distance usually means higher support probability,
- higher `policy_dim` increases representational richness,
- larger spaces often require more actors/iterations for stable comparisons.

Conceptually, preference compatibility can be thought of as:

$$
\text{support tendency} \propto -\text{distance}(\text{actor position}, \text{bill position})
$$

## Layers

Layers are composable influences on vote behavior:

- ideal point,
- public opinion,
- lobbying,
- media pressure,
- party discipline,
- government agenda.

Each layer encodes a distinct mechanism. For example:

- public opinion shifts support in line with electorate pressure,
- lobbying introduces targeted directional pressure,
- party discipline constrains individual deviation,
- agenda control modifies which proposals reach final voting.

Use layer toggles to move from minimal baselines to richer institutional dynamics.

## Aggregation

Layer outputs are combined with an aggregation strategy:

- `sequential`,
- `average`,
- `weighted`,
- `multiplicative`.

Choose strategy based on your analytical objective:

- `average`: balanced and interpretable baseline,
- `weighted`: explicit relative importance across layers,
- `sequential`: ordered, path-dependent composition,
- `multiplicative`: interaction-sensitive attenuation/amplification.

Keep aggregation fixed while testing single-layer effects to preserve interpretability.

## Engines

Simulation engines run repeated sessions and expose outcome metrics:

- deterministic,
- sequential Monte Carlo,
- parallel Monte Carlo.

Deterministic runs are ideal for debugging and baseline checks.
Monte Carlo runs are better for estimating uncertainty and variance.

## Outcomes and interpretation

Primary outputs typically include:

- `pass_rate`,
- accepted and rejected counts,
- session-level summaries (backend dependent).

Interpret results comparatively:

- compare scenarios with shared seeds and scale,
- alter one variable group at a time,
- inspect both central tendency and spread.

## Concept-to-configuration map

| Concept | Typical controls |
| --- | --- |
| policy-space scale | `num_actors`, `policy_dim` |
| reproducibility | `seed` |
| institutional assumptions | preset factories, `AdvancedActorsConfig` |
| influence composition | `LayerConfig` include flags, strengths, `layer_names` |
| combination rule | `aggregation_strategy`, `aggregation_weights` |
| runtime method | deterministic vs Monte Carlo engine backends |

Continue with [Configuration](configuration.md) for parameter-level setup patterns.
