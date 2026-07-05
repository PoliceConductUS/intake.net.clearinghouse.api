# Civil Case Artifact Model

This document captures the current working model for Clearinghouse civil-case
artifacts produced by this source module. Root intake owns the final shared IO
schema and import behavior, but this repo should use these decisions when
building the producer.

## Importable Case Rule

A Clearinghouse record is importable as a civil case only when it can be tied to
at least one explicit, resolvable law-enforcement agency association.

The producer should not emit importable case artifacts for records where agency
association is missing, ambiguous, or unsupported by source evidence. Those
records should be written to a command-local manual association candidate file.

Officer associations can be named officers or the agency's unknown officer. The
unknown officer is modeled as a normal personnel record, with exactly one
unknown officer per agency. Root intake resolves or creates that agency-scoped
unknown officer record as needed.

## Required CivilCase Fields

Each importable `CivilCase` record requires:

- `title`
- `source_case_id`
- `source_url`
- `court`
- `case_number`
- `filed_date`
- `jurisdiction`
- `claims_summary`

The source-local record key should be derived from stable Clearinghouse identity,
such as the Clearinghouse case ID or durable case URL segment.

## Jurisdiction

Jurisdiction is independent from incident location.

Jurisdiction describes the legal forum and court context. The first schema
should support structured court data while leaving room for later first-class
court actors:

```yaml
jurisdiction:
  court: <court name or source court label>
  judges:
    - <plain-text judge name>
```

`jurisdiction.court` is required as part of the required `jurisdiction` object.
Judges can be plain text for now. Do not resolve judges to canonical entities in
this producer until root intake has a first-class court actor model.

## Incident

Incident describes where the underlying police-conduct event occurred. It is not
the same as jurisdiction.

The first schema should support:

```yaml
incident:
  location_path_id: <source-local or canonical-compatible location path key>
```

If incident location cannot be resolved safely, the case should go to manual
association candidates instead of being emitted as an importable case, unless a
later root contract explicitly supports unresolved incident location.

## Plaintiffs

Plaintiffs are plain text in the first version. There is no canonical plaintiff
association.

```yaml
plaintiffs:
  - Estate of Example Person
  - Jane Doe
```

## Defendants And Agency Personnel

Defendants are the entities associated with the case. The initial model should
support agencies and agency personnel. Location path defendants, such as a city
or county government, may be needed when the legal defendant is a municipality
or county rather than the police department itself.

For agency personnel, model officers under the agency context. This prevents
officer associations without an agency and makes unknown officers simple:

```yaml
defendants:
  - agency:
      agency_id: minneapolis-police-department
      personnel:
        - ref:
            name: derek-chauvin
        - unknownOfficer: {}
```

Rules:

- `agency_id` is a source-local agency key unless root intake says otherwise.
- `personnel` is non-empty for importable police-conduct case associations.
- Each personnel item contains exactly one supported shape.
- `unknownOfficer: {}` means the agency-scoped unknown officer for the parent
  agency.
- The producer must not create canonical IDs.

## Links

Each case should include at least the Clearinghouse source URL. Additional links
can include dockets, opinions, complaints, settlement documents, findings
letters, or source-context pages when available.

```yaml
links:
  - url: https://clearinghouse.net/case/123
    title: Clearinghouse case page
    type: source
```

## Manual Association Candidates

Manual association candidates are command-local files for records that should
not become importable artifacts yet.

Use manual candidates for:

- police-related records with unresolved agency association
- agency-resolved records where officer handling needs human review and the root
  unknown-officer contract is not available
- ambiguous source classification
- case-to-case relationships before concrete examples and root schema support
- non-court civil-rights investigations before concrete examples and root schema
  support

The file should preserve source evidence and source-local identifiers. It should
not contain canonical IDs.

## Deferred Decisions

### Case Relationships

Do not model case-to-case relationships until concrete Clearinghouse examples
are available. Possible future relationship types may include:

- appeal of
- related docket
- consolidated with
- settlement related to
- enforcement action for

### Non-Court Civil-Rights Investigations

Do not decide whether non-court civil-rights investigations belong in
`CivilCase` until concrete Clearinghouse examples are available. They may require
a separate future artifact kind instead of stretching the case model.

### Events

Procedural events are v2. Do not add events to the first artifact shape.

## Likely Future Additions

The first model should leave room for these additions without implementing them
prematurely:

- judges as first-class entities
- city attorneys as first-class entities
- prosecutors as first-class entities
- courts as first-class entities
- plaintiffs beyond plain text
- normalized constitutional and statutory claims
- settlement amounts and monetary relief
- injunctive relief and consent decree terms
- monitors and monitoring reports
- procedural events and docket history
- case-to-case relationships
- document-level artifacts for complaints, orders, findings letters, settlement
  agreements, and consent decrees
