# KinTrace MVP Implementation Specification

**Source ticket:** `tickets/TBD-generate-implementation-spec-from-prd.md`  
**Product source of truth:** `docs/PRD.md`  
**Status:** Specified  
**Scope:** KinTrace MVP technical contract only

---

## 1. Purpose

Define the implementation contract for the KinTrace MVP described in `docs/PRD.md` without expanding product scope or resolving decisions that remain explicitly unresolved.

The implementation must preserve these confirmed boundaries:

- tree-centric, not user-centric;
- no KinTrace authentication in MVP;
- browser `localStorage` persistence;
- user-triggered discovery only;
- external candidates never become confirmed tree data without explicit researcher acceptance;
- extended family relationships are derived deterministically from the graph;
- FamilySearch is the initial intended genealogy provider behind an abstraction;
- AI may assist research but is not a source of genealogical truth.

This specification does not authorize runtime implementation, dependency changes, publication, merge, deployment, release, or resolution of FamilySearch legal/access/OAuth/persistence questions.

---

## 2. Repository State and Constraints

Current repository evidence shows no KinTrace runtime/application implementation yet. The PRD defines product behavior and intended architecture boundaries, while the exact frontend/runtime stack, graph rendering approach, state architecture, schema validation approach, browser support target, and verification commands remain unresolved.

Therefore this specification defines behavior, domain contracts, state boundaries, integration seams, failure behavior, and verification expectations without selecting unsupported libraries or frameworks.

Any later plan that chooses a runtime stack, dependency, backend/proxy, OAuth design, or FamilySearch persistence behavior must treat that as a separate material implementation decision and obtain the required approval.

---

## 3. Domain Model

### 3.1 FamilyTree

```ts
interface FamilyTree {
  id: string;
  name: string;
  rootPersonId: string;
  createdAt: string;
  updatedAt: string;
}
```

The root is the current display root only and has no genealogical privilege. It may be changed to any confirmed person.

### 3.2 Person

```ts
interface Person {
  id: string;
  names: {
    first: string;
    middle?: string;
    surname: string;
    previousSurname?: string;
  };
  sex?: "male" | "female" | "unknown";
  birth?: {
    date?: string;
    approximate?: boolean;
    place?: string;
  };
  death?: {
    date?: string;
    approximate?: boolean;
    place?: string;
  };
  isLiving?: boolean;
  externalIds?: {
    familySearch?: string;
  };
  createdAt: string;
  updatedAt: string;
}
```

The implementation must support arbitrary starting persons and must not assume the researcher is represented in the tree.

### 3.3 Relationship

Persist only primary graph relationships:

```ts
interface Relationship {
  id: string;
  personAId: string;
  personBId: string;
  type: "parent" | "spouse";
  status: "confirmed" | "probable" | "possible";
  evidenceIds: string[];
  createdAt: string;
  updatedAt: string;
}
```

Derived relationships such as sibling, grandparent, aunt/uncle, first cousin, and other kinship paths must not be redundantly persisted where they can be derived from parent-child graph structure.

A sibling entry created through the UI must be represented through appropriate direct graph relationships rather than a persisted `sibling` edge.

### 3.4 Evidence

```ts
interface Evidence {
  id: string;
  source: "user" | "familysearch" | "other";
  type: "birth" | "marriage" | "death" | "census" | "relationship" | "other";
  externalRecordId?: string;
  personIds: string[];
  relationshipIds: string[];
  extractedData?: Record<string, unknown>;
  sourceUrl?: string;
  createdAt: string;
}
```

Evidence provenance must be retained. External evidence and manually supplied evidence must remain distinguishable.

### 3.5 Discovery

```ts
interface Discovery {
  id: string;
  targetPersonId: string;
  type: "person_match" | "possible_parent" | "possible_spouse" | "possible_child" | "record";
  confidence?: number;
  status: "pending" | "accepted" | "rejected" | "unsure";
  externalData: Record<string, unknown>;
  evidenceIds: string[];
  createdAt: string;
}
```

Discovery records are candidate research findings. They must remain separate from confirmed graph truth until explicit acceptance.

---

## 4. Application State and Persistence

The MVP state contract is versioned and stored entirely in browser `localStorage`.

```ts
interface KinTraceState {
  version: 1;
  tree: FamilyTree | null;
  persons: Person[];
  relationships: Relationship[];
  evidence: Evidence[];
  discoveries: Discovery[];
}
```

Preferred storage key from the PRD:

```text
kintrace:v1
```

### 4.1 Persistence behavior

