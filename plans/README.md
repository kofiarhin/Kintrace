# KinTrace Implementation Plans

Plans define **how to execute an approved specification in order**.

Each plan references its source ticket and spec and splits work into the smallest useful vertical slices.

For testable slices use:

```text
RED → GREEN → REFACTOR → VERIFY
```

For every slice record:

- outcome;
- affected areas;
- RED test;
- minimum GREEN change;
- refactor boundary;
- verification.

Final verification should include the repository's actual relevant test/lint/type/build commands once they exist and browser review for user-facing flows when tooling permits.

For graph/relationship work, include deterministic relationship cases and edge cases.

For persistence/import work, include malformed-data, version, destructive-replacement, reload, and failure cases.

Do not redesign the approved spec silently. Material changes return to the appropriate approval boundary.

Use the same basename as the source spec, for example:

`plans/001-local-tree-foundation.md`
