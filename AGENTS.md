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

## Intake Source Module Rules

This repository is an intake source module for the Civil Rights Litigation
Clearinghouse API. It must follow the root intake producer workflow documented
in [docs/intake-source-producer-workflow.md](docs/intake-source-producer-workflow.md).

The module namespace is `clearinghouse-api`.

Root intake is the durable source for shared contracts. Before changing command
behavior, workspace layout, artifact shape, source identity, persistence,
caching, validation, or generated-contract behavior, read:

- `/Users/dalelotts/dev/PoliceConductUS/intake/AGENTS.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0001-define-intake-envelope-contract.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0005-use-source-specific-artifact-producers.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0010-own-shared-intake-workspace-layout.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0014-implement-commands-as-configurable-elt-pipelines.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/openspec/specs/artifacts-database-import/spec.md`

Source data does not belong in this repository. Preserve acquired source data,
source provenance, digests, generated artifacts, diagnostics, and logs under the
command folder assigned by the `Command` envelope. Use the module state path
assigned by the `Command` envelope for reusable module-owned source/cache state.

Do not write outside paths granted by `Command.spec.path` and
`Command.spec.statePath`. Do not infer additional writable paths from the process
current working directory, sibling repositories, or previous local experiments.

All intake YAML envelopes must use
`apiVersion: policeconduct.org/intake/v1alpha1` and must be read or written
through root intake shared IO when that envelope kind is part of the shared
contract. Do not hand-roll envelope parsing, validation, serialization,
filenames, or path conventions.

Source-produced artifacts must use stable source-local record names and must not
contain canonical database IDs. Root intake owns `SourceNameToCanonicalId`
records, canonical cuid2 assignment, database mutation planning, replay, and
canonical resolved-property state.

If source records are missing fields needed for stable artifacts, resolve those
fields through explicit auditable command stages. Cache only deterministic,
module-owned resolver output under the module state path. Fail loudly when a
property cannot be resolved safely.

Reconfirm assumptions periodically as new root intake contracts, producer
patterns, or source facts become available. Ask before including or excluding
adjacent repositories, changing persistence/cache behavior, adding artifact
kinds, or changing source-data handling.

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

## Implementation Standards

Validate data at system boundaries. Public APIs, CLIs, jobs, workflows, and data
loaders should accept typed, validated models rather than raw dictionaries,
loosely shaped objects, or unchecked payloads.

Use intent-revealing names. Avoid abbreviations, single-letter variables, and
multiple names for the same concept. Keep terminology consistent across docs,
code, schemas, tests, and user-facing behavior.

Prefer pure functions, immutable domain values, composition over inheritance, and
clear separation between CLI/API/UI, domain behavior, persistence,
infrastructure, and external integrations.

Tasks, jobs, data loaders, and workflows should be idempotent and repeatable
unless an ADR explains why that is impossible or undesirable.

Prefer `.yaml` for human-authored configuration, declarative specs, manifests,
workflows, and policy files. Use JSON when a tool requires it or when strict
machine interchange/schema support is materially better.

Build observability in from the start for production services and scheduled
automation. Emit structured logs and enough metrics or traces to diagnose
failures.

## Operational Standards

Manual diagnosis is an anti-pattern. Production services, scheduled jobs, and
data pipelines should make problems visible through automated checks, reports,
dashboards, traces, metrics, alerts, or CLI commands before a human investigates
manually.

Runbooks should be indexes into automated detection, diagnosis, recovery, and
escalation paths. If a runbook requires a manual check, prefer adding a health
check, validation report, metric, alert, or CLI command.

Do not log secrets, credentials, private records, sealed records, or unnecessary
personal data. Use stable IDs, source references, and redacted context for
diagnosis.

PR environments and other temporary cloud resources need visible ownership,
cost-control tags, and automated cleanup.

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

## Infrastructure Environments

AWS is the default platform for production and pull request environments.

Pull request environments should be isolated per PR, created or updated as part
of the PR lifecycle, and destroyed when the PR closes. Infrastructure should be
defined in checked-in configuration, validated in CI where practical, and exposed
through job-centric npm scripts when this repository owns deployable
infrastructure.

Infrastructure scripts use `infra:<job>` for local commands and
`infra:<job>:<env>` for non-local environments. Local is the default when no
environment suffix is present. Shared environments should use explicit suffixes;
currently expected suffixes are `pr` and `prod`. Examples:
`infra:plan`, `infra:diagnose`, `infra:plan:prod`, `infra:diagnose:prod`, and
`infra:destroy:pr`. `diagnose` scripts must be read-only. Do not add a
production destroy script unless the repo has an explicit disaster-recovery
design and approval path.

For `:pr` commands, resolve the PR from the active git branch/worktree using
GitHub CLI. If no open PR is associated with the active branch, fail loudly and
explain how to provide an explicit PR number, such as
`npm run infra:diagnose:pr -- --pr 123`.

Use another production or PR environment platform only when an ADR explains the
reason, access model, cleanup behavior, observability, and cost controls.

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
