# Results

This directory will hold approved expected outputs associated with frozen reference releases and, where appropriate, templates for custom-run result manifests.

Intended reference contents include:

- machine-readable metric summaries;
- report tables and figures derived from the experimental study;
- artifact manifests;
- compact reference outputs needed for regression checks.

Custom replications should write to separate experiment-specific directories and must not overwrite frozen reference artifacts.

Every retained result artifact must be traceable to a framework/source commit, input/configuration hashes and an explicit experiment identifier.
