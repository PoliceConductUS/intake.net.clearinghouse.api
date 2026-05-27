# ADR 0003: Use Conventional Commits

## Status

Proposed

## Context

Commit messages should make the purpose of a change clear without requiring a
developer to inspect the diff first.

## Decision

Use Conventional Commits for all commits:

```text
<type>(optional-scope): <short imperative summary>
```

Examples:

```text
docs: propose validate-intake-package
feat(cli): add validate command scaffold
fix(data): correct duplicate source reference
test(cli): cover unreadable input path
chore(tooling): add developer bootstrap
```

Use `BREAKING CHANGE:` in the commit body when a commit intentionally breaks
backward compatibility.

## Consequences

- History is easier to scan.
- Review, release notes, and rollback are easier.
- Tooling can later enforce commit message format if needed.

## Revisit Trigger

Revisit if release tooling requires a stricter commit policy.
