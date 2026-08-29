# Canonical reproduction scripts

This directory will contain the minimal runners required to reproduce the final experimental evidence.

Planned interface:

```bash
python scripts/reproduce_all.py
```

Phase-specific scripts may also be provided, but they must consume frozen manifests/configurations and fail fast when an expected input hash does not match.

Development, exploratory and one-off diagnostic scripts belong in the source repository, not here.
