# Configuration

PolicyFlux configuration is built around three composable objects that map directly to simulation behavior.

<div class="grid cards" markdown>

-   :material-cog-outline: **Integration-level control**

    ---

    Define global model shape, runtime strategy, and top-level execution options.

-   :material-layers-outline: **Layer-level control**

    ---

    Enable or disable political influence layers and tune their intensity.

-   :material-account-group-outline: **Actor-level control**

    ---

    Configure advanced institutional actors such as lobbyists, whips, and executive behavior.

</div>

## Configuration model

- `IntegrationConfig`
- `LayerConfig`
- `AdvancedActorsConfig`

## Scope by object

| Object | Scope | Typical use |
| --- | --- | --- |
| `IntegrationConfig` | top-level simulation settings | actor count, dimensions, iterations, seed, aggregation |
| `LayerConfig` | political influence composition | layer toggles and strengths |
| `AdvancedActorsConfig` | institutional/special actor mechanics | lobbyists, whips, executive behavior |

## IntegrationConfig

`IntegrationConfig` is the top-level container for simulation setup.

Common fields include:

- `num_actors`
- `policy_dim`
- `iterations`
- `seed`
- `layer_config`
- `actors_config`
- `aggregation_strategy`
- `aggregation_weights`

!!! tip "Reproducibility"
    Keep `seed` fixed across comparative runs to isolate the effect of configuration changes.

## LayerConfig

`LayerConfig` controls which influence layers are active and how strongly they affect decisions.

Common toggles:

- `include_ideal_point`
- `include_public_opinion`
- `include_lobbying`
- `include_media_pressure`
- `include_party_discipline`
- `include_government_agenda`
- `include_neural`

Each layer also exposes strength and intensity parameters.

Use explicit layer ordering and overrides when you need deterministic composition:

- `layer_names`
- `layer_overrides`

## AdvancedActorsConfig

`AdvancedActorsConfig` enables additional actor mechanics, such as:

- lobbyists,
- party whips,
- executive-specific behavior.

These controls are most useful in institutional comparison experiments.

## Flat configuration API

`IntegrationConfig` supports a flat configuration style that maps fields across nested configs.

```python
from policyflux import IntegrationConfig, build_engine

config = IntegrationConfig.from_flat(
    num_actors=140,
    include_public_opinion=False,
    public_support=0.63,
    n_lobbyists=2,
    lobbyist_strength=0.71,
    aggregation_strategy="average",
)

engine = build_engine(config)
engine.run()
```

You can also update an existing config:

```python
config = IntegrationConfig().with_flat(public_support=0.58, n_whips=1)
```

Unknown flat keys raise `ValueError`.

## Recommended workflow

1. Start with preset or minimal `IntegrationConfig`.
2. Introduce one layer change at a time.
3. Add advanced actor mechanics only after baseline calibration.
4. Run deterministic + Monte Carlo for both central estimate and variance.

## Practical tips

- Keep `seed` fixed to make scenario comparisons reproducible.
- Start with presets for baseline experiments, then refine with explicit configs.
- Change one layer at a time when analyzing causal effects.

For configuration examples in context, see [Getting Started](getting-started.md) and [API Overview](api-overview.md).
