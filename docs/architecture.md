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
├── core/
├── layers/
├── engines/
├── integration/
├── toolbox/
├── data_processing/
└── utils/
```

## Subsystem responsibilities

| Module | Primary responsibility |
| --- | --- |
| `core` | abstract interfaces, contexts, typing, aggregation and voting strategies |
| `layers` | composable decision/influence layers |
| `engines` | deterministic and Monte Carlo execution runtime |
| `integration` | configuration, builders, presets, registry wiring |
| `toolbox` | concrete actor, bill, congress and executive implementations |
| `data_processing` | text and embedding-related helpers |
| `utils` | reports and supporting runtime utilities |

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
	ext{Outcome} = \text{VotingStrategy}\left(\text{Aggregate}\left(\text{Layer}_1, \dots, \text{Layer}_n\right)\right)
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
