# ADR 0002: Use Trunk-Based Development and Worktrees

## Status

Proposed

## Context

Repositories should keep work scoped, easy to review, and easy to merge back to
trunk.

## Decision

Use trunk-based development with short-lived branches. Prefer git worktrees for
all changes, including OpenSpec proposal artifacts and implementation.

Manual worktrees live under:

```text
./.worktrees/<change-name>
```

## Consequences

- The main checkout stays clean.
- Branch cleanup is explicit.
- Behavior changes can carry proposal, implementation, verification, and archive
  commits in one isolated workspace.

## Revisit Trigger

Revisit if the organization adopts a different branch-management tool or hosted
development environment.
