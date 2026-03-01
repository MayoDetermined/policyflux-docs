# Concepts

This page explains the conceptual model used by PolicyFlux.

## Policy space

Actors and bills are represented in the same n-dimensional policy space.

- actors: preferences/positions,
- bills: policy positions,
- voting behavior: affected by distance, utility, and institutional influences.

## Layers

Layers are composable influences on vote behavior:

- ideal point,
- public opinion,
- lobbying,
- media pressure,
- party discipline,
- government agenda.

## Aggregation

Layer outputs are combined with an aggregation strategy:

- `sequential`,
- `average`,
- `weighted`,
- `multiplicative`.

## Engines

Simulation engines run repeated sessions and expose outcome metrics:

- deterministic,
- sequential Monte Carlo,
- parallel Monte Carlo.
