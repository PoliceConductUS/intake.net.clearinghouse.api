# intake.net.clearinghouse.api

Source-specific intake producer for Civil Rights Litigation Clearinghouse API
records related to police conduct.

This repository owns source-specific extraction and artifact generation for the
Clearinghouse API. It does not own canonical database IDs, root intake state,
database mutation planning, or source-data persistence outside the standard
intake workspace model.

## Intake Role

This is an intake source module. It should expose a command that root intake can
run through a `Command` envelope. The command reads Clearinghouse source data,
preserves acquired source material in the assigned command folder, uses the
module state path for reusable source/cache state, and writes versioned intake
artifact envelopes through root intake shared IO.

Source data does not belong in this repository. Checked-in files should be code,
tests, fixtures small enough to prove behavior, documentation, OpenSpec
artifacts, and configuration.

The module namespace is:

```text
clearinghouse-api
```

Expected artifact output, once root intake supports civil litigation import
types:

```yaml
apiVersion: policeconduct.org/intake/v1alpha1
kind: Artifacts
metadata:
  namespace: clearinghouse-api
  name: <command-name>
spec:
  artifacts:
    - ref:
        path: <command-name>.CivilCases.yaml
        kind: CivilCases
        sha256: <digest>
```

## Standard Producer Workflow

Follow [docs/intake-source-producer-workflow.md](docs/intake-source-producer-workflow.md).
The current Clearinghouse civil-case artifact model is documented in
[docs/civil-case-artifact-model.md](docs/civil-case-artifact-model.md).

Short version:

1. Root intake creates a command folder and passes this module a `Command`
   envelope.
2. This module reads only the paths granted by `Command.spec.path` and
   `Command.spec.statePath`.
3. The command acquires source data from the Clearinghouse API or an approved
   workspace cache.
4. Acquired source data is preserved under the command folder, with provenance
   and digests.
5. Reusable source/cache state lives under this module's assigned state path.
6. The producer derives stable source-local record names from source-provided
   Clearinghouse identifiers.
7. The producer writes `Artifacts` and typed artifact envelopes through root
   intake shared IO.
8. The producer never writes canonical database IDs.
9. Root intake validates artifacts, resolves source names to canonical IDs,
   plans database mutations, and owns replay.

## Root Intake References

Root intake is the durable source for shared contracts:

- `/Users/dalelotts/dev/PoliceConductUS/intake/AGENTS.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0001-define-intake-envelope-contract.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0005-use-source-specific-artifact-producers.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0010-own-shared-intake-workspace-layout.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0014-implement-commands-as-configurable-elt-pipelines.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/openspec/specs/artifacts-database-import/spec.md`

If this repository and root intake disagree, stop and resolve the conflict before
changing behavior.

## Development

From a fresh clone:

```bash
./scripts/bootstrap-dev.sh
npm run validate
```

Run this to see available npm scripts:

```bash
npm run
```

Use OpenSpec before behavior, data-shape, validation, command, or artifact
contract changes:

```bash
npm run openspec:validate
npm run openspec:status
```

## Initial Implementation Order

Root intake must support civil litigation artifact kinds before this producer can
emit final importable artifacts. The expected sequence is:

1. Add civil litigation artifact contracts and shared IO to root intake.
2. Publish or expose the updated root intake shared IO.
3. Add this repo's command to acquire Clearinghouse API source records and write
   `CivilCases` and related typed artifacts.
4. Verify the generated `Artifacts` envelope with root intake in dry-run import.

Do not add source data to this repository while implementing those steps.
