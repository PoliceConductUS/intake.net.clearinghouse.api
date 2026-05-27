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