- On launch, load and validate persisted state before presenting the active tree.
- If no valid tree exists, show the empty-state flow for creating a starting person.
- Every successful in-scope mutation must persist automatically.
- No global Save action is required.
- Invalid or unreadable persisted data must not be silently interpreted as valid state.
- Persistence errors must surface as user-visible errors without destroying the last known valid in-memory state.

### 4.2 State mutation boundary

Confirmed graph state may change only through explicit user actions such as:

- create starting person;
- add/edit a person;
- add/remove a direct relationship;
- accept a discovery;
- import a validated snapshot after confirmation;
- reset after confirmation.

Candidate search results, AI output, or external provider responses must not directly mutate confirmed graph state.

---

## 5. Primary User Flows

### 5.1 Launch and tree creation

1. Load local state.
2. If a tree exists, open it.
3. Otherwise present `Create a family tree` with `Add starting person`.
4. Collect supported person fields from the PRD.
5. Create the first `Person`, create a `FamilyTree`, set `rootPersonId`, and persist.

### 5.2 Add known relative

From a selected person, the researcher may add:

- parent;
- spouse;
- child;
- sibling.

For parent, spouse, and child, create the new person and corresponding direct graph edge.

For sibling, create or connect the required shared parent relationship(s) rather than storing a sibling edge. If the relationship cannot be represented without an unresolved parent identity, the UI must make that ambiguity explicit rather than inventing a parent.

### 5.3 Edit person

Editing may modify supported person fields. It must not silently alter unrelated relationships or evidence.

### 5.4 Remove incorrect manually entered relationship

The researcher may remove a direct manually entered relationship. The implementation must remove only the selected edge and allow derived relationship results to update deterministically from the remaining graph.

### 5.5 Select and centre on a person

Selecting a person opens their detail context and exposes:

- name;
- birth/death information;
- parents;
- spouse(s);
- children;
- evidence count;
- missing information;
- Edit;
- Add relative;
- Find relatives.

The researcher can recenter the tree on any confirmed person without changing genealogical meaning.

### 5.6 Relationship explorer

The researcher selects two confirmed people and asks how they are related. The system returns:

- relationship name when supported;
- graph path;
- common ancestor when relevant.

The result must be computed from the confirmed graph only.

---

## 6. Deterministic Relationship Engine

The implementation must provide deterministic equivalents of:

```ts
getParents(personId)
getChildren(personId)
getSiblings(personId)
getGrandparents(personId)
getAuntsAndUncles(personId)
getFirstCousins(personId)
findCommonAncestor(personAId, personBId)
findRelationship(personAId, personBId)
```

### 6.1 Rules

- Parent-child graph structure is authoritative for lineage traversal.
- Spouse edges may contribute to labels/paths but must not fabricate blood relationships.
- Derived kinship is calculated from the graph and not written back as redundant persistent edges.
- Relationship calculations must be deterministic and reproducible for the same graph state.
- AI or external provider confidence must never override deterministic graph results.

### 6.2 Minimum covered outcomes

Tests must cover representative paths for:

- parent/child;
- sibling;
- grandparent/grandchild;
- aunt/uncle and niece/nephew where supported by the chosen relationship naming implementation;
- first cousin;
- common ancestor;
- unrelated/disconnected people;
- graph updates after removing an edge;
- duplicate/cyclic-invalid relationship prevention according to the later implementation plan's validation rules.

The exact naming coverage beyond the PRD's required operations must not be expanded speculatively.

---

## 7. Import, Export, and Reset

### 7.1 Export

Export one versioned KinTrace JSON snapshot containing:

- tree;
- persons;
- relationships;
- evidence;
- discoveries.

The exported file must preserve the schema version.

### 7.2 Import

Before replacing local state, the application must:

1. read the selected file;
2. parse JSON;
3. validate required schema structure;
4. verify supported `version`;
5. reject malformed or unsupported input;
6. present a replacement confirmation;
7. replace state only after explicit confirmation;
8. persist the accepted imported state.

Failed validation must leave existing local data unchanged.

### 7.3 Reset

Reset is destructive and must require explicit confirmation before clearing the active KinTrace state.

---

## 8. Discovery and Evidence Review

### 8.1 Trigger boundary

Discovery occurs only after the researcher selects a person and explicitly chooses `Find relatives` or manually initiates search.

No background or automatic genealogy search is permitted in the MVP.

### 8.2 Search intent

Supported discovery targets include:

- parents;
- spouse;
- children;
- other records.

The query may use already-known person information including name, approximate birth year, birthplace, spouse, parents, children, and known residences where available.

### 8.3 Candidate model

External results must be presented as candidates. Candidate presentation should support:

- identifying information;
- confidence when available;
- explanation of matching and conflicting attributes;
- review action.

Confidence is advisory only.

### 8.4 Review outcomes

The researcher must be able to choose:

- Confirm match;
- Not the same person;
- Not sure.

These map to accepted, rejected, and unsure discovery states.

### 8.5 Acceptance mutation

On explicit acceptance:

- external evidence may be attached;
- accepted missing confirmed information may be added;
- accepted relatives may be added to the confirmed graph;
- only the explicitly accepted changes may affect confirmed state.

The implementation must not interpret acceptance of one fact as blanket acceptance of unrelated external fields.

---

## 9. Identity Resolution Boundary

Candidate matching may consider:

- name similarity;
- birth date/year;
- birthplace;
- spouse;
- parents;
- children;
- residence;
- historical chronology.

The exact confidence-scoring algorithm is unresolved and must not be invented in this specification.

Any confidence value is decision support for the researcher, never confirmation authority.

Conflicting information must remain reviewable and must not be silently collapsed into a single fact.

---

## 10. FamilySearch Provider Boundary

External genealogy access must be encapsulated behind a provider abstraction equivalent to:

```ts
interface GenealogyProvider {
  searchPeople(query: PersonSearchQuery): Promise<PersonCandidate[]>;
  getPerson(id: string): Promise<ExternalPerson>;
  getRelationships(id: string): Promise<ExternalRelationship[]>;
  getRecords(id: string): Promise<ExternalRecord[]>;
}
```

The domain and UI must not depend directly on FamilySearch-specific response shapes.

### 10.1 Unresolved before implementation

The following remain hard unresolved gates and must not be decided implicitly:

- FamilySearch production API access;
- commercial-use/licensing constraints;
- OAuth requirements;
- permissions/scopes;
- whether integration can be client-only or requires a backend/proxy;
- what retrieved FamilySearch data may be persisted in `localStorage` and for how long.

Until these are resolved, implementation planning may define a provider seam and mocked/test-double behavior, but must not claim a production-ready FamilySearch integration.

---

## 11. AI Boundary

AI is optional supporting infrastructure only.

Allowed uses from the PRD:

- candidate/match explanation;
- historical record extraction;
- conflict explanation.

AI must not:

- silently create people;
- silently merge identities;
- invent relationships;
- override deterministic relationship calculations;
- convert unsupported assumptions into confirmed facts.

Any AI-generated structured result used by later implementation must be validated before application logic consumes it.

---

## 12. Main UI Responsibilities

### 12.1 Tree

Must support:

- confirmed family graph display;
- pan/zoom;
- person selection;
- centre on person;
- branch expand/collapse where implemented;
- add relative;
- edit person;
- start discovery;
- basic relationship information.

### 12.2 Person Detail

Side/detail panel responsibilities:

- person summary;
- direct family;
- evidence count;
- missing information;
- edit/add-relative/find-relative actions.

### 12.3 Person Profile

Detailed person history may expose the PRD's suggested sections:

- Overview;
- Life;
- Family;
- Records;
- Sources.

Because these sections are described as suggested, later implementation must not treat every section as a mandatory first-slice requirement unless the approved plan includes it.

### 12.4 Discover

Must keep pending/unresolved candidate findings separate from confirmed tree truth.

Categories such as strong candidates, possible matches, unresolved findings, and conflicts are permitted but not required taxonomy unless the implementation plan explicitly chooses them.

### 12.5 Search

Manual genealogy search may expose the PRD's suggested fields. Exact field layout is an implementation detail.

### 12.6 Records

Central evidence access may categorize supported record types. Exact navigation is an implementation detail.

### 12.7 Settings

MVP settings responsibilities include:

- FamilySearch connection boundary;
- export;
- import;
- reset;
- default tree root;
- default generation depth.

No KinTrace account management belongs here.

---

## 13. Validation and Error Behavior

The implementation plan must define concrete validation rules for the selected stack, but these behavior-level requirements are mandatory:

- required person creation fields cannot produce an invalid person record;
- relationship endpoints must reference existing people;
- malformed persisted/imported state must be rejected safely;
- unsupported state versions must be rejected safely;
- failed import must not alter current state;
- failed external-provider requests must not alter confirmed graph state;
- candidate review failures must preserve the previous discovery/confirmed state;
- destructive reset/import replacement requires confirmation;
- user-facing errors should identify recoverable next actions without exposing sensitive data.

If the implementation detects graph data that would make deterministic traversal invalid, it must fail safely rather than return fabricated kinship.

---

## 14. Privacy and Security Constraints

Living-person information is privacy-sensitive.

For MVP:

- KinTrace project data remains in the researcher's browser;
- no public profiles;
- no tree sharing;
- no collaboration;
- no central KinTrace user database;
- no KinTrace authentication/account system.

Later implementation must not introduce network transmission of local living-person data except where explicitly required by an approved external genealogy search flow and consistent with resolved provider permissions/data rules.

Secrets, OAuth credentials, or provider tokens must not be stored in ordinary persisted KinTrace project data.

---

## 15. Non-Goals

Do not include:

- KinTrace user authentication/accounts;
- cloud persistence;
- multi-device sync;
- collaboration or invitations;
- public profiles;
- DNA matching;
- automatic/background discovery;
- autonomous relationship creation;
- Ancestry integration;
- Findmypast integration;
- GRO scraping;
- native mobile apps;
- social or messaging features;
- image restoration;
- automatic photo recognition;
- production-grade GEDCOM interoperability.

---

## 16. Implementation Slices for Planning

The later TDD implementation plan should preserve the roadmap order and prefer vertical, independently verifiable slices:

1. Local family-tree foundation.
2. Interactive tree exploration.
3. Deterministic relationship engine.
4. Local data safety: export/import/reset.
5. Evidence and discovery review model using local/mock provider data first where necessary.
6. FamilySearch provider integration only after unresolved access/licensing/OAuth/persistence gates are resolved.
7. Full MVP verification against PRD success criteria.

This ordering is planning guidance derived from the existing roadmap; it does not itself authorize implementation.

---

## 17. Acceptance Contract

The implementation satisfies this specification only when repository and verification evidence show that a researcher can:

- create a tree around any starting person;
- add/edit people and direct family relationships;
- reload without losing the valid local tree;
- navigate/select/recentre the confirmed tree;
- derive required extended relationships deterministically;
- safely export a complete versioned snapshot;
- reject malformed/unsupported imports without data loss;
- confirm before import replacement/reset;
- explicitly trigger discovery;
- review multiple candidates and supporting/conflicting evidence;
- accept a discovery and update only explicitly accepted confirmed state;
- reject a discovery without changing confirmed graph truth;
- determine the relationship between two confirmed people;
- keep candidate/external/AI output separate from confirmed genealogical truth until human acceptance.

FamilySearch production integration is not considered satisfied until its unresolved access, licensing, OAuth, permissions, architecture, and persistence constraints are separately resolved and verified.

---

## 18. Verification Contract

Exact commands remain unresolved until runtime/tooling exists. The later implementation plan must discover and record repository-defined commands before claiming verification.

At minimum, implementation verification must include:

### Automated

- domain/state mutation tests;
- persistence serialization/loading tests;
- import validation/version/replacement tests;
- deterministic relationship-engine tests for representative and edge paths;
- discovery acceptance/rejection tests proving confirmed-state boundaries;
- provider abstraction tests independent of FamilySearch-specific payloads.

### User-flow / browser verification

Where browser tooling is available, inspect relevant desktop and mobile states for:

- launch with empty state;
- existing-tree load;
- add/edit relative;
- select/recentre;
- relationship explorer;
- export/import validation and confirmation;
- discovery loading/empty/error/success states;
- accept/reject/unsure candidate flows;
- reset confirmation;
- console/network errors;
- accessibility basics including keyboard/focus behavior relevant to implemented controls.

### Review blockers

Delivery must be blocked if implementation:

- mutates confirmed genealogy without explicit acceptance;
- confuses candidate information with confirmed truth;
- returns incorrect deterministic kinship;
- loses/corrupts persisted tree data;
- replaces imported/local data without validation and confirmation;
- introduces deferred scope without approval;
- exposes living-person information beyond the approved local-browser boundary;
- relies on unresolved FamilySearch assumptions;
- introduces serious accessibility, console, network, security, or data-integrity defects.

---

## 19. Traceability

This specification is derived from:

- `docs/PRD.md` for product behavior, data concepts, flows, constraints, non-goals, and success criteria;
- `AGENTS.md` for delivery, permissions, state distinctions, and review/verification boundaries;
- `roadmap.md` for ordered MVP outcomes;
- `review.md` for blocking and non-blocking review criteria;
- `context/current-state.md`, `context/architecture.md`, and `context/decisions.md` for current repository state and unresolved architecture/product decisions;
- `spec/README.md` for specification conventions.

No unresolved product or FamilySearch decision has been intentionally promoted to a confirmed implementation requirement in this specification.
