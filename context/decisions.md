# Decisions

## Confirmed

### Product name: KinTrace

**Source:** Product definition / `docs/PRD.md`  
**Consequence:** Use KinTrace as the product name and project identity.

### Tree-centric model

**Source:** `docs/PRD.md`  
The researcher does not need to be represented in the tree. Any person may be the starting/display root.

### No KinTrace authentication in MVP

**Source:** `docs/PRD.md`  
No user accounts, login, collaboration, or account-management system is in scope.

### Browser localStorage persistence

**Source:** `docs/PRD.md`  
MVP project data persists in the researcher's browser. Export/import are required because localStorage is not a reliable backup.

### User-triggered discovery only

**Source:** `docs/PRD.md`  
The researcher explicitly initiates external genealogy searches from a selected person. No automatic/background discovery in MVP.

### Evidence before certainty

**Source:** `docs/PRD.md`  
External candidates do not become confirmed tree members without researcher acceptance. Confirmed, probable, possible, conflicting, and rejected states remain distinguishable.

### Derive extended relationships

**Source:** `docs/PRD.md`  
Persist primitive direct graph relationships and deterministically derive sibling, grandparent, aunt/uncle, cousin, and relationship paths where possible.

### FamilySearch as initial intended provider

**Source:** `docs/PRD.md`  
Integrate through a provider abstraction rather than coupling the domain model directly to FamilySearch.

### AI is not the source of genealogy truth

**Source:** `docs/PRD.md`  
AI may support explanation, record extraction, and conflict interpretation but must not silently create/merge people or override deterministic relationship calculations.

## Unresolved

- exact runtime/frontend stack;
- exact graph rendering approach;
- exact client state architecture;
- FamilySearch production API access;
- FamilySearch commercial/licensing constraints;
- FamilySearch OAuth requirements;
- what FamilySearch data may legally/contractually be persisted in localStorage;
- whether FamilySearch requires a server-side integration component;
- exact approach to living-person privacy beyond local-only MVP storage;
- exact confidence-scoring method for identity resolution;
- exact handling of multiple conflicting facts for the same life event;
- exact browser support target and accessibility baseline;
- exact test/build/tooling commands once implementation is scaffolded.

## Historical

No superseded project decisions are recorded yet.
