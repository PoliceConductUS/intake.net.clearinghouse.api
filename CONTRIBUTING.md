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
