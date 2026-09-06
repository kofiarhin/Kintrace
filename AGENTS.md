# KinTrace Project Operating Guide

## Product

KinTrace is an MVP genealogy research application for reconstructing a trustworthy family network from incomplete information. A researcher can start with any person, add known relatives, explicitly search for missing family connections, review evidence, confirm or reject discoveries, and understand how people are related.

The product promise is:

> Start with anyone. Build what you know. Trace what you don't.

The current source of product truth is `docs/PRD.md`.

## Current goal

Deliver the KinTrace MVP defined in `docs/PRD.md` while preserving these confirmed boundaries:

- tree-centric, not user-centric;
- no KinTrace authentication in the MVP;
- browser `localStorage` persistence;
- user-triggered discovery only;
- external candidates never become confirmed tree data without researcher acceptance;
- extended family relationships are derived deterministically from the graph;
- FamilySearch is the initial intended genealogy provider, subject to unresolved access/licensing/OAuth/persistence constraints.

## Workspace health and synchronization

Use `/workspace-health` when durable project truth may have drifted from repository, Git, GitHub, or verification evidence. It is read-only.

Use `/sync-project` only for evidence-backed documentation/lifecycle reconciliation after project reality changed outside the normal delivery flow. Unless stronger instructions apply, present the exact sync and wait for `Approve sync` before writing.

## Operator workflow

Use `/morning-brief` to reconcile current project truth, identify at most one highest-leverage next outcome, and create or reuse at most one evidence-backed ticket in `tickets/`.

The morning brief must not implement runtime work, create specs/plans, modify dependencies/data, commit, push, merge, deploy, or activate routines.

## Software delivery workflow

Default flow:

```text
/workspace-health   # optional read-only audit
      ↓
/sync-project       # optional approved truth repair
      ↓
/morning-brief
      ↓
create/reuse one ready ticket
      ↓
/deliver-ticket
      ↓
spec → TDD plan → consolidated execution review
      ↓
Approve plan
      ↓
RED → GREEN → REFACTOR → VERIFY
      ↓
final verification → review → project truth sync
      ↓
status: delivered
      ↓
/publish-ticket     # optional separate publication boundary
      ↓
Approve publish
      ↓
scoped commit if needed → non-force push → draft PR
```

The manual expert path remains:

```text
/ticket → /spec → /plan → /implement-plan
```

## Ticket lifecycle

Allowed lifecycle states:

- `ready`
- `awaiting-approval`
- `in-progress`
- `verifying`
- `delivered`
- `blocked`
- `failed-verification`
- `superseded`

`delivered` and `superseded` are terminal historical states.

A delivered ticket means its scoped outcome was implemented, required acceptance evidence exists, verification/review completed, and project truth was synchronized. It does not mean committed, pushed, pull-requested, merged, deployed, or released.

## Working rules

- Read `docs/PRD.md`, `roadmap.md`, `review.md`, relevant `context/` files, and `context/lessons.md` before changing runtime work.
- Inspect current repository evidence before claiming implementation details.
- Keep one ticket to one observable outcome and one reviewable change.
- Prefer TDD for testable implementation: RED → GREEN → REFACTOR → VERIFY.
- Keep deterministic genealogy logic separate from probabilistic AI or external-data matching.
- Never let AI silently create, merge, or confirm genealogical truth.
- Preserve evidence provenance and uncertainty.
- Treat living-person information as privacy-sensitive product data.
- Preserve unrelated work and current user changes.
- Do not add auth, cloud persistence, background discovery, extra providers, DNA features, collaboration, or other deferred scope unless a later approved ticket explicitly changes scope.
- Do not claim tests, builds, browser checks, commits, pushes, merges, deployments, or releases succeeded without observed evidence.

## Permissions

Safe/read-only without additional approval:

- inspect repository files and Git/GitHub evidence;
- read project context;
- run read-only diagnostics;
- prepare tickets/specs/plans within their normal contracts;
- run `/workspace-health`.

Approval-required:

- runtime/application code changes;
- dependency changes;
- changes to persistence model;
- authentication or account-system changes;
- FamilySearch integration decisions that affect data handling, OAuth, permissions, or licensing;
- destructive import/reset behaviour changes;
- security/privacy boundary changes;
- publication via commit/push/draft PR.

Human-owned unless explicitly authorized separately:

- merge;
- production deployment or release;
- destructive production/data operations;
- credentials/secrets;
- legal/licensing acceptance;
- publishing external commitments.

Material scope, architecture, dependency, migration, authentication, permission, privacy/security, acceptance, or verification changes invalidate prior implementation approval.

## Verification

Before describing runtime work as delivered, use repository-defined checks once implementation exists. Until commands exist, exact test/lint/type/build commands are unresolved and must be confirmed from repository evidence.

For user-facing work, inspect relevant desktop and mobile flows when browser tooling is available, including loading, empty, validation/error, success, console, network, and accessibility states.

Family graph relationship results must be verified deterministically with tests for representative and edge-case kinship paths.

## Document alignment

After verified implementation, update only project-truth documents whose evidence changed:

- `context/current-state.md`
- `context/architecture.md`
- `context/decisions.md`
- `roadmap.md`
- `context/lessons.md`
- source ticket lifecycle/evidence

Do not use planning artifacts as implementation evidence.

## Completion states

Keep these distinct when relevant:

`proposed → specified → planned → awaiting-approval → in-progress → implemented → verifying → verified → delivered → committed → pushed → pull-requested → merged → deployed → released`.
