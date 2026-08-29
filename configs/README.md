# Configurations

This directory separates frozen reference presets from reusable examples for external replication.

Structure:

- `presets/` — immutable configurations tied to identifiable experimental reference releases;
- `examples/` — templates for user-provided datasets;
- `schemas/` — machine-readable configuration contracts when frozen.

Rules:

- copy a reference configuration only after the corresponding experimental Gate is closed;
- preserve source commit and SHA-256 in the release manifest;
- do not keep exploratory variants here;
- never modify a released reference preset in place; create a new version;
- custom configurations must map logical dataset roles instead of depending on fixed source-column names.
