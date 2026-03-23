# Architecture

PolicyFlux is organized around composable abstractions for actors, bills, influence layers, and execution engines.

<div class="grid cards" markdown>

-   :material-puzzle-outline: **Composable by design**

	---

	Core abstractions are separated from concrete implementations to support controlled extension.

-   :material-layers-triple-outline: **Layered decision model**

	---

	Multiple political influences are computed independently and aggregated through configurable strategies.

-   :material-engine-outline: **Engine independence**

	---

	Deterministic and Monte Carlo execution share integration contracts and metrics outputs.

</div>

## High-level module layout

```text
policyflux/
├── core/            # abstractions, typing, contexts, strategies, DI container
├── layers/          # composable decision layers (7 built-in + neural + ERGM)
├── engines/         # deterministic + sequential/parallel Monte Carlo
├── integration/     # config, builders, presets, registry, fluent API
│   ├── builders/    # engine, congress, layer, actor, mechanics builders
│   └── presets/     # presidential, parliamentary, semi-presidential, 10 countries
├── toolbox/         # concrete actor/bill/congress/executive implementations
│   └── special_actors/  # lobbyist, whip, speaker, president
├── math_models/     # ERGM, lobbying ERGM, Tullock contest
├── model/           # TF-style Sequential + Functional model API
├── scenarios/       # comparative systems, lobbying/discipline/veto sweeps, country comparison
├── data_processing/ # text vectorization and encoding
└── utils/           # bar chart and pie chart reporting
```

## Subsystem responsibilities

| Module | Primary responsibility |
| --- | --- |
| `core` | abstract interfaces, contexts, typing, aggregation and voting strategies |
| `layers` | composable decision/influence layers (ideal point, public opinion, lobbying, media, party, agenda, neural, ERGM) |
| `engines` | deterministic and Monte Carlo execution runtime |
| `integration` | configuration, builders, presets, registry wiring, fluent API |
| `toolbox` | concrete actor, bill, congress, executive, and multi-chamber parliament implementations |
| `math_models` | mathematical models: ERGM, lobbying ERGM (bipartite), Tullock rent-seeking contest |
| `model` | TF-style model API: `Sequential`, `Model` (functional), `Input`, and model `layers` |
| `scenarios` | reusable experiment runners: comparative systems, sweeps, country parliament comparison |
| `data_processing` | text and embedding-related helpers |
| `utils` | visualization (bar charts, pie charts) and supporting runtime utilities |

## Runtime flow

1. Build `IntegrationConfig` (`direct`, `from_flat`, or presets).
2. Resolve concrete components through integration builders.
3. Evaluate active decision layers.
4. Aggregate layer outputs.
5. Apply voting strategy.
6. Collect summary metrics.

## Decision pipeline model

At a conceptual level, outcome generation follows:

$$
\text{Outcome} = \text{VotingStrategy}\left(\text{Aggregate}\left(\text{Layer}_1, \dots, \text{Layer}_n\right)\right)
$$

This separation makes it easier to test, compare, and swap individual mechanisms.

## Layer registration

`policyflux.integration.registry` enables custom layer factories:

- register with `register_layer(name, factory)`,
- build with `build_layer_by_name(name, context)`,
- configure via `LayerConfig.layer_names` and `layer_overrides`.

!!! tip "When to use custom layer registration"
	Use registry extension when default layers are not sufficient for your domain assumptions,
	but keep naming conventions stable for reproducibility across experiments.

## Design principles

- separation of concerns between abstraction and implementation,
- deterministic reproducibility via explicit configuration and seed handling,
- extension safety through centralized integration and registry contracts.

For complete internals, see [API Reference](reference/index.md).
