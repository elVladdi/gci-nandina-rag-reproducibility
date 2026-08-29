# Configuration schemas

Stable releases should validate experiment configuration before execution.

The future schema should cover at least:

- experiment identifier and mode (`reference` or `custom`);
- dataset paths and logical column mapping;
- target-code level/validation rule;
- grouping and split policy;
- retrieval strategies and parameters;
- normative corpus identity;
- optional LLM stages;
- output location;
- reference-only expected hashes.

A machine-readable schema will be added when the canonical configuration interface is frozen.
