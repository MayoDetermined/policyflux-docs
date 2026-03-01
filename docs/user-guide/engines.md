# Engines

Available engine backends:

- `DeterministicEngine`
- `SequentialMonteCarlo`
- `ParallelMonteCarlo`

Standard usage:

```python
from policyflux import build_engine

engine = build_engine(config)
engine.run()
```
