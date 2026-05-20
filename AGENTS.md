# AGENTS.md

## Purpose

This file defines project-specific guidance for agents working in this
repository.

This repository belongs to the Institute for Police Conduct, Inc. The development
style is outcome-driven, direct, evidence-based, and hostile to speculative
complexity.

Use this file for repository doctrine. Use Superpowers for execution discipline,
including brainstorming, planning, TDD, debugging, review, verification, branch
finishing, and worktree setup.

## Prime Directive

Build the smallest correct thing that produces the intended outcome.

Use outcomes-driven development with an outside-in approach: start from the user
or system outcome, define the observable behavior, then work inward to the
smallest implementation that satisfies it.

Do not optimize for imagined future needs. Do not preserve old behavior unless it
is explicitly required. Do not add fallback behavior unless it is explicitly
required and tested. Do not invent requirements or hide uncertainty.

When requirements are ambiguous, prefer the smallest safe interpretation, state
the assumption plainly, and keep the change reversible. Ask the user only when a
wrong assumption would cause meaningful harm.

## Workflow References

- Superpowers governs how agents execute work in this repo.
- OpenSpec governs intended behavior and change artifacts.
- Trunk-based development governs integration: keep branches short-lived, scoped,
  and mergeable back to trunk.
- Git worktrees are preferred for all change work, including OpenSpec proposal
  artifacts and implementation; use the `superpowers:using-git-worktrees` skill
  rather than hand-rolling a competing worktree process.
- Manual git worktrees should be created under `./.worktrees/<change-name>` from
  the repo root.

Do not duplicate Superpowers workflows in this file. If this file and a
Superpowers skill disagree about process mechanics, follow the user instruction
with the higher priority and call out the conflict.

## OpenSpec Usage

OpenSpec is the source of truth for intended system behavior.

Read `openspec/config.yaml` before product, schema, data-shape, validation, or
workflow behavior changes. It defines the project context, global rules, and the
`superpowers-bridge` schema used by this repo.

If user-visible behavior, data shape, validation behavior, workflow behavior, or
generated contracts change, create or update the appropriate OpenSpec change
before implementation. Do not implement first and backfill the spec later.

Create OpenSpec change artifacts from the change worktree, not from the main
checkout. Documentation-only edits, formatting, test-only refactors, and internal
refactors that preserve specified behavior can be direct PR-sized changes.

## No Hidden Product Decisions

Do not encode product decisions only in code, comments, tickets, pull requests,
or agent messages. If behavior matters, it belongs in OpenSpec.

Examples of hidden product decisions:

- stricter validation than requested
- silently normalizing user input
- adding required fields
- changing command, API, data, or workflow semantics
- adding fallback behavior
- choosing a durable persistence or provenance model

If project context, OpenSpec, existing code, and this file conflict, stop and
call out the conflict instead of guessing.

## Simplicity Rules

Prefer the smallest complete solution. Small means focused, understandable,
testable, and shippable.

Every line of code, branch, function, file, abstraction, dependency,
configuration option, and test helper must be necessary for the current outcome.
If it can be removed and the required outcome still works, remove it.

Do not add generic frameworks, plugin systems, configuration layers, queues,
caches, retries, compatibility shims, or extension points unless the current
outcome requires them.

## No Silent Fallback

This is a fail-fast-and-loudly project.

Do not guess. Do not silently recover. Do not report partial success as success.
Do not skip invalid records without making that visible. Do not continue after a
failed write as though the operation succeeded.

Allowed fallback behavior must be explicitly required, visible, tested,
documented, and removable.

## Dependencies

Do not add dependencies for hypothetical future needs. Keep dependency changes
scoped to the current task unless the user asks for a broader update. Remove
dependencies that are no longer necessary for the current outcome.

When adding or updating a dependency, check the current published version instead
of relying on memory.

For new tools, languages, frameworks, services, or runtimes, document the current
outcome, alternatives, validation path, operational burden, and removal path.
Use an ADR when the choice affects architecture, workflow, runtime operations,
security posture, or long-term maintenance.

When an organization technology radar exists, prefer `Adopt` entries first and
`Trial` entries second. Treat `Assess` and `Caution` entries as requiring
explicit ADR justification before adding them to a project.

## Validation

Prove changes with the narrowest validation that covers the risk.

For OpenSpec changes, run `npm run openspec:validate`.

If a relevant validation command cannot be run, report that clearly with the
reason.

## Trunk-Based Development

Keep work scoped and short-lived. Prefer small branches from trunk, frequent
integration, and direct fixes over long-running feature branches. Avoid broad
refactors mixed with behavior changes.

Working in a git worktree is preferred for all changes, including documentation,
setup edits, OpenSpec proposal artifacts, and implementation, because it keeps
the main checkout clean and makes branch cleanup explicit.

When creating worktrees manually, use `./.worktrees/<change-name>` from the repo
root. Keep worktree names aligned with the branch or OpenSpec change name.

Changes that do not modify behavior or outcome may be made directly on `main`
when the scope is small and reversible. Behavior changes, data-shape changes,
validation changes, and downstream contract changes should use an isolated
worktree.

The worktree setup and cleanup process belongs to Superpowers; this repo only
requires that isolation be considered and that branch work remains easy to merge
back to trunk.

Use Conventional Commit messages for all commits.
