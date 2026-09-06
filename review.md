# KinTrace Review Standard

## Must fix

Block delivery when any in-scope change:

- creates or mutates confirmed genealogy relationships without explicit researcher acceptance;
- confuses candidate/probable information with confirmed truth;
- produces incorrect deterministic kinship results;
- loses or corrupts persisted tree data;
- replaces imported/local data without the required validation and confirmation;
- introduces authentication, cloud persistence, background discovery, or other deferred scope without an approved scope change;
- exposes sensitive living-person information beyond the MVP's local-browser boundary;
- relies on FamilySearch behaviour, licensing, OAuth, or persistence assumptions that have not been verified;
- leaves the primary user journey unusable or materially broken;
- introduces serious accessibility, console, network, security, or data-integrity defects.

## Should fix

Important non-blocking issues include:

- unclear relationship or evidence labels;
- weak empty/error/loading states;
- unnecessary graph complexity;
- poor keyboard/focus behaviour;
- confusing candidate comparison;
- avoidable duplication between persisted direct relationships and derived relationships;
- unhelpful mobile tree navigation;
- unsupported abstraction or architectural complexity not required by the current ticket.

## Okay to ship

Work may be considered okay to ship from a product/review perspective only when:

- it matches the approved ticket and PRD boundaries;
- deterministic logic has relevant automated coverage;
- local persistence behaviour is checked;
- user-facing flows are inspected at relevant desktop/mobile widths when tooling permits;
- loading, empty, validation/error, and success states are handled where applicable;
- console/network behaviour is inspected when applicable;
- accessibility basics relevant to the change are checked;
- no unrelated scope or unapproved dependency is included;
- project truth documents accurately reflect actual evidence.

"Okay to ship" is not merge, deployment, or release authorization.

## Review dimensions

Review:

- product fit against `docs/PRD.md`;
- tree-centric language and flow;
- genealogy correctness;
- evidence provenance and uncertainty;
- deterministic vs probabilistic boundaries;
- data integrity and local persistence;
- living-person privacy;
- import/reset safety;
- accessibility and responsive UX;
- external-provider errors and permission boundaries;
- scope control and unnecessary complexity;
- unexpected file changes and regressions.

## Evidence and document alignment

- Plans are intended work, not implementation evidence.
- Tests/checks count only when actually run and inspected.
- `context/current-state.md` must not claim more than repository evidence supports.
- `context/architecture.md` changes only when architecture truth changes.
- `context/decisions.md` records confirmed decisions, not proposals.
- `roadmap.md` advances only when required completion evidence exists.
- `context/lessons.md` contains only repository-specific lessons observed during implementation/review.
