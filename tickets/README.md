# KinTrace Tickets

Tickets define **what should change and why** and form the durable queue between `/morning-brief` and `/deliver-ticket`.

Keep one ticket to one outcome, one visible finish line, and one reviewable change.

A ticket should contain only evidence-supported scope:

- request/problem;
- intended user outcome;
- current behaviour from repository evidence;
- desired behaviour;
- scope and exclusions;
- requirements and acceptance criteria;
- constraints/dependencies;
- unresolved questions.

Technical implementation design belongs in `spec/`.

Use stable numeric filenames, for example:

`001-local-tree-foundation.md`

## Lifecycle metadata

New tickets use:

```yaml
---
ticket_schema: 1
status: ready
source: manual
created: YYYY-MM-DD
---
```

Allowed sources:

- `manual`
- `morning-brief`
- `deliver-ticket`

Optional fields when applicable:

- `spec`
- `plan`
- `delivered_at`
- `superseded_by`
- `blocked_reason`

## Queue states

Active/non-terminal:

- `ready`
- `awaiting-approval`
- `in-progress`
- `verifying`
- `blocked`
- `failed-verification`

Terminal:

- `delivered`
- `superseded`

`/deliver-ticket` with no argument selects the highest-numbered eligible unfinished numeric ticket, skips terminal/blocked tickets during automatic selection, and revalidates interrupted work before continuation.

Do not reopen delivered tickets silently. A later regression gets a new ticket referencing the historical one.

## Intake flow

```text
/morning-brief
      ↓
create or reuse at most one evidence-backed status: ready ticket
      ↓
/deliver-ticket
```

The morning brief must not implement runtime work.

## Delivery flow

```text
/deliver-ticket
      ↓
spec → TDD plan → consolidated execution contract
      ↓
Approve plan
      ↓
RED → GREEN → REFACTOR → VERIFY
      ↓
final verification + review + project truth sync
      ↓
status: delivered
```

Manual expert flow remains:

```text
/ticket → /spec → /plan → /implement-plan
```

## KinTrace-specific ticket boundaries

A ticket must call out when it affects:

- genealogy data integrity;
- deterministic relationship logic;
- evidence/candidate/confirmed state;
- local persistence or import/reset;
- living-person privacy;
- FamilySearch or other external provider access;
- AI-assisted interpretation;
- dependencies or architecture.

Material changes to those areas require renewed approval.

## Acceptance and delivery evidence

Prefer observable checkbox criteria and mark them complete only from implementation/verification evidence.

A ticket becomes `delivered` only when:

- its acceptance criteria are supported;
- required checks/review are complete;
- no in-scope `Must fix` remains;
- project truth is synchronized;
- a concise `## Delivery Evidence` section records actual checks and remaining human review.

`delivered` does not mean committed, pushed, pull-requested, merged, deployed, or released.
