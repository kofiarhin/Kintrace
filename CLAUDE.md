# Claude Project Instructions

Read and follow `AGENTS.md` as the canonical KinTrace operating guide.

Then read:

- `docs/PRD.md`
- `roadmap.md`
- `review.md`
- `context/current-state.md`
- `context/decisions.md`
- `context/lessons.md`
- any task-relevant context, ticket, spec, and plan files.

Use `/workspace-health` for read-only truth/lifecycle/verification audits.

Use `/sync-project` only for approval-gated documentation/lifecycle reconciliation.

Use `/morning-brief` for safe intake and at most one queued ticket. It never implements.

Use `/deliver-ticket` as the default end-to-end delivery command. Runtime edits begin only after the consolidated execution contract receives `Approve plan`.

Use `/publish-ticket` only after a ticket is delivered and the user explicitly approves the publication contract. Publication does not imply merge, deployment, or release.

Manual delivery remains available through:

```text
/ticket → /spec → /plan → /implement-plan
```

Repository and verification evidence outrank stale planning or lifecycle metadata.
