# KinTrace Roadmap

## Current goal

Build the MVP described in `docs/PRD.md`: a local-first family research application where a researcher can create a tree around any starting person, persist known people and direct relationships, explicitly search for missing connections, review evidence-backed candidates, confirm or reject discoveries, and determine how confirmed people are related.

Current lifecycle: **Proposed**. No runtime implementation exists in the repository.

## Ordered outcomes

1. **Proposed — Local family-tree foundation**
   - Create a tree around any starting person.
   - Add/edit people and direct parent/spouse relationships.
   - Persist the tree in browser `localStorage`.
   - Reopen the app without losing saved tree data.

2. **Proposed — Interactive tree exploration**
   - Display the confirmed family graph.
   - Select and centre on any person.
   - Support the primary person detail and add-relative flows.
   - Support relevant desktop/mobile interaction states.

3. **Proposed — Deterministic relationship engine**
   - Derive siblings, grandparents, aunts/uncles, first cousins, and relationship paths from direct graph edges.
   - Find common ancestors and describe supported kinship relationships deterministically.

4. **Proposed — Local data safety**
   - Export a versioned KinTrace JSON snapshot.
   - Validate imports before replacing local data.
   - Require confirmation before destructive reset/import replacement.

5. **Proposed — Evidence and discovery review model**
   - Store evidence and discovery candidates separately from confirmed tree truth.
   - Let the researcher confirm, reject, or leave a candidate unresolved.
   - Preserve conflicting information rather than silently deleting competing claims.

6. **Proposed — FamilySearch integration**
   - Verify production access, commercial/licensing constraints, OAuth requirements, and persistence rules first.
   - Add the provider behind an abstraction.
   - Search only when the researcher explicitly triggers discovery.
   - Never auto-confirm external candidates.

7. **Proposed — MVP completion**
   - Verify the full PRD success criteria with automated and experience-level evidence.
   - Keep implementation, delivery, publication, merge, deployment, and release states distinct.

## Exclusions

The MVP excludes:

- KinTrace authentication/accounts;
- cloud persistence and multi-device sync;
- collaboration, invitations, and public profiles;
- DNA matching;
- automatic/background genealogy discovery;
- autonomous relationship creation;
- Ancestry, Findmypast, and GRO scraping;
- native mobile apps;
- social/messaging features;
- image restoration and automatic photo recognition;
- production-grade GEDCOM interoperability.

## Definition of done

The MVP is done only when repository and verification evidence show that a researcher can:

- create a tree around an arbitrary person;
- manually build known immediate family relationships;
- reload without losing local data;
- select a person and explicitly search for missing family information;
- inspect multiple candidates and their evidence;
- accept a discovery and see the confirmed tree update correctly;
- reject a discovery without changing confirmed tree truth;
- determine a family relationship between two confirmed people;
- export and restore tree data;
- complete required automated checks and browser/user-flow verification.

A ticket, spec, plan, commit, PR, or merge does not by itself satisfy this definition.
