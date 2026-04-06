# Layers

Built-in layers:

- `IdealPointLayer`
- `PublicOpinionLayer`
- `LobbyingLayer`
- `MediaPressureLayer`
- `PartyDisciplineLayer`
- `GovernmentAgendaLayer`
- `LobbyingERGMPLayer` (network-aware lobbying via ERGM bipartite graphs)

Optional:

- `SequentialNeuralLayer` (PyTorch-based; requires `pip install 'policyflux[torch]'`).

## Custom composition

Use `layer_names` and `layer_overrides` to build an explicit layer pipeline.

## What each layer usually represents

| Layer | Typical mechanism | Common tuning focus |
| --- | --- | --- |
| `IdealPointLayer` | actor-bill preference proximity | baseline preference shape |
| `PublicOpinionLayer` | electorate-level pressure | `public_support` |
| `LobbyingLayer` | targeted influence pressure | `lobbying_intensity`, lobbyist count/strength |
| `MediaPressureLayer` | salience and narrative pressure | `media_pressure` |
| `PartyDisciplineLayer` | party-line conformity | `party_discipline_strength`, `party_line_support` |
| `GovernmentAgendaLayer` | agenda access/priority effects | `government_agenda_pm_strength` |
| `LobbyingERGMPLayer` | network-aware lobbying via ERGM bipartite graph | ERGM model parameters |
| `SequentialNeuralLayer` | trainable PyTorch neural layer | custom architecture factory |

## Minimal layer-first workflow

1. Start with ideal point only.
2. Add one extra layer.
3. Keep seed and aggregation fixed.
4. Compare `pass_rate` deltas.
5. Repeat for next layer.

This yields cleaner attribution than enabling many layers at once.

## Explicit order and overrides example

```python
from policyflux import IntegrationConfig, LayerConfig, build_engine

config = IntegrationConfig(
	num_actors=100,
	policy_dim=2,
	iterations=200,
	seed=7,
	layer_config=LayerConfig(
		include_ideal_point=True,
		include_public_opinion=True,
		include_party_discipline=True,
		public_support=0.6,
		party_discipline_strength=0.5,
		layer_names=[
			"ideal_point",
			"public_opinion",
			"party_discipline",
		],
		layer_overrides={
			"public_opinion": {"support": 0.62},
		},
	),
)

engine = build_engine(config)
engine.run()
print(engine.pass_rate)
```

## Neural layer notes

When using neural layers:

- install optional dependencies first,
- keep a deterministic baseline for comparison,
- document model artifacts and runtime assumptions for reproducibility.

## Troubleshooting

!!! warning "Hard-to-interpret output changes"
	Reduce active layers and reintroduce them one by one.

!!! warning "Sensitivity to layer order"
	Use explicit `layer_names` and version-control this order across experiments.
