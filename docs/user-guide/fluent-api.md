# Fluent API

PolicyFlux exposes a fluent builder through `PolicyFlux`.

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
