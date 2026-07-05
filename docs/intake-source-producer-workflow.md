# Intake Source Producer Workflow

This repository is an intake source module. Source modules produce artifacts for
root intake; they do not import directly into the database and do not own
canonical database identity.

## Non-Negotiable Rules

- Do not commit source data to this repository.
- Do not write canonical database IDs in source-produced artifacts.
- Do not write root intake state.
- Do not hand-roll YAML envelope parsing, validation, serialization, filenames,
  or path conventions.
- Do not read or write outside the paths granted by the command envelope.
- Do not silently skip malformed, missing, or ambiguous source records.
- Do not make persistence, caching, source acquisition, or artifact-scope
  decisions without checking root intake contracts and current project guidance.

## Command Boundary

Root intake owns command creation. A source-module command is executed from a
`Command` envelope using:

```yaml
apiVersion: policeconduct.org/intake/v1alpha1
kind: Command
metadata:
  namespace: clearinghouse-api
  name: <command-name>
spec:
  path: <absolute-command-output-path>
  statePath: <absolute-module-state-path>
  sharedIoRoot: <absolute-root-intake-shared-io-root>
  args:
    - artifacts
    - create
```

`spec.path` is the command folder. It is the audit unit for that execution:
logs, preserved source files, provenance, digests, generated artifacts, and
command-local diagnostics belong there.

`spec.statePath` is this module's reusable state/cache folder. It may hold
source acquisition caches or deterministic resolver state owned by this module.
It must not hold root intake `SourceNameToCanonicalId` records or canonical
database state.

`spec.sharedIoRoot` points to root intake shared IO. Every intake envelope read
or written by this module must use that shared IO when the envelope kind is part
of the root intake contract.

## Source Acquisition

The command should acquire data from the Clearinghouse API or an approved
workspace cache. Acquired source data is evidence-like material.

For each source payload or file, preserve:

- the source URL or request identity
- request parameters that affect results
- retrieval time when relevant
- local command-folder path
- byte count when available
- SHA-256 digest
- record count when available

The command folder should contain the source material needed to audit or
recreate the generated artifacts for that command. Reusable downloaded source
caches may live under `spec.statePath`, but each command should still copy or
materialize the source evidence it used under `spec.path`.

## Stable Source Identity

Use source-provided stable identifiers whenever the Clearinghouse API provides
them. If a record has no stable source ID, derive a stable source-local name from
source fields that are part of the source record identity, and document that
derivation in code and tests.

Stable source names become `spec.records` keys in typed artifact envelopes.
They are not canonical database IDs. Root intake maps:

```text
apiVersion + source namespace + source record kind + source record name
```

to canonical cuid2 IDs through intake-owned state.

## Artifact Generation

The producer writes:

- a root `Artifacts` envelope
- typed artifact envelopes referenced by the root envelope
- source provenance files and digests in the command folder
- command-local diagnostics when needed

Every envelope must use:

```yaml
apiVersion: policeconduct.org/intake/v1alpha1
metadata:
  namespace: clearinghouse-api
```

The root `Artifacts` envelope should reference typed artifact files with
relative paths and SHA-256 digests. Typed records should be stored under
`spec.records`, keyed by stable source-local record name.

The producer should use root intake shared IO so the canonical writer injects
the supported `apiVersion`, validates the exact `kind`, owns filenames, rejects
unknown fields, and computes digests.

## Resolving Missing Properties

If a required artifact property cannot be read directly from the source record,
the command may resolve it only through an explicit, auditable stage:

- declare the resolver input
- make the resolver deterministic for that input
- cache reusable resolver output under `spec.statePath` only when the cache
  belongs to this source module
- preserve source evidence and resolver evidence
- fail loudly when the missing property cannot be resolved safely

Root intake owns canonical-entity resolved-property caches. Source modules must
not write canonical resolved-property state unless root intake explicitly grants
that behavior through a shared contract.

## Current Clearinghouse Scope

The first useful target is a narrow civil-litigation artifact slice after root
intake supports the required artifact kinds:

- `CivilCases`
- `CivilCaseLinks`
- relationship artifacts only when source records provide explicit, stable, and
  low-risk relationship evidence

Reconfirm scope before adding agency/personnel matching, coverage-link artifacts,
or broad taxonomy fields.

## Root Contracts

Read these root intake documents before changing command behavior, workspace
layout, artifact shape, source identity, or persistence behavior:

- `/Users/dalelotts/dev/PoliceConductUS/intake/AGENTS.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0001-define-intake-envelope-contract.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0005-use-source-specific-artifact-producers.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0010-own-shared-intake-workspace-layout.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/docs/adr/0014-implement-commands-as-configurable-elt-pipelines.md`
- `/Users/dalelotts/dev/PoliceConductUS/intake/openspec/specs/artifacts-database-import/spec.md`
