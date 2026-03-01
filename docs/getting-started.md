# Getting Started

This guide takes you from installation to a validated first simulation in under 10 minutes.

<div class="grid cards" markdown>

-   :material-lightning-bolt: **Fast onboarding**

    ---

    Install, run a baseline simulation, and confirm results with a minimal script.

-   :material-flask-outline: **Research-ready defaults**

    ---

    Start from reproducible parameters (`seed`, fixed dimensions, stable layer toggles).

-   :material-rocket-launch-outline: **Next-step paths**

    ---

    Move directly to API, configuration, and architecture depending on your role.

</div>

## Prerequisites

- Python 3.10+
- pip

!!! tip "Use a virtual environment"
    For clean dependency management, create and activate a dedicated venv before installation.

## Installation

From PyPI:

```bash
pip install policyflux
```

From source:

```bash
git clone https://github.com/MayoDetermined/policyflux.git
cd policyflux
pip install -e .
```

Optional extras:

```bash
# Neural layers
pip install -e ".[torch]"

# Text encoders
pip install -e ".[text-encoders]"

# Notebook/examples support
pip install -e ".[examples]"

# Developer tooling
pip install -e ".[dev]"
```

## Verify installation

```bash
python -c "import policyflux; print(policyflux.__version__)"
```

## Minimal simulation

```python
from policyflux import build_engine, IntegrationConfig, LayerConfig

config = IntegrationConfig(
    num_actors=50,
    policy_dim=2,
    iterations=100,
    seed=12345,
    layer_config=LayerConfig(
        include_ideal_point=True,
        include_public_opinion=True,
        include_party_discipline=True,
        public_support=0.60,
        party_discipline_strength=0.5,
    ),
)

engine = build_engine(config)
engine.run()

print(f"Pass rate: {engine.pass_rate:.1%}")
print(f"Accepted: {engine.accepted_bills}, Rejected: {engine.rejected_bills}")
```

## Quick comparative run (preset-based)

```python
from policyflux import (
    build_engine,
    create_parliamentary_config,
    create_presidential_config,
)

presidential = create_presidential_config(num_actors=100, policy_dim=2, iterations=200, seed=42)
parliamentary = create_parliamentary_config(num_actors=100, policy_dim=2, iterations=200, seed=42)

eng_a = build_engine(presidential)
eng_b = build_engine(parliamentary)

eng_a.run()
eng_b.run()

print(f"Presidential pass rate: {eng_a.pass_rate:.1%}")
print(f"Parliamentary pass rate: {eng_b.pass_rate:.1%}")
```

## What to inspect after run

- `engine.pass_rate`
- `engine.accepted_bills`
- `engine.rejected_bills`

These metrics provide a baseline before adding more complex layers and actor mechanics.

## Development commands

```bash
pytest
ruff check policyflux/
mypy policyflux/
mkdocs serve
```

## Next

Choose your next path:

- API surface and entry points: [API Overview](api-overview.md)
- Configuration depth: [Configuration](user-guide/configuration.md)
- Internals and module map: [Architecture](architecture.md)
