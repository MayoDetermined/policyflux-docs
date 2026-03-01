# Testing and Quality

Install development dependencies:

```bash
pip install -e ".[dev]"
```

Run tests and checks:

```bash
pytest
pytest tests/unit -m unit
pytest tests/smoke -m smoke
ruff check policyflux/
mypy policyflux/
```

Validate docs:

```bash
pip install -r requirements-docs.txt
mkdocs build --strict
mkdocs serve
```
