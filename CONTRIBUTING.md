# Contributing

Thank you for considering a contribution.

## Development Setup

```bash
./scripts/bootstrap-dev.sh
npm run validate
```

Use `npm run` to see available project commands.

## Change Workflow

Behavior changes should use OpenSpec and Superpowers:

1. Brainstorm the outcome.
2. Create a worktree under `./.worktrees/<change-name>`.
3. Turn the brainstorm into an OpenSpec change.
4. Run `/opsx:continue <change-name>` until proposal/spec/tasks/plan artifacts
   are coherent.
5. Apply with `/opsx:apply <change-name>`.
6. Verify, participate in the retrospective, archive, and validate before merge.

Small documentation, formatting, or tooling edits that preserve behavior can be
handled directly.

## Commit Messages

Use Conventional Commits:

```text
<type>(optional-scope): <short imperative summary>
```

Examples:

```text
docs: update contributing guide
feat(cli): add validate command
fix(data): correct duplicate source reference
```

## Sensitive Data

Do not include sensitive personal data, credentials, private records, sealed
records, or non-public law-enforcement material in public issues, pull requests,
fixtures, screenshots, or logs.

## Implementation Expectations

Use typed, validated models at public API, CLI, job, workflow, and data loading
boundaries. Keep names intent-revealing, avoid abbreviations, and use one term
per concept across docs, code, schemas, tests, and user-facing behavior.

Prefer `.yaml` for human-authored configuration, declarative specs, manifests,
workflows, and policy files. Keep JSON when required by the tool.

Design tasks and data workflows to be idempotent and repeatable. Errors should
fail fast with enough context to diagnose the problem.

## Public Web Accessibility

Public-facing web projects must include automated accessibility checks using
axe-core or an axe-core-based tool. If the check is not part of `npm run
validate`, document the command and CI job that runs it.
