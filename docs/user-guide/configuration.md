# Configuration

PolicyFlux uses three main dataclasses:

- `IntegrationConfig`
- `LayerConfig`
- `AdvancedActorsConfig`

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

## LayerConfig highlights

- inclusion flags (`include_*`)
- strengths/support values (`public_support`, `media_pressure`, etc.)
- explicit ordering (`layer_names`)
- per-layer overrides (`layer_overrides`)

## AdvancedActorsConfig highlights

- executive type and executive-specific parameters
- lobbyists and whips
- speaker agenda support

## Environment settings

`Settings` reads environment variables prefixed with `POLICYFLUX_`.
