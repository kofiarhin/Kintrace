# Product Context

## Product

KinTrace is a genealogy and family-relationship research application.

Promise:

> Start with anyone. Build what you know. Trace what you don't.

It helps a researcher reconstruct a family network from incomplete information by combining manual tree building, evidence-backed discovery, and deterministic relationship calculation.

## Customer

Primary user: a person researching a family's history.

The researcher may be:

- researching their own family;
- researching another family;
- documenting historical genealogy;
- helping another person construct a tree;
- reconstructing information from family documents.

No customer interviews, market validation, or buyer evidence are recorded yet.

## Problem

Family information is fragmented across memories, relatives, handwritten notes, certificates, historical records, genealogy databases, and incomplete trees.

The user needs to turn those fragments into a trustworthy, understandable family network without manually treating every possible relationship or external record as fact.

## Included MVP scope

- create a family tree around any starting person;
- manually add/edit known people;
- direct parent and spouse graph relationships;
- derived kinship relationships;
- interactive tree exploration;
- browser `localStorage` persistence;
- local export/import/reset;
- evidence and discovery candidate states;
- user-triggered external genealogy search;
- candidate review and explicit confirm/reject actions;
- relationship explorer;
- FamilySearch as the first intended provider, pending access/licensing verification.

## Excluded MVP scope

- KinTrace authentication/accounts;
- cloud persistence;
- multi-device syncing;
- collaboration and public profiles;
- DNA matching;
- background/automatic discovery;
- autonomous confirmation;
- additional genealogy providers;
- native mobile apps;
- social/messaging features;
- photo restoration/recognition;
- production-grade GEDCOM interoperability.

## Primary journey

```text
Create tree
  ↓
Add starting person
  ↓
Add known relatives
  ↓
Explore confirmed graph
  ↓
Select any person
  ↓
Find relatives
  ↓
Search genealogy source
  ↓
Review candidate evidence
  ↓
Confirm / reject / not sure
  ↓
Confirmed tree grows only when accepted
  ↓
Repeat
```

## Important secondary flows

- inspect a person's overview/life/family/records/sources;
- determine how two confirmed people are related;
- resolve conflicting evidence;
- export local tree data;
- validate and import a tree snapshot;
- reset local tree data with confirmation.

## Success

The MVP success criteria are the ten observable outcomes listed in `docs/PRD.md#20-success-criteria`.

No business or adoption metrics are specified yet.
