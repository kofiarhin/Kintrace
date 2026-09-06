# KinTrace Core Demo Flow

This is the intended MVP walkthrough from `docs/PRD.md`. No step is currently implemented or verified.

## 1. Create a tree

**User:** opens KinTrace with no saved tree and chooses to add a starting person.  
**Product should show:** a tree-creation flow centred on any person, not necessarily the researcher.  
**Expected outcome:** a new local tree exists with the selected root/display person.

## 2. Add known relatives

**User:** adds parents, spouse(s), siblings, or children they already know.  
**Product should show:** confirmed people and direct relationships in the graph.  
**Expected outcome:** known family structure grows and persists locally.

## 3. Explore the confirmed graph

**User:** pans/zooms, selects a person, or centres the tree on another person.  
**Product should show:** selected-person details, known relationships, evidence count, and missing information.  
**Expected outcome:** the researcher can navigate the family without the app assuming the tree is about them.

## 4. Trigger discovery

**User:** selects a person and chooses `Find relatives`.  
**Product should show:** a choice such as parents, spouse, children, or other records, then a search based on known facts.  
**Expected outcome:** external search occurs only because the researcher explicitly initiated it.

## 5. Review candidates

**User:** opens a possible match.  
**Product should show:** known tree facts alongside external-record facts, confidence/supporting signals, and source evidence.  
**Expected outcome:** the candidate remains separate from confirmed tree truth.

## 6. Confirm, reject, or leave unresolved

**User:** chooses `Confirm match`, `Not the same person`, or `Not sure`.  
**Product should show:** a clear outcome without silent merging.  
**Expected outcome:** only accepted information changes the confirmed graph.

## 7. Continue tracing

**User:** selects the newly confirmed person and starts another discovery search.  
**Expected outcome:** the family graph progressively expands through explicit researcher decisions.

## 8. Understand a relationship

**User:** selects two confirmed people and asks how they are related.  
**Product should show:** a deterministic relationship label/path and common ancestor where applicable.  
**Expected outcome:** the relationship comes from graph traversal, not AI guessing.

## 9. Preserve research

**User:** reloads, exports, imports, or intentionally resets local data.  
**Expected outcome:** normal reload preserves the tree; export produces a versioned backup; import validates before replacement; destructive actions require confirmation.
