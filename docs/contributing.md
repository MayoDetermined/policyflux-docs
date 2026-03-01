# Contributing

Contributions are welcome for features, fixes, tests, and documentation.

## Development setup

```bash
git clone https://github.com/MayoDetermined/policyflux.git
cd policyflux
pip install -e ".[dev]"
```

## Quality checks

Run before opening a pull request:

```bash
pytest tests/
ruff check policyflux/
mypy policyflux/
```

## Pull request checklist

- clear scope and rationale,
- tests added or updated for behavioral changes,
- lint and type checks passing,
- documentation updated where relevant,
- changelog entry prepared if user-facing behavior changed.

## Documentation contributions

For docs-only changes:

1. edit markdown files in `docs/`,
2. update `mkdocs.yml` navigation when adding pages,
3. verify local build:

```bash
pip install mkdocs
mkdocs build --strict
```

## Code style

- keep changes minimal and focused,
- preserve compatibility with project architecture,
- prefer explicit and readable APIs,
- avoid introducing hidden side effects.
