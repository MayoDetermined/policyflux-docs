# Fluent API

PolicyFlux exposes a fluent builder through `PolicyFlux`.

Use the fluent API when you want readable, chain-based configuration in scripts and notebooks.

## Flat style

```python
from policyflux import PolicyFlux

engine = (
    PolicyFlux()
    .actors(80)
    .policy_dim(2)
    .iterations(150)
    .seed(123)
    .with_public_opinion(support=0.6)
    .presidential(approval_rating=0.62)
    .build()
)
```

This style is compact and works well for quick experiments.

## Section builders

```python
from policyflux import PolicyFlux

engine = (
    PolicyFlux.builder()
    .actors(100)
    .policy_dim(3)
    .layers().public_opinion(support=0.58).done()
    .executive().parliamentary(pm_party_strength=0.6).done()
    .special_actors().lobbyists(4, strength=0.7).done()
    .build()
)
```

Section builders are useful when you want stronger semantic grouping of configuration blocks.

## Comparison-friendly fluent pattern

```python
from policyflux import PolicyFlux

def build_with_support(support: float):
    return (
        PolicyFlux()
        .actors(120)
        .policy_dim(2)
        .iterations(250)
        .seed(99)
        .with_public_opinion(support=support)
        .parliamentary(pm_party_strength=0.6)
        .build()
    )

for support in (0.5, 0.6, 0.7):
    engine = build_with_support(support)
    engine.run()
    print(f"support={support:.1f} -> {engine.pass_rate:.1%}")
```

## Fluent API best practices

- keep one chain per scenario for readability,
- fix seed and model scale when comparing assumptions,
- extract reusable chain fragments into small functions,
- avoid mixing too many unrelated changes in one chain.

## When to prefer dataclass configs instead

Prefer `IntegrationConfig` + nested objects when:

- you need strict, explicit long-term experiment definitions,
- your team reviews configuration as structured data,
- you need straightforward serialization or version comparison.

See [Configuration](configuration.md) for explicit dataclass patterns.
