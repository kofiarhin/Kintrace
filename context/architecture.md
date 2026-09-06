# Architecture

## Intended

The PRD defines a local-first client application with these intended boundaries:

- browser persistence through `localStorage`;
- no KinTrace authentication/account system in the MVP;
- a normalized family graph centred on `FamilyTree`, `Person`, `Relationship`, `Evidence`, and `Discovery` domain concepts;
- persist direct parent/spouse relationships rather than redundant derived kinship edges;
- deterministic relationship-engine operations for parents, children, siblings, grandparents, aunts/uncles, cousins, common ancestors, and relationship paths;
- evidence/discovery candidates remain separate from confirmed tree truth until explicit researcher acceptance;
- FamilySearch is the initial intended external genealogy source;
- external genealogy access should be hidden behind a `GenealogyProvider` abstraction;
- AI, if added, may explain/extract/compare but must not own genealogy truth or override deterministic relationship logic.

## Implemented

No application/runtime implementation exists in the repository.

Current repository evidence consists of:

- `README.md`;
- `docs/PRD.md`;
- AI Dev Workspace operating documentation on the workspace setup branch.

## Verified

No application architecture behaviour has been verified because runtime implementation does not yet exist.

The merged PRD is verified to exist on `main`.

## Constraints

Confirmed product constraints:

- no KinTrace auth in the MVP;
- local browser persistence;
- user-triggered discovery only;
- explicit human confirmation before external candidates affect confirmed tree truth;
- living-person data must remain within the MVP's local-browser privacy boundary;
- import must validate schema/version and require confirmation before replacing existing data;
- deterministic kinship calculation must not be delegated to AI;
- FamilySearch legal/access/OAuth/data-persistence constraints must be verified before implementation relies on them.

## Unresolved

- frontend framework and exact runtime stack are not specified by the PRD;
- exact graph visualization library, if any;
- state-management approach;
- exact schema-validation library/format;
- whether FamilySearch integration can be implemented purely client-side or requires a backend/proxy;
- FamilySearch production access and commercial/licensing conditions;
- OAuth flow requirements;
- which FamilySearch data may be persisted locally and for how long;
- exact automated test, lint, type-check, and build commands once runtime scaffolding exists.
