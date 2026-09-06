# KinTrace Browser Review Checklist

For user-facing work, inspect the actual flow when browser tooling is available.

## Layout

- Desktop width
- Mobile width
- Tree pan/zoom/selection behaviour
- Person detail panel or mobile equivalent
- Long names, missing dates/places, and large family branches

## Core states

- First-run / empty tree
- Tree with known relatives
- Loading external genealogy results
- No search results
- Candidate results
- Candidate review
- Validation/error state
- External-provider error
- localStorage persistence error where detectable
- Import validation failure
- Successful confirm/reject
- Successful export/import
- Destructive reset confirmation

## Accessibility

- Keyboard access to primary actions
- Visible focus states
- Meaningful labels for person/evidence status
- Dialog/drawer focus management
- Error messaging not dependent on colour alone
- Appropriate semantics for forms and controls

## Runtime evidence

- Console errors
- Network errors
- Unexpected external requests
- FamilySearch auth/provider failures where relevant

## Product review

- UI remains tree-centric, not researcher-centric
- Candidate/probable/confirmed states are distinguishable
- No external candidate silently modifies confirmed tree data
- Relationship labels match deterministic graph evidence
- Living-person data remains within approved MVP privacy boundaries
- Current ticket scope and `review.md` are satisfied

Report only what was actually inspected. Unavailable checks remain not run.
