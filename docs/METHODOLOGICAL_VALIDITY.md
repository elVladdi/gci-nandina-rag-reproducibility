# Methodological validity criteria

An external replication is not valid merely because software runs successfully. It should preserve the experimental controls required by the published protocol.

## Minimum criteria

A replication should:

- define the analysis unit and grouping unit;
- declare jurisdiction, nomenclature, version and target code depth;
- document normative-corpus provenance and hierarchy coverage;
- preserve group independence across historical/development/evaluation partitions when dependent observations exist;
- prevent true evaluation labels and derived label information from entering retrieval, ranking or generation inputs;
- use the same evaluation instances for direct comparisons among strategies;
- avoid tuning on the final evaluation partition;
- record input/configuration/corpus identities and hashes where feasible;
- preserve case-level outputs required to recalculate aggregate metrics;
- report historical-support and normative-coverage limitations;
- report exact/near-duplicate diagnostics when relevant;
- document all deviations from the reference specification.

## Reference compatibility versus external validity

A replication can be methodologically valid without reproducing the reference study's numerical results. Differences in jurisdiction, taxonomy, data composition, historical support, corpus coverage and implementation stack may lead to different outcomes.

Direct numerical comparisons should therefore be interpreted only after documenting these factors.

## Conformance evidence

Where available, protocol-sheet test vectors provide component-level conformance evidence. Passing test vectors does not by itself establish scientific validity; dataset integrity, split design and experimental isolation must also be demonstrated.
