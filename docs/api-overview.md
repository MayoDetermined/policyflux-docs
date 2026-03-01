# API Overview

This page maps the public PolicyFlux API from fast onboarding to advanced composition.

<div class="grid cards" markdown>

-   :material-rocket-launch: **Fast start**

    ---

    Build a simulation in minutes with presets and `build_engine(config)`.

-   :material-tune-variant: **Config-first control**

    ---

    Use `IntegrationConfig`, `LayerConfig`, and `AdvancedActorsConfig`
    to tune institutional and behavioral dynamics.

-   :material-hammer-wrench: **Advanced composition**

    ---

    Drop to low-level builders and registries when extending internals.

</div>

## Recommended import pattern

Prefer root imports from `policyflux` for forward compatibility.

## Primary imports

```python
from policyflux import (
    build_engine,
    IntegrationConfig,
    LayerConfig,
    AdvancedActorsConfig,
    PolicyFlux,
    create_presidential_config,
    create_parliamentary_config,
    create_semi_presidential_config,
)
```

## Typical usage flow

1. Choose a preset (`create_*_config`) or build config objects explicitly.
2. Build engine with `build_engine(config)`.
3. Execute simulation via `engine.run()`.
4. Read outcome metrics (`pass_rate`, accepted/rejected counts).

## Main entry points

- `IntegrationConfig` - top-level simulation parameters.
- `LayerConfig` - layer activation and influence intensities.
- `AdvancedActorsConfig` - lobbyists, whips, executive-specific settings.
- `build_engine(config)` - engine construction from integrated config.
- `create_*_config(...)` - institutional presets for rapid comparisons.
- `PolicyFlux` - fluent builder for chain-based configuration.

## Presets vs custom configuration

| Approach | Best for | Trade-off |
| --- | --- | --- |
| Presets (`create_*_config`) | quick comparative experiments | less explicit control |
| Dataclass configs | reproducible studies and explicit assumptions | more verbose setup |
| Fluent `PolicyFlux` builder | compact scripts and notebooks | API literacy required |

## Fluent API preview

```python
from policyflux import PolicyFlux

engine = (
    PolicyFlux()
    .actors(120)
    .policy_dim(2)
    .iterations(250)
    .with_public_opinion(support=0.61)
    .presidential(approval_rating=0.58)
    .build()
)

engine.run()
print(engine.pass_rate)
```

## Low-level integration builders

- Use these only when integrating custom mechanics or extending internal workflows.

- `build_session(...)`
- `build_bill(...)`
- `build_congress(...)`
- `build_layers(...)`

!!! tip "Need symbol-level API details?"
    Continue to [API Reference](reference/index.md) for module-by-module documentation.

## Full API details

See [API Reference](reference/index.md) for symbol-level documentation.
