# Test vectors

This directory will contain redistributable input/output cases and invariants for validating independent implementations of protocol sheets.

Recommended layout:

```text
test_vectors/<SHEET-ID>/
├── README.md
├── input/
├── expected/
└── manifest.json
```

A test vector should identify the protocol-sheet version it validates, the input schema, expected deterministic outputs or invariants, numerical tolerances when needed, and artifact hashes.

Test vectors validate conformance to the published specification; they do not expose or embed private reference source code.