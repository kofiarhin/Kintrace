# KinTrace MVP Full Implementation Plan

**Source ticket:** `tickets/TBD-generate-implementation-spec-from-prd.md`  
**Source spec:** `spec/TBD-generate-implementation-spec-from-prd.md`  
**Status:** Planned  
**Execution model:** Vertical slices with RED → GREEN → REFACTOR → VERIFY  
**Approved baseline:** React + Vite + TypeScript + Tailwind CSS; Vitest + React Testing Library; Zod for schema/state validation; React Flow for tree visualization; TanStack Query only for remote/provider state; no Redux by default; no backend initially; FamilySearch behind a provider interface with mock/test implementation until production integration gates are resolved.

---

## 1. Goal

Implement the KinTrace MVP described by the source specification as a local-first genealogy research application that:

- creates and edits a family tree around any starting person;
- persists confirmed local state in browser `localStorage`;
- stores only primitive parent/spouse graph relationships and derives extended kinship deterministically;
- supports interactive tree exploration;
- safely imports, exports, and resets local state;
- keeps evidence/discovery candidates separate from confirmed tree truth;
- supports explicit, user-triggered genealogy discovery through a provider abstraction;
- never allows AI/provider confidence to silently create or confirm genealogical truth;
- remains within the MVP privacy boundary and explicit non-goals.

This plan does not resolve FamilySearch licensing, production access, OAuth, permissions, proxy/backend, or persistence-policy questions. Production FamilySearch activation remains a gated follow-on step.

---

## 2. Architectural Baseline

### Client

- React
- latest Vite
- TypeScript
- Tailwind CSS
- React Router if multiple top-level views require routing
- React Flow for interactive family graph rendering
- TanStack Query for provider/search requests only
- local deterministic application/domain state through a small typed store/reducer layer; no Redux unless complexity later proves it necessary

### Validation

- Zod schemas for:
  - KinTrace persisted state;
  - import payloads;
  - domain mutation inputs;
  - provider-facing normalized data where applicable.

### Testing

- Vitest
- React Testing Library
- deterministic domain/unit tests for graph and relationship logic
- component/integration tests for user-visible workflows
- browser-level verification after implementation where tooling permits

### Persistence

- `localStorage`
- state key: `kintrace:v1`
- versioned root state
- validated load before use
- automatic persistence after successful confirmed-state mutations
- import/reset replacement only after explicit confirmation

### Provider Boundary

Define a typed `GenealogyProvider` contract and use a mock/test provider for MVP implementation until FamilySearch production constraints are resolved.

No production FamilySearch token/OAuth handling is implemented in the baseline plan.

---

## 3. Proposed Repository Shape

Exact paths may be adjusted to fit Vite conventions during scaffolding, but the implementation should remain close to:

```text
src/
  app/
    App.tsx
    routes.tsx
    providers.tsx
  domain/
    types.ts
    schemas.ts
    ids.ts
    graph/
      graph.ts
      relationshipEngine.ts
      relationshipLabels.ts
      validation.ts
    mutations/
      people.ts
      relationships.ts
      discoveries.ts
      evidence.ts
  state/
    kintraceStore.ts
    persistence.ts
    selectors.ts
  features/
    tree/
      TreePage.tsx
      FamilyGraph.tsx
      PersonNode.tsx
      PersonDetailPanel.tsx
      AddRelativeDialog.tsx
      EditPersonDialog.tsx
    relationship-explorer/
      RelationshipExplorer.tsx
    discover/
      DiscoverPage.tsx
      CandidateCard.tsx
      CandidateReview.tsx
    search/
      SearchPage.tsx
    records/
      RecordsPage.tsx
    settings/
      SettingsPage.tsx
      ImportExport.tsx
      ResetTree.tsx
  integrations/
    genealogy/
      GenealogyProvider.ts
      types.ts
      mock/
        MockGenealogyProvider.ts
  test/
    factories.ts
    fixtures.ts
    setup.ts
```

Do not create architectural layers that are not required by the slices below.

---

## 4. Global Invariants

These invariants apply to every slice and should have automated coverage where practical.

1. A discovery/provider response cannot directly mutate confirmed graph state.
2. Only explicit researcher actions may create/edit/delete confirmed people or relationships.
3. Persist only `parent` and `spouse` relationship edges.
4. Sibling/grandparent/aunt/uncle/cousin/path results are derived.
5. Same graph state produces the same deterministic relationship result.
6. Malformed or unsupported persisted/imported data is rejected safely.
7. Failed imports never replace current state.
8. Reset and import replacement require confirmation.
9. FamilySearch-specific response shapes never leak into domain/UI contracts.
10. Living-person data stays local except when explicitly included in an approved provider search.
11. No KinTrace auth, cloud persistence, collaboration, background discovery, or autonomous relationship creation.
12. Errors must preserve the last known valid state and provide recoverable user feedback where possible.

---

# Phase 0 — Runtime Scaffolding and Verification Harness

## Slice 0.1 — Scaffold the client application

**Outcome**

A minimal React/Vite/TypeScript application runs with Tailwind, test tooling, lint/type/build scripts, and no product behavior yet.

**Affected areas**

- root `package.json`
- Vite/TypeScript config
- Tailwind config
- `src/`
- test setup

**RED**

Add a smoke test expecting the app shell to render “KinTrace”.

**GREEN**

Create the smallest Vite React TypeScript app that satisfies the smoke test.

**REFACTOR**

Keep root application composition minimal; avoid adding unused dependencies or abstractions.

**VERIFY**

Run the repository-defined equivalents of:

```bash
npm test
npm run lint
npm run typecheck
npm run build
```

Record exact commands after scaffolding.

## Slice 0.2 — Establish shared domain contracts and schemas

**Outcome**

Typed domain models and Zod schemas exist for `FamilyTree`, `Person`, `Relationship`, `Evidence`, `Discovery`, and `KinTraceState`.

**RED**

Tests reject:

- missing required identifiers;
- invalid relationship type;
- unsupported discovery status;
- unsupported state version;
- relationship endpoints with malformed IDs at schema level where applicable.

**GREEN**

Add domain TypeScript types and matching Zod schemas.

**REFACTOR**

Keep schemas aligned with the approved spec; do not add unsupported fields.

**VERIFY**

Unit tests + typecheck.

---

# Phase 1 — Local Family-Tree Foundation

## Slice 1.1 — Empty-state launch and starting person creation

**Outcome**

With no valid saved tree, KinTrace shows the create-tree empty state and lets the researcher add an arbitrary starting person.

**RED**

Component/integration tests prove:

- empty state renders when storage is empty;
- required person fields are validated;
- a successful starting-person submission creates one `Person` and one `FamilyTree`;
- `rootPersonId` points at the created person;
- no “Me/My father/My mother” user-centric assumptions appear.

**GREEN**

Implement minimal form, tree creation mutation, and local state update.

**REFACTOR**

Extract form validation/input normalization only when duplication appears.

**VERIFY**

Tests + manual browser check for empty, validation, success states.

## Slice 1.2 — Validated localStorage load/save

**Outcome**

KinTrace automatically persists valid state and restores it on reload.

**RED**

Tests cover:

- valid saved state loads;
- malformed JSON is rejected;
- valid JSON with invalid schema is rejected;
- unsupported version is rejected;
- successful mutation persists;
- persistence write failure surfaces an error while keeping valid in-memory state.

**GREEN**

Implement `persistence.ts` with typed load/save result handling.

**REFACTOR**

Separate serialization/validation from UI notifications.

**VERIFY**

Unit tests + reload browser verification.

## Slice 1.3 — Add/edit people and direct relationships

**Outcome**

From a selected person, the researcher can add parent, child, spouse, and edit person details.

**RED**

Tests cover:

- add parent creates a person and direct parent edge;
- add child creates equivalent parent edge orientation correctly;
- add spouse creates spouse edge;
- edit person changes only selected fields;
- relationship endpoints must exist;
- duplicate direct relationship is rejected;
- illegal self-relationship is rejected.

**GREEN**

Implement people/relationship mutations and minimal dialogs.

**REFACTOR**

Centralize graph mutation validation.

**VERIFY**

Domain tests + UI integration tests + persistence verification.

## Slice 1.4 — Add sibling without persisted sibling edge

**Outcome**

Sibling entry is represented via shared parent relationships, never a `sibling` relationship record.

**RED**

Tests cover:

- existing shared parent can connect new sibling correctly;
- no `sibling` type is persisted;
- when sibling creation would require inventing an unknown parent, the UI surfaces an explicit ambiguity rather than fabricating one.

**GREEN**

Implement shared-parent sibling flow.

**REFACTOR**

Reuse direct parent mutation primitives.

**VERIFY**

Domain and UI tests.

## Slice 1.5 — Remove incorrect direct relationship

**Outcome**

The researcher can remove one manually entered direct relationship without deleting unrelated people/evidence.

**RED**

Tests prove:

- only selected edge is removed;
- unrelated edges remain;
- derived relationship results update after edge removal;
- persistence reflects the new graph.

**GREEN**

Implement relationship removal with confirmation if UX requires it.

**REFACTOR**

Keep deletion behavior edge-scoped.

**VERIFY**

Unit/integration tests.

---

# Phase 2 — Interactive Tree Exploration

## Slice 2.1 — Render confirmed graph

**Outcome**

React Flow renders confirmed people and primitive graph edges from local state.

**RED**

Tests cover mapping domain state to nodes/edges without inserting discoveries as confirmed nodes.

**GREEN**

Implement graph adapter and basic React Flow canvas.

**REFACTOR**

Keep React Flow-specific structures outside domain state.

**VERIFY**

Component tests + browser render check.

## Slice 2.2 — Select person and show detail panel

**Outcome**

Selecting a node opens the Person Detail panel with required direct-family/evidence/missing-info summary and actions.

**RED**

Tests cover panel contents and selected-person changes.

**GREEN**

Implement selectors and detail panel.

**REFACTOR**

Keep derived display data in selectors, not persisted state.

**VERIFY**

Component tests + keyboard/focus check.

## Slice 2.3 — Centre, pan, zoom, and branch visibility

**Outcome**

Researcher can navigate the graph and centre on any confirmed person.

**RED**

Test deterministic state/UI adapter behavior for changing active root/centre target. Browser-level interaction verifies pan/zoom/centre.

**GREEN**

Implement graph controls and root/centre action.

**REFACTOR**

Do not treat display root as genealogical authority.

**VERIFY**

Desktop and mobile-width browser checks.

## Slice 2.4 — Responsive tree shell and accessibility basics

**Outcome**

Tree/detail actions remain usable at target desktop/mobile widths.

**RED**

Add component-level accessibility assertions where stable and practical.

**GREEN**

Implement responsive layout, labels, focus order, keyboard-reachable controls.

**REFACTOR**

Avoid custom interaction patterns where native controls suffice.

**VERIFY**

Browser inspection for focus, contrast basics, overflow, empty/error states.

---

# Phase 3 — Deterministic Relationship Engine

## Slice 3.1 — Core graph selectors

**Outcome**

Implement:

- `getParents`
- `getChildren`
- `getSiblings`
- `getGrandparents`

**RED**

Table-driven tests for normal, empty, duplicate-prevention, disconnected, and multi-parent cases.

**GREEN**

Implement pure graph functions.

**REFACTOR**

Use adjacency indexes/helpers without coupling to React.

**VERIFY**

Unit tests only; no UI dependency.

## Slice 3.2 — Aunts/uncles and first cousins

**Outcome**

Implement `getAuntsAndUncles` and `getFirstCousins`.

**RED**

Tests include:

- maternal/paternal branches;
- half-sibling implications where supported by graph semantics;
- no accidental spouse-as-blood-relative inference;
- disconnected branches.

**GREEN**

Build from lower-level graph selectors.

**REFACTOR**

Prefer composition over duplicated traversal.

**VERIFY**

Unit tests.

## Slice 3.3 — Common ancestor

**Outcome**

Implement deterministic `findCommonAncestor`.

**RED**

Tests cover:

- nearest common ancestor;
- multiple possible ancestors with deterministic tie handling;
- ancestor/descendant;
- unrelated people;
- same-person query.

**GREEN**

Implement bounded ancestry traversal.

**REFACTOR**

Document deterministic tie policy chosen by the plan implementation if multiple equally near ancestors exist; if this becomes a material product issue, stop and return for approval.

**VERIFY**

Unit tests.

## Slice 3.4 — Relationship path and naming

**Outcome**

Implement `findRelationship` for the spec-required MVP relationships.

**RED**

Tests cover at minimum:

- parent/child;
- sibling;
- grandparent/grandchild;
- aunt/uncle ↔ niece/nephew;
- first cousin;
- unrelated;
- path/common-ancestor metadata.

**GREEN**

Return a typed relationship result containing label, path, and common ancestor where relevant.

**REFACTOR**

Keep naming separate from traversal.

**VERIFY**

Unit tests with fixture family graphs.

## Slice 3.5 — Relationship Explorer UI

**Outcome**

Researcher chooses two confirmed people and receives deterministic relationship output.

**RED**

UI tests prove:

- only confirmed people are selectable;
- result updates after graph mutation;
- unrelated state is handled clearly.

**GREEN**

Implement explorer UI using relationship engine only.

**REFACTOR**

No AI/provider logic in this feature.

**VERIFY**

Integration tests + browser check.

---

# Phase 4 — Local Data Safety

## Slice 4.1 — Versioned export

**Outcome**

Export the complete validated local state as a versioned JSON file.

**RED**

Tests assert exported snapshot includes tree, persons, relationships, evidence, discoveries, and version.

**GREEN**

Implement export serializer/download helper.

**REFACTOR**

Reuse root schema validation before export if useful.

**VERIFY**

Unit test serialized output + browser download check.

## Slice 4.2 — Import parsing and validation

**Outcome**

KinTrace reads an import file and validates JSON, schema, version, and referential integrity before proposing replacement.

**RED**

Tests reject:

- malformed JSON;
- unsupported version;
- missing required collections;
- invalid relationship endpoints;
- invalid root person;
- structurally invalid evidence/discovery links where enforced.

**GREEN**

Implement import parser/validator returning a typed result without mutating live state.

**REFACTOR**

Keep validation pure and side-effect free.

**VERIFY**

Unit tests.

## Slice 4.3 — Confirmed import replacement

**Outcome**

Valid import replaces current state only after explicit confirmation.

**RED**

Tests prove:

- cancel leaves state untouched;
- validation failure leaves state untouched;
- confirm replaces and persists;
- persistence failure reports error without pretending replacement succeeded.

**GREEN**

Implement staged import and confirmation flow.

**REFACTOR**

Separate “validated candidate state” from active state.

**VERIFY**

Integration/browser tests.

## Slice 4.4 — Reset with confirmation

**Outcome**

Reset clears local KinTrace state only after explicit confirmation.

**RED**

Tests cover cancel and confirm paths.

**GREEN**

Implement reset action.

**REFACTOR**

Use the same destructive-action confirmation pattern as import replacement.

**VERIFY**

Integration/browser tests.

---

# Phase 5 — Evidence and Discovery Review Model

## Slice 5.1 — Evidence persistence and provenance

**Outcome**

Evidence can be attached to people/relationships while preserving source/provenance.

**RED**

Tests cover user evidence and normalized external evidence metadata.

**GREEN**

Implement evidence mutations/selectors.

**REFACTOR**

Keep raw provider data out of confirmed domain fields except allowed normalized/provenance payloads.

**VERIFY**

Unit/integration tests.

## Slice 5.2 — Discovery candidate state

**Outcome**

Create/store candidates with `pending`, `accepted`, `rejected`, and `unsure` statuses separately from confirmed graph truth.

**RED**

Tests prove creating/updating a discovery does not mutate persons/relationships automatically.

**GREEN**

Implement discovery mutations and Discover page list.

**REFACTOR**

Keep candidate and confirmed-state mutations separate.

**VERIFY**

Unit/UI tests.

## Slice 5.3 — Candidate review

**Outcome**

Researcher can inspect candidate details, matching/conflicting attributes, evidence, and choose Confirm / Not same / Not sure.

**RED**

UI tests cover all review states and error preservation.

**GREEN**

Implement candidate review UI.

**REFACTOR**

Make confidence display advisory only.

**VERIFY**

Component/browser tests.

## Slice 5.4 — Explicit acceptance mutation

**Outcome**

Accepting a candidate applies only explicitly selected confirmed changes.

**RED**

Tests prove:

- acceptance can attach evidence;
- selected missing facts can be copied;
- selected relative can be created/linked;
- unrelated external fields remain unapplied;
- rejected/unsure candidates never mutate confirmed graph.

**GREEN**

Implement an explicit acceptance command containing the exact proposed confirmed mutations.

**REFACTOR**

Use one auditable domain mutation boundary for candidate acceptance.

**VERIFY**

Domain + integration tests.

---

# Phase 6 — Provider Abstraction and User-Triggered Search

## Slice 6.1 — GenealogyProvider contract

**Outcome**

Typed provider abstraction exists and UI/domain do not import FamilySearch-specific types.

**RED**

Contract-level tests against mock provider behavior.

**GREEN**

Implement:

```ts
interface GenealogyProvider {
  searchPeople(query: PersonSearchQuery): Promise<PersonCandidate[]>;
  getPerson(id: string): Promise<ExternalPerson>;
  getRelationships(id: string): Promise<ExternalRelationship[]>;
  getRecords(id: string): Promise<ExternalRecord[]>;
}
```

**REFACTOR**

Normalize provider outputs before feature layers consume them.

**VERIFY**

Typecheck + tests.

## Slice 6.2 — Mock/test genealogy provider

**Outcome**

A deterministic provider fixture supports realistic candidate and failure flows without production FamilySearch access.

**RED**

Tests cover success, empty results, latency/loading, and provider error.

**GREEN**

Implement MockGenealogyProvider.

**REFACTOR**

Keep fixtures reusable across UI/browser tests.

**VERIFY**

Unit/integration tests.

## Slice 6.3 — User-triggered search flow

**Outcome**

From a selected person, “Find relatives” builds a query from known facts and runs only after explicit user action.

**RED**

Tests prove:

- no provider request occurs automatically on page/tree load;
- explicit Find relatives triggers request;
- search target can be Parents/Spouse/Children/Other records;
- known person data is mapped into normalized search query;
- provider failure does not mutate confirmed graph.

**GREEN**

Implement TanStack Query mutation/query flow triggered imperatively by researcher action.

**REFACTOR**

Keep remote state outside confirmed graph state.

**VERIFY**

Integration/browser tests including loading, empty, error, success.

## Slice 6.4 — Manual Search screen

**Outcome**

Researcher may run manual genealogy search with supported fields from the spec.

**RED**

Tests cover submission, validation, empty/error/results states.

**GREEN**

Implement Search page using same provider abstraction.

**REFACTOR**

Reuse normalized query and candidate presentation components.

**VERIFY**

UI/browser tests.

---

# Phase 7 — Records, Profile, Discover, Settings Completion

## Slice 7.1 — Person Profile

**Outcome**

Provide the minimum useful detailed person profile required to support the MVP without overcommitting optional sections.

**RED**

Tests verify confirmed person data, family, and evidence are displayed.

**GREEN**

Implement Overview plus only the additional suggested sections needed by current MVP flows.

**REFACTOR**

Do not make every PRD “suggested” section mandatory unless implementation evidence shows it is needed.

**VERIFY**

UI/browser tests.

## Slice 7.2 — Records library

**Outcome**

Researcher can inspect evidence records linked to people/relationships.

**RED**

Tests cover filtering/display by supported evidence type and provenance.

**GREEN**

Implement Records page.

**REFACTOR**

Reuse evidence selectors.

**VERIFY**

UI/browser tests.

## Slice 7.3 — Discover page completion

**Outcome**

Pending, unsure, rejected, and accepted discoveries are reviewable without confusing them with confirmed tree members.

**RED**

Tests cover state filters and clear status labels.

**GREEN**

Complete Discover UI.

**REFACTOR**

Avoid speculative scoring taxonomies.

**VERIFY**

UI/browser tests.

## Slice 7.4 — Settings

**Outcome**

Settings exposes:

- provider connection status boundary;
- export/import;
- reset;
- default tree root;
- default generation depth.

No account settings.

**RED**

Tests verify settings actions and absence of auth/account UI.

**GREEN**

Implement settings controls.

**REFACTOR**

Persist only approved local preferences/state.

**VERIFY**

UI/browser tests.

---

# Phase 8 — Privacy, Validation, and Failure Hardening

## Slice 8.1 — Referential and graph integrity validation

**Outcome**

State validator rejects or prevents invalid graph mutations.

**RED**

Tests cover:

- missing person endpoints;
- self-parent;
- duplicate edges;
- cycles in parent-child lineage;
- invalid root person;
- invalid evidence/discovery references where required.

**GREEN**

Implement deterministic validation.

**REFACTOR**

Keep mutation validation shared across UI/import/acceptance paths.

**VERIFY**

Unit tests.

## Slice 8.2 — Living-person privacy boundary

**Outcome**

No feature transmits living-person data except through an explicit, approved provider search initiated by the researcher.

**RED**

Tests ensure app launch/background rendering does not invoke provider. Search request construction occurs only inside explicit search action.

**GREEN**

Add privacy guards/documentation in provider/search boundary.

**REFACTOR**

Avoid duplicating sensitive fields into remote-cache keys/log output unnecessarily.

**VERIFY**

Network/browser inspection.

## Slice 8.3 — Error/recovery consistency

**Outcome**

All user-facing failure states preserve valid state and communicate next action.

**RED**

Tests cover storage errors, import errors, provider errors, candidate acceptance errors, and invalid graph states.

**GREEN**

Implement consistent result/error surfaces.

**REFACTOR**

Centralize only repeated error primitives, not domain-specific handling.

**VERIFY**

Integration/browser checks for error paths.

---

# Phase 9 — Production FamilySearch Gate

This phase is **blocked until external decisions are resolved**.

Required decisions/evidence before implementation:

1. FamilySearch production API access is confirmed.
2. Commercial-use/licensing terms are confirmed.
3. OAuth flow and required scopes are confirmed.
4. Client-only vs backend/proxy architecture is decided.
5. Allowed local persistence fields and retention rules are confirmed.
6. Credential/token storage approach is approved.
7. Living-person transmission/privacy implications are approved.

After approval, create a dedicated governed ticket/spec amendment if architecture or data handling changes materially.

### Planned implementation after gate resolution

- implement `FamilySearchProvider` behind the existing interface;
- add OAuth/connection UI only as approved;
- normalize API responses into provider-neutral types;
- add request/error/permission handling;
- ensure no raw tokens/secrets enter KinTrace persisted project state;
- add contract tests against fixtures and integration tests where safe;
- verify exact persisted-data behavior against approved policy;
- run real provider smoke tests only with authorized credentials/environment.

Production FamilySearch is not considered implemented or verified until this gate is completed.

---

# Phase 10 — Final MVP Verification

## Automated

Run all repository-defined checks and record exact results:

```bash
npm test
npm run lint
npm run typecheck
npm run build
```

Add any browser/e2e command introduced by implementation.

Do not claim any check passed unless observed.

## Deterministic relationship verification

Use fixed family fixtures covering:

- parent/child;
- siblings;
- grandparents;
- aunt/uncle and niece/nephew;
- first cousins;
- common ancestors;
- unrelated branches;
- graph update after edge deletion;
- invalid cycle prevention.

## Persistence verification

Verify:

- create tree → reload → data survives;
- edit/add relationship → reload;
- malformed localStorage payload;
- unsupported version;
- persistence write failure behavior.

## Import/export/reset verification

Verify:

- valid export round-trip;
- malformed JSON rejected;
- unsupported version rejected;
- invalid references rejected;
- cancel replacement preserves old state;
- confirmed replacement succeeds;
- cancel reset preserves state;
- confirmed reset clears state.

## Discovery verification

Verify:

- no automatic searches;
- explicit search only;
- loading/empty/error/results states;
- candidate does not alter confirmed graph;
- reject/unsure leaves graph unchanged;
- confirm applies only selected facts/relationships;
- evidence provenance preserved.

## Browser/user-flow verification

At minimum inspect desktop and mobile widths for:

1. first launch / empty state;
2. starting person creation;
3. add/edit relative;
4. tree navigation;
5. person detail;
6. relationship explorer;
7. export/import/reset;
8. discover candidate review;
9. manual search with mock provider;
10. settings.

Inspect:

- console errors;
- network errors;
- focus/keyboard behavior;
- loading/empty/validation/error/success states;
- obvious accessibility failures;
- responsive overflow/tree usability.

---

# 11. Acceptance Mapping

The implementation is ready for final review only when evidence demonstrates:

- arbitrary starting person/tree creation;
- local persistence and reload;
- parent/child/spouse/sibling-entry flows;
- interactive confirmed graph;
- deterministic relationship engine and explorer;
- safe relationship deletion;
- versioned export;
- validated/confirmed import replacement;
- confirmed reset;
- evidence provenance;
- separated discovery candidate state;
- explicit candidate review/acceptance;
- user-triggered provider search only;
- provider abstraction with mock/test implementation;
- no auth/cloud/background discovery/autonomous relationship creation;
- all required automated and browser verification passes.

Production FamilySearch connectivity is excluded from “verified MVP runtime” until the external gate in Phase 9 is resolved. The provider seam and mock-driven discovery workflow may be fully implemented before then.

---

# 12. Execution Order and Checkpoints

Execute strictly in this order unless verified implementation evidence justifies a smaller safe adjustment:

1. Phase 0 — scaffolding/contracts
2. Phase 1 — local tree foundation
3. Phase 2 — interactive tree
4. Phase 3 — relationship engine
5. Phase 4 — data safety
6. Phase 5 — evidence/discovery
7. Phase 6 — provider seam/mock search
8. Phase 7 — remaining MVP screens
9. Phase 8 — hardening
10. Phase 10 — final verification
11. Phase 9 — production FamilySearch only after separate gate resolution

At the end of every phase:

- run relevant tests;
- run type/lint/build checks available at that point;
- inspect the diff;
- confirm no unrelated scope;
- confirm no non-goal/deferred feature entered;
- update the source ticket evidence/status only when actual evidence warrants it.

---

# 13. Execution Authority Boundary

This plan defines implementation order but does not by itself authorize:

- dependency installation;
- runtime code changes;
- FamilySearch production decisions;
- commits/pushes/PRs;
- merge;
- deployment/release.

Before implementation begins, confirm the execution scope and any dependency changes required by Phase 0. Material scope, architecture, dependency, privacy/security, data-handling, or acceptance changes invalidate prior approval and require renewed approval.
