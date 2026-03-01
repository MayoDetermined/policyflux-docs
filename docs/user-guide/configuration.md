# Configuration

PolicyFlux uses three main dataclasses:

- `IntegrationConfig`
- `LayerConfig`
- `AdvancedActorsConfig`

## Configuration hierarchy

`IntegrationConfig` is the root container and typically embeds:

- one `LayerConfig` (influence mechanics),
- one `AdvancedActorsConfig` (institutional/special actors),
- global runtime and reproducibility settings.

Think of setup as:

```text
IntegrationConfig
├── model scale (num_actors, policy_dim, iterations)
├── reproducibility (seed)
├── aggregation (strategy, optional weights)
├── layer_config: LayerConfig
└── actors_config: AdvancedActorsConfig
```

## Flat setup

```python
from policyflux import IntegrationConfig

config = IntegrationConfig.from_flat(
    num_actors=120,
    policy_dim=3,
    include_public_opinion=True,
    public_support=0.62,
    n_lobbyists=3,
    lobbyist_strength=0.7,
    aggregation_strategy="average",
)
```

Unknown flat keys raise `ValueError`.

Use flat setup when you need compact scripts or tabular parameter sweeps.
Use explicit nested objects when you want maximal readability and long-term maintainability.

## Explicit nested setup

```python
from policyflux import IntegrationConfig, LayerConfig, AdvancedActorsConfig

config = IntegrationConfig(
    num_actors=120,
    policy_dim=3,
    iterations=300,
    seed=42,
    aggregation_strategy="average",
    layer_config=LayerConfig(
        include_ideal_point=True,
        include_public_opinion=True,
        include_lobbying=True,
        public_support=0.62,
        lobbyist_strength=0.7,
    ),
    actors_config=AdvancedActorsConfig(
        n_lobbyists=3,
    ),
)
```

## LayerConfig highlights

- inclusion flags (`include_*`)
- strengths/support values (`public_support`, `media_pressure`, etc.)
- explicit ordering (`layer_names`)
- per-layer overrides (`layer_overrides`)

Recommended workflow:

1. Start with minimal layer set.
2. Add one mechanism at a time.
3. Keep `aggregation_strategy` unchanged while isolating effects.
4. Introduce explicit ordering only if order dependence is part of your hypothesis.

## AdvancedActorsConfig highlights

- executive type and executive-specific parameters
- lobbyists and whips
- speaker agenda support

Use this config for institutional realism after baseline model calibration.
For early prototyping, keep it minimal to reduce confounders.

## Aggregation strategy guidelines

| Strategy | Best for | Caveat |
| --- | --- | --- |
| `average` | stable baseline comparisons | smooths strong single-layer effects |
| `weighted` | explicit layer importance assumptions | requires principled weight design |
| `sequential` | order-sensitive pipelines | less intuitive cross-study comparisons |
| `multiplicative` | interaction-heavy assumptions | can amplify instability |

If you use `weighted`, document and version-control your weight vector.

## Validation and error handling

Common sources of configuration errors:

- unknown keys in `from_flat(...)`,
- inconsistent assumptions (for example enabling a layer without meaningful strength/support values),
- overly large parameter combinations that make exploratory runs slow.

Practical checks before long runs:

1. build config,
2. run a short test (`iterations` reduced),
3. validate metrics shape and magnitude,
4. scale up.

## Reproducible experiment template

```python
from policyflux import build_engine, IntegrationConfig

def run_once(seed: int, public_support: float) -> float:
    config = IntegrationConfig.from_flat(
        num_actors=120,
        policy_dim=2,
        iterations=250,
        seed=seed,
        include_public_opinion=True,
        public_support=public_support,
        aggregation_strategy="average",
    )
    engine = build_engine(config)
    engine.run()
    return engine.pass_rate

baseline = run_once(seed=100, public_support=0.55)
intervention = run_once(seed=100, public_support=0.65)
print(f"delta={intervention - baseline:+.2%}")
```

## Environment settings

`Settings` reads environment variables prefixed with `POLICYFLUX_`.

Use environment variables for CI and deployment-specific behavior,
and keep experiment logic in versioned Python configuration code.
