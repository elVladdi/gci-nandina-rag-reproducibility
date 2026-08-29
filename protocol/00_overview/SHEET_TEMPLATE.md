# <ID> — <Component title>

## 1. Objective
Describe the component's experimental role and what problem it solves.

## 2. Inputs
List logical inputs, schemas and required metadata.

## 3. Preconditions
State assumptions that must hold before execution.

## 4. Procedure
Describe the algorithmic procedure. Include pseudocode when prose is insufficient for an independent implementation.

## 5. Configurable parameters
List parameters, allowed ranges and reference-study values when frozen.

## 6. Invariants and prohibited information flows
State conditions that must always hold, including leakage/label-isolation constraints.

## 7. Outputs
Define outputs and link to formal schemas under `specifications/`.

## 8. Validation
Define internal checks and failure/warning behavior.

## 9. Metrics
List metrics directly associated with this component.

## 10. Acceptance criteria
Define observable conditions for considering an independent implementation conformant.

## 11. Edge cases
Document expected behavior for missing support, ties, empty retrieval, malformed inputs and other relevant cases.

## 12. Reproducibility metadata
List hashes, versions, seeds, commands or identifiers that must be recorded.

## 13. Reference-experiment parameters
If applicable, record the frozen values used by a published reference campaign without turning them into universal defaults.

## 14. Formal specifications
Link schemas/contracts from `specifications/`.

## 15. Test vectors
Link corresponding vectors under `test_vectors/<ID>/`.

## Status
`draft` | `frozen` | `superseded`
