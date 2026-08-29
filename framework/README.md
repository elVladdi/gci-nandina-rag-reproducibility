# Framework

This directory is reserved for the reusable canonical implementation extracted from the development repository after experimental stages are frozen.

The framework should contain protocol logic that is independent of one particular dataset: data validation, grouped splitting, retrieval, evaluation, integration, audit and controlled generation components.

Dataset-specific values belong in `configs/`, not as hard-coded constants here.
