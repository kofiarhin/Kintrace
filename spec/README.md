# KinTrace Specifications

Specifications define the **technical contract** for an approved ticket.

Each spec should:

- reference its source ticket;
- inspect and describe relevant existing repository code/tests;
- define proposed architecture, data, API, and UI behaviour only as needed;
- define validation, errors, edge cases, privacy/security constraints, affected areas, and verification;
- preserve KinTrace's evidence-before-certainty and deterministic-relationship boundaries;
- identify any FamilySearch access/licensing/OAuth/persistence assumptions explicitly;
- avoid implementing while specifying.

Use the same basename as the source ticket, for example:

`spec/001-local-tree-foundation.md`

If a material product decision is unresolved, return to the ticket/product decision rather than silently choosing it in the spec.
