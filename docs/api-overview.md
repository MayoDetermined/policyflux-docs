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

## One-liner runners

Run a complete simulation in a single call and get raw vote counts back:

```python
from policyflux import run_presidential, run_parliamentary, run_semi_presidential

votes = run_presidential(num_actors=100, policy_dim=2, iterations=200, seed=42)
passed = sum(1 for v in votes if v > 50)
print(f"Pass rate: {passed / len(votes):.1%}")
```

Available: `run_presidential`, `run_parliamentary`, `run_semi_presidential`.

Engine builders (build but do not run):

```python
from policyflux import parliamentary_engine

engine = parliamentary_engine(num_actors=150, seed=7)
votes = engine.run()
```

Available: `presidential_engine`, `parliamentary_engine`, `semi_presidential_engine`.

Default config constants: `PRESIDENTIAL_DEFAULT`, `PARLIAMENTARY_DEFAULT`, `SEMI_PRESIDENTIAL_DEFAULT`.

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

## TF-style model API

A TensorFlow/Keras-inspired interface for constructing simulations as layer graphs.

Sequential API:

```python
from policyflux.model import Sequential
from policyflux.model import layers as L

model = Sequential(num_actors=100, policy_dim=2)
model.add(L.IdealPoint())
model.add(L.PublicOpinion(support=0.6))
model.add(L.PartyDiscipline(strength=0.5, line_support=0.7))

model.compile(executive="presidential", aggregation="sequential")
results = model.run(iterations=200, seed=42)
model.summary()
```

Functional API:

```python
from policyflux.model import Model, Input
from policyflux.model import layers as L

bill = Input(policy_dim=2, num_actors=100)
x = L.IdealPoint()(bill)
x = L.PublicOpinion(support=0.6)(x)
x = L.Lobbying(intensity=0.4)(x)

model = Model(inputs=bill, outputs=x)
model.compile(executive="parliamentary", aggregation="average")
results = model.run(iterations=200, seed=42)
```

## Multi-chamber parliament presets

Country-specific parliament models with realistic chamber sizes and veto structures:

```python
from policyflux.integration.presets.parliament_presets import (
    create_uk_parliament,
    create_us_congress,
    list_presets,
)
from policyflux.toolbox import SequentialBill

print(list_presets())  # all available country keys

uk = create_uk_parliament()    # Commons (650) + Lords (800), suspensive veto
us = create_us_congress()      # House (435) + Senate (100), full veto

bill = SequentialBill()
bill.make_random_position(dim=2)
result = uk.cast_votes(bill)
print(f"Passed: {result.passed}, Rounds: {result.rounds}")
```

Available countries: UK, US, Germany, France, Italy, Poland, Sweden, Spain, Australia, Canada.

## Scenario runners

```python
from policyflux.scenarios import comparative_systems, lobbying_sweep, country_comparison

# Compare presidential vs parliamentary vs semi-presidential
results = comparative_systems.run(num_actors=100, iterations=300, seed=42)
for r in results:
    print(f"{r.system}: {r.passage_rate:.1%}")

# Sweep lobbying intensity from 0 to 1
points = lobbying_sweep.run(num_actors=100, n_steps=10, seed=42)
for p in points:
    print(f"Intensity {p.lobbying_intensity:.1f}: {p.passage_rate:.1%}")

# Country parliament comparison
results = country_comparison.run(n_bills=30, policy_dim=2, seed=42)
for r in results:
    print(f"{r.parliament_name}: {r.passage_rate:.1%}")
```

Available: `comparative_systems`, `lobbying_sweep`, `party_discipline_sweep`,
`veto_player_sweep`, `country_comparison`.

## Mathematical models

Access via `import_models()` (lazy import to avoid heavy dependencies at startup):

```python
from policyflux import import_models

math = import_models()

# Tullock rent-seeking contest
contest = math.TullockContest(n_contestants=5, prize_value=1.0, r=0.5)
contest.set_expenditure(0, 0.3)
probs = contest.compute_win_probabilities()

# ERGM network generation
ergm = math.ExponentialRandomGraphModel(n_nodes=20)
adjacency = ergm.generate(seed=42)
print(f"Density: {ergm.get_density():.3f}")

# Lobbying ERGM (bipartite lobbyist-legislator network)
lobby_net = math.LobbyingERGMPModel(n_lobbyists=5, n_legislators=50)
lobby_net.generate(seed=42)
print(f"Avg lobbyist reach: {lobby_net.get_average_lobbyist_reach():.1f}")
```

## Visualization utilities

```python
from policyflux import bake_a_pie, craft_a_bar

bake_a_pie(votes, num_actors=100, title="Presidential System")
craft_a_bar(votes, title="Vote Distribution")
```

## Layer registry

Register and use custom layers:

```python
from policyflux import register_layer, LAYER_REGISTRY, build_layer_by_name

register_layer("my_layer", lambda ctx: MyCustomLayer(ctx))
layer = build_layer_by_name("my_layer", context)
```

## Seed and RNG utilities

```python
from policyflux import set_seed, get_rng

set_seed(42)          # set global seed
rng = get_rng()       # access the global RNG instance
```

## Low-level integration builders

Use these only when integrating custom mechanics or extending internal workflows.

- `build_session(...)`
- `build_bill(...)`
- `build_congress(...)`
- `build_layers(...)`
- `build_executive(...)`
- `build_advanced_actors(...)`

!!! tip "Need symbol-level API details?"
    Continue to [API Reference](reference/index.md) for module-by-module documentation.

## Full API details

See [API Reference](reference/index.md) for symbol-level documentation.
