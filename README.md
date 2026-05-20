# Project Template

Reusable starting point for Institute for Police Conduct, Inc. repositories that
use Codex App, OpenSpec, Superpowers, the OpenSpec Superpowers bridge,
trunk-based development, Conventional Commits, and one-command developer setup.

After creating a repo from this template, replace this README with the
project-specific purpose, commands, data rules, and operating notes.

## New Developer Quickstart

From a fresh clone on macOS or Linux:

```bash
./scripts/bootstrap-dev.sh
```

Run this to see every available npm script:

```bash
npm run
```

Common commands:

```bash
npm run doctor
npm run validate
npm run openspec:validate
```

## Standard Workflow

Use this flow for behavior changes, data-shape changes, validation changes, or
anything that affects downstream contracts.

1. Verify setup:

   ```bash
   npm run doctor
   npm run openspec:validate
   npx openspec schemas
   npx openspec status
   ```

2. Brainstorm the outcome with Codex. Ask for the smallest useful slice, what
   can be removed or postponed, whether backward compatibility matters, and what
   tests would prove the change works.

3. Start the change in a worktree:

   ```text
   Start <change-name> in a git worktree before creating OpenSpec artifacts.
   ```

   Manual equivalent:

   ```bash
   mkdir -p .worktrees
   git worktree add .worktrees/<change-name> -b <change-name>
   ```

4. Turn the brainstorm into an OpenSpec change:

   ```text
   Turn this brainstorm into an OpenSpec change named <change-name>. Do not implement yet.
   ```

5. Continue the OpenSpec flow until proposal/spec/tasks/plan artifacts are
   coherent:

   ```text
   /opsx:continue <change-name>
   ```

   Ask "What's next in the Superpowers/OpenSpec flow?" when unsure.

6. Review `openspec/changes/<change-name>/` before implementation. Look for
   missing acceptance criteria, vague requirements, untested behavior,
   unnecessary scope, accidental backward compatibility promises, and tasks or
   tests that do not connect back to the proposed behavior.

7. Commit the OpenSpec artifacts:

   ```bash
   git add -A
   git commit -m "docs: propose <change-name>"
   ```

8. Apply:

   ```text
   /opsx:apply <change-name>
   ```

9. Validate and commit implementation:

   ```bash
   npm run validate
   git add <changed-files>
   git commit -m "feat: implement <change-name>"
   ```

10. Verify, participate in the retrospective, and archive:

    ```text
    /opsx:verify <change-name>
    /opsx:continue <change-name>
    /opsx:archive <change-name>
    ```

11. Commit archive artifacts:

    ```bash
    git add -A
    git commit -m "docs: archive <change-name>"
    ```

## Prompting Codex

Do not over-control the AI with step-by-step implementation instructions.
Instead, give outcomes, constraints, examples, and acceptance criteria.

AI has trouble with vague concepts. Replace "make it robust" with the failure
modes to handle, "make it simple" with what should be removed or postponed, and
"production-ready" with the checks, guarantees, and operator behavior that
matter.

## Conventional Commits

Use Conventional Commits:

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

Commitizen can help format messages:

- <https://github.com/commitizen/cz-cli>
- <https://commitizen-tools.github.io/commitizen/commands/commit/>

## References

- Engineering standards: `PoliceConductUS/engineering-standards`
- Org profile: `PoliceConductUS/.github`

## Adding Tools or Frameworks

Do not add tools, languages, frameworks, services, or runtimes for speculative
future needs. Start with the Institute for Police Conduct, Inc. technology
radar:

- `Adopt` technologies are the easiest to add.
- `Trial` technologies are reasonable when the project has a matching use case
  and documents the evaluation.
- `Assess` and `Caution` technologies need stronger justification before
  becoming project dependencies.

For durable technology choices, write an ADR and document:

- the current outcome it enables
- what existing tool or pattern it replaces
- how developers install, run, test, and debug it
- how CI validates it
- security, data, accessibility, or operational risk
- the removal path if it does not work out

Prefer `.yaml` for human-authored configuration, declarative specs, manifests,
workflows, and policy files. Keep JSON where the tool requires it, such as
`package.json`, or where machine interchange/schema support is materially better.

## Infrastructure Environments

AWS is the default platform for production and pull request environments.

Pull request environments should be isolated per PR, created or updated as part
of the PR lifecycle, and destroyed when the PR closes. Infrastructure should be
defined in checked-in configuration and exposed through job-centric npm scripts
when this repository owns deployable infrastructure.

Infrastructure scripts use `infra:<job>` for local commands and
`infra:<job>:<env>` for non-local environments. Local is the default when no
environment suffix is present. Shared environments should use explicit suffixes;
currently expected suffixes are `pr` and `prod`.

```bash
npm run infra:up
npm run infra:plan
npm run infra:apply
npm run infra:diagnose
npm run infra:plan:prod
npm run infra:apply:prod
npm run infra:diagnose:prod
npm run infra:plan:pr
npm run infra:apply:pr
npm run infra:diagnose:pr
npm run infra:destroy:pr
```

`diagnose` scripts must be read-only. Do not define a production destroy script
unless the repo has an explicit disaster-recovery design and approval path.

Use GitHub OIDC for GitHub Actions access to AWS. Avoid long-lived AWS access
keys in repository or organization secrets.

Use another production or PR environment platform only when an ADR explains the
reason, access model, cleanup behavior, observability, and cost controls.

## Implementation Standards

Validate data at public API, CLI, job, workflow, and data loading boundaries
using typed models. Keep names intent-revealing and terminology consistent.
Design tasks and data workflows to be idempotent and repeatable.

Production services and scheduled automation should include structured logs and
enough metrics or traces to diagnose failures. Prefer OpenTelemetry when a
project needs portable observability across services or jobs.

## Operational Standards

Manual diagnosis is an anti-pattern. Production services, scheduled jobs, and
data pipelines should expose automated health, validation, audit, or diagnostic
output before relying on human investigation.

Runbooks should point to automated signals and recovery paths: dashboards, log
queries, traces, validation reports, job reports, CLI commands, rerun/replay
steps, rollback steps, and escalation paths.

Temporary cloud resources, including PR environments, should have ownership tags,
cost controls, and automated cleanup.

Do not log secrets, credentials, private records, sealed records, or unnecessary
personal data.

## Public Web Accessibility

If this repository is a public-facing web project, add automated accessibility
checks using axe-core or an axe-core-based tool and document the local/CI command.
