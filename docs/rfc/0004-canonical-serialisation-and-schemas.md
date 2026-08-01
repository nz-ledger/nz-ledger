# RFC 0004: Canonical serialisation, schemas, and compatibility

- **Status:** Draft
- **Author:** `@nz365guy` (drafted with AI assistance, Claude, August 2026)
- **Reviewers:** None yet. This RFC requires independent security review and maintainer review before acceptance. AI-assisted review may inform, but does not satisfy, that requirement
- **Created:** 2026-08-01
- **Related issues:** #6. Depends on accepted RFC 0001 and draft RFC 0003. Bounded by RFC 0005, #8, and #9

## Problem and outcome

RFC 0001 requires that identical explicit input produce identical canonical data before
transport metadata (`KRN-023`), and RFC 0003 defines what a financial value is. Neither says
what bytes a record actually becomes. Without that, two conforming implementations can hold
the same domain state and still produce different documents, different digests, and different
signatures, which makes reproducibility unverifiable and makes the tamper-evident chain in
FD-019 impossible to specify.

This RFC defines the byte-level contract. Its intended observable outcome is that two
independent implementations, given the same explicit domain state and the same accepted
contract versions, emit byte-identical documents, and that a third party holding only the
public contracts can validate, compare, and hash those documents without access to either
implementation.

It builds on draft RFC 0003 rather than an accepted one. That is permitted by RFC 0003 on the
condition that dependent work records it, and this RFC records it here: **if the exact-value
contract changes materially, the value-encoding rules below change with it.**

The words **MUST**, **MUST NOT**, **SHOULD**, and **MAY** are normative. Stable `CAN-*`
identifiers are normative.

## Scope and non-goals

### In scope

- the canonical byte representation of a contract document;
- the project profile over RFC 8785, covering numbers, strings, absent and null values;
- encodings for identifiers, dates, timestamps, enumerations, and financial values;
- JSON Schema conventions, dialect, and validation strictness;
- extension points and how unknown data is treated;
- contract versioning and the compatibility policy;
- the definition of the byte sequence that hashing and signing operate on; and
- the golden-vector file format for serialisation vectors.

### Non-goals

This RFC does not define:

- digest algorithms, signature envelopes, signer identity, key custody, or the FD-019 book
  hash chain, all of which belong to **RFC 0005**, which builds directly on this one;
- command, query, event, evidence, or error envelope contents, or the failure-name registry,
  which belong to #8;
- the conformance runner, adapter, or profile mechanics, which belong to #9;
- exact-value semantics, bounds, rounding, or valuation, which belong to RFC 0003;
- transport, compression, encryption, or storage layout; or
- any claim of legal, tax, accounting, regulatory, professional, security, or production
  approval.

## Source boundary

Researched as of August 2026, to test rather than dictate the contract. Each source is
recorded in `../SOURCE_REGISTER.md` with its access date and verification status.

- **RFC 8785, JSON Canonicalization Scheme**, is already the adopted baseline in
  `../CONFORMANCE.md`. It fixes UTF-8 encoding, object-member ordering by UTF-16 code unit,
  and string escaping. Its documented weakness is IEEE 754 number canonicalisation, which this
  profile avoids by construction rather than by care.
- **RFC 8259** defines JSON itself and is the underlying grammar.
- **JSON Schema 2020-12** remains the latest stable dialect. A successor, referred to as
  v1/2026, is in development and not released. The contract therefore declares its dialect
  explicitly in every schema so that adopting a future dialect is a recorded, versioned change
  rather than a silent one.
- **RFC 9562** obsoletes RFC 4122, defines the canonical UUID string form, and adds versions
  6, 7, and 8. Version 7 is the current recommended default where time-ordered identifiers are
  wanted.
- **RFC 3339** defines internet date and time formats and is the basis for the timestamp and
  date encodings below. It has been **updated by RFC 9557** (2024), which defines the Internet
  Extended Date/Time Format, adding optional bracketed suffixes such as `[Europe/Paris]` and
  `[u-ca=hebrew]`, a general `[key=value]` extension form, and a `!` flag marking a suffix as
  critical to process. RFC 9557 also reinterprets the `Z` designator to mean that the instant
  in UTC is known while the offset to local time is unknown. That reinterpretation suits this
  contract exactly, because the kernel records instants and treats local presentation as a
  concern above it. The suffixes do not, and are forbidden below.
- **Draft RFC 0003** supplies the decimal-string grammar and value classes used here.

## Canonical form

### Base

A canonical document is a JSON document canonicalised under **RFC 8785**, with the additional
restrictions in this section. Where this RFC is stricter than RFC 8785, this RFC governs. This
RFC never relaxes RFC 8785.

Canonical output MUST be UTF-8 with no byte order mark, no insignificant whitespace, no
trailing newline, and object members ordered as RFC 8785 requires.

### Numbers

JSON numbers are the one part of RFC 8785 the project does not rely on, so their use is
restricted rather than trusted.

- A JSON number MUST NOT be used for any financial value. Financial values are decimal strings
  under RFC 0003, which is already required by `KRN-007`.
- A JSON number MAY be used only for a non-financial integer, such as a revision counter, an
  aggregate version, a sequence position, a line ordinal, or a count.
- Such an integer MUST be within the signed 64-bit range, MUST have no fractional part, and
  MUST have no exponent.
- A non-integer JSON number MUST be rejected wherever it appears.

The result is that no value whose correctness matters ever passes through IEEE 754, so RFC
8785's number weakness cannot reach the accounting domain.

### Strings

- A string MUST be valid UTF-8 with no unpaired surrogates.
- String content MUST NOT be Unicode-normalised, case-folded, trimmed, or otherwise altered by
  serialisation. Text may be evidence, and silently rewriting it would breach `KRN-028`. A
  producer that wants normalised text normalises it before it becomes contract data, and the
  fact that it did so is its own business to record.
- Control characters other than those RFC 8785 escapes MUST be rejected in identifier,
  enumeration, and code fields. They are permitted in free-text fields, because rejecting them
  there would silently drop supplied content.

### Absent and null

RFC 8785 canonicalises whichever of these is present; it does not say what they mean. This
contract does, because conflating them is a well-worn source of financial defects.

- **Absent** means the field was not supplied.
- **Null** means the field was supplied with an explicit empty value.

These are distinct and MUST NOT be interchanged by any producer, consumer, canonicaliser, or
storage layer. Every schema MUST state, for each optional field, whether absent, null, or both
are permitted. A field that permits neither is required.

## Value encodings

| Kind | Encoding | Notes |
| --- | --- | --- |
| Financial value | Decimal string under RFC 0003, with its declared value class | Never a JSON number. |
| Internal identifier | RFC 9562 UUID in canonical string form, lowercase, hyphenated | Any UUID version. See the rule below. |
| External identifier | Object of issuer or namespace, type, and value, per `KRN-018` | A bare value is never identity. |
| Date | RFC 3339 `full-date`, `YYYY-MM-DD` | No time, no offset. Used for accounting dates. |
| Timestamp | RFC 3339 `date-time`, UTC only, trailing `Z`, exactly six fractional digits | See the rule below. |
| Enumeration | Lowercase ASCII string from a closed set declared in the schema | No numeric enumerations. |
| Boolean | JSON `true` or `false` | Never `0`, `1`, or a string. |
| Integer | JSON number within signed 64-bit range | Non-financial only. |
| Binary | Base64url without padding, in a field explicitly typed as binary | Length bounded by the schema. |

### Identifiers and embedded time

Internal identifiers use the RFC 9562 canonical string form and MAY be of any UUID version, so
that an implementation wanting time-ordered index locality can use version 7 while one wanting
no embedded time can use version 4. Canonical bytes are identical in either case.

**Any timestamp embedded in an identifier has no accounting meaning.** It MUST NOT be read as,
substituted for, or reconciled against effective time, accounting date, or recorded time, and
MUST NOT be used to order accounting facts. `KRN-016` requires the three temporal meanings to
be explicit and distinct, and an identifier that quietly carries a fourth would undermine that
for anyone reading records rather than contracts.

### Timestamps

Timestamps are RFC 3339 date-time values in UTC, with a trailing `Z` and exactly six
fractional digits, for example `2026-08-01T04:12:33.000000Z`.

- A local offset MUST NOT be used. An offset is presentation, and preserving it would create
  two byte representations of one instant.
- Fractional precision is fixed rather than variable so that the same instant has exactly one
  canonical form. Six digits is chosen because it is the precision of the PostgreSQL reference
  store selected in FD-005, so a round trip through the reference persistence cannot silently
  change canonical bytes.
- A producer with coarser precision pads with zeros. A producer with finer precision MUST
  reject the value rather than truncate it, because truncation would silently reorder facts
  that differ only below the sixth digit.
- **RFC 9557 IXDTF suffixes MUST be rejected**, including time-zone names, calendar tags, any
  `[key=value]` extension, and any suffix marked critical with `!`. Two reasons. Permitting
  them would give one instant many canonical forms, defeating the whole contract. And a
  critical-flagged suffix demands that a consumer either honour it or refuse the value, so a
  kernel that ignored one would be silently discarding data its producer marked as essential,
  which is the same defect that strict field validation exists to prevent.

Under the RFC 9557 reinterpretation, the trailing `Z` asserts that the instant in UTC is known
and says nothing about any local offset. That is precisely the claim this contract intends to
make, and it is why local offsets are excluded rather than merely discouraged.

## Schemas

### Dialect and conventions

- Schemas use **JSON Schema 2020-12** and MUST declare `$schema` explicitly. The successor
  dialect in development is a watch item, and adopting it will be a recorded change under the
  compatibility policy below, never an incidental upgrade.
- Every schema declares a stable `$id` containing its contract name and major version.
- Property names are `lower_snake_case` ASCII.
- Every schema is self-contained or references only other project schemas by `$id`. A schema
  MUST NOT reference a schema fetched from an external network location at validation time.

### Strictness

Every object in a contract document MUST set `additionalProperties: false`, and a document
carrying a field the schema does not define MUST be rejected.

This is the decision in this RFC most worth challenging, so the reasoning is stated plainly.
The common convention is that readers ignore fields they do not understand, which makes
rolling upgrades easy. In a financial contract that convention is a hazard: a field that a
producer considered material, such as a condition, a limit, a correction marker, or an
authority qualifier, would be silently discarded by an older reader, which would then accept a
record it did not fully understand. The project's posture elsewhere is to fail closed, as
`KRN-026` does for control accounts, and this follows it.

The cost is real and is accepted: adding a field is a coordinated change rather than a free
one, and the compatibility rules below exist to make that manageable.

### Extension points

Because strict validation would otherwise make extension impossible, a schema MAY declare an
explicit extension container at a named location. Data inside an extension container:

- is carried, stored, and canonicalised unchanged;
- is included in canonical bytes, so it is covered by digests and signatures;
- MUST NOT affect any kernel invariant, validation outcome, balance, or posting decision; and
- MUST be namespaced by the extension's identifier, so two extensions cannot collide.

An extension cannot make a document valid that would otherwise be invalid, which preserves
FD-015.

## Versioning and compatibility

Each contract carries an explicit version as `MAJOR.MINOR.PATCH`.

| Change | Version effect | Permitted |
| --- | --- | --- |
| Adding an optional field | MINOR | Yes |
| Adding a required field | MAJOR | Yes |
| Removing or renaming any field | MAJOR | Yes |
| Narrowing a value bound or an enumeration | MAJOR | Yes, because a document that used to validate may not |
| Widening a value bound or an enumeration | MAJOR | Yes, because an older reader will reject the new documents |
| Marking a field deprecated without removing it | MINOR | Yes |
| Changing the meaning of an existing field | Not permitted | Issue a new field instead |
| Changing canonical encoding of any value kind | MAJOR | Yes, and it invalidates prior digests, so RFC 0005 must say what happens to existing chains |
| Editorial change with no effect on validation or bytes | PATCH | Yes |

Both widening and narrowing are major changes here, which is stricter than usual. Under strict
validation, a widened bound produces documents an older reader rejects, so calling it minor
would be untrue. RFC 0003 already anticipated this for value bounds.

A document declares the contract version it was produced against. A consumer MUST reject a
document whose major version it does not implement, rather than attempting a best-effort read.

## Hashing and signing input

This RFC defines only the input. Algorithms, signature envelopes, signer identity, and the
FD-019 book hash chain are specified in RFC 0005.

The input to any digest or signature over a contract document is **the exact canonical byte
sequence defined by this RFC**, with no framing, whitespace, transport metadata, or
re-encoding. Two implementations that agree on this RFC therefore agree on the bytes to be
hashed before they agree on anything about hashing, which is the property FD-019 needs.

A digest computed over a re-serialised, pretty-printed, or transport-wrapped form is invalid.

## Golden-vector format

Serialisation vectors are published as JSON files with this shape. The runner, adapters, and
profiles belong to #9.

```json
{
  "vector_set": "canonical-serialisation",
  "contract_version": "1.0.0",
  "vectors": [
    {
      "id": "CAN-V0001",
      "description": "object member ordering is by UTF-16 code unit",
      "input": { "b": "2", "a": "1" },
      "expect": "accept",
      "canonical": "{\"a\":\"1\",\"b\":\"2\"}"
    },
    {
      "id": "CAN-V0002",
      "description": "a JSON number is rejected as a financial value",
      "input": { "amount": 10.5 },
      "expect": "reject",
      "failure": "FINANCIAL_VALUE_INVALID"
    }
  ]
}
```

Every vector has a stable identifier, a description, an input, and either an expected
canonical output or an expected failure name. A vector MUST NOT depend on the current time, a
random value, a locale, or a platform.

## Invariant catalogue

Failure names are stable semantic names proposed for registration in #8.

| ID | Normative statement | Rationale | Valid example | Invalid example | Expected failure | Security consequence |
| --- | --- | --- | --- | --- | --- | --- |
| `CAN-001` | A canonical document MUST be RFC 8785 output under this profile: UTF-8, no byte order mark, no insignificant whitespace, no trailing newline, members ordered as RFC 8785 requires. | One document, one byte sequence. | Members emitted in code-unit order. | Members emitted in insertion order. | `CANONICAL_FORM_INVALID` | Divergent digests make integrity claims unverifiable. |
| `CAN-002` | A JSON number MUST NOT carry a financial value. | Keeps every value that matters out of IEEE 754. | `"amount": "10.50"`. | `"amount": 10.50`. | `FINANCIAL_VALUE_INVALID` | Precision loss and reader-dependent reinterpretation. |
| `CAN-003` | A JSON number MUST be an integer within signed 64-bit range with no fraction and no exponent. | Bounds the only numeric type that remains. | `"version": 8`. | `"version": 8.0` or `1e3`. | `NUMBER_INVALID` | Overflow and parser differentials. |
| `CAN-004` | Strings MUST be valid UTF-8 without unpaired surrogates, and string content MUST NOT be normalised, folded, trimmed, or otherwise altered by serialisation. | Text may be evidence; rewriting it breaches `KRN-028`. | Supplied text preserved exactly. | Serialiser applies NFC to a description field. | `STRING_ALTERED` | Silent modification of evidence-bearing content. |
| `CAN-005` | Absent and null MUST be distinct and MUST NOT be interchanged, and every schema MUST state which are permitted per optional field. | The two mean different things and conflating them changes meaning. | Absent means not supplied; null means explicitly empty. | A storage layer converts absent to null on round trip. | `NULLABILITY_INVALID` | A supplied value and a missing one become indistinguishable. |
| `CAN-006` | Internal identifiers MUST use the RFC 9562 canonical string form, lowercase and hyphenated. | One recognisable, standard shape. | `0192f9c3-...` lowercase. | Uppercase, braced, or unhyphenated forms. | `IDENTIFIER_FORM_INVALID` | Identifier comparison failures and substitution. |
| `CAN-007` | A timestamp embedded in an identifier MUST NOT be read as, substituted for, or reconciled against effective time, accounting date, or recorded time, and MUST NOT order accounting facts. | `KRN-016` requires three explicit distinct times; identity must not smuggle in a fourth. | Version 7 identifiers used purely as identity. | A report orders journals by identifier timestamp. | `TEMPORAL_BASIS_INVALID` | False chronology that reconciles internally and is wrong. |
| `CAN-008` | Timestamps MUST be RFC 3339 UTC with trailing `Z` and exactly six fractional digits. A value with finer precision MUST be rejected, not truncated. | One instant, one canonical form, matching the reference store's precision. | `2026-08-01T04:12:33.000000Z`. | `2026-08-01T16:12:33+12:00` or nanosecond precision. | `TIMESTAMP_FORM_INVALID` | Reordering and duplicate representations of one instant. |
| `CAN-009` | Dates MUST be RFC 3339 `full-date` with no time and no offset. | An accounting date is a date, and attaching a zone invites conversion. | `2026-08-01`. | `2026-08-01T00:00:00Z`. | `DATE_FORM_INVALID` | Period misassignment across a zone boundary. |
| `CAN-010` | Every object MUST set `additionalProperties: false`, and a document carrying an undefined field MUST be rejected. | A field the producer considered material must never be silently discarded. | Unknown field rejected with a named failure. | Unknown field ignored and the record accepted. | `UNKNOWN_FIELD` | A record is accepted by a reader that did not understand it. |
| `CAN-011` | Extension data MUST be namespaced, carried unchanged, included in canonical bytes, and MUST NOT affect any invariant, validation outcome, or posting decision. | Preserves FD-015 while making extension possible under strict validation. | Namespaced extension object ignored by kernel logic. | An extension field changes whether a journal balances. | `EXTENSION_SCOPE_VIOLATION` | Extensions bypassing financial and security controls. |
| `CAN-012` | Every schema MUST declare its dialect and a stable versioned `$id`, and MUST NOT reference a schema fetched from an external network location at validation time. | Validation must be reproducible and offline. | Local `$ref` by project `$id`. | `$ref` to a third-party URL. | `SCHEMA_REFERENCE_INVALID` | Remote content changing validation outcomes, or validation-time network dependency. |
| `CAN-013` | A consumer MUST reject a document whose declared major contract version it does not implement. | A best-effort read of an unknown major version is a guess about financial data. | Reject and report the version. | Parse the fields that look familiar. | `CONTRACT_VERSION_UNSUPPORTED` | Silent misinterpretation of an evolved contract. |
| `CAN-014` | Widening and narrowing a value bound or enumeration are both MAJOR changes. | Under strict validation each breaks one side, so neither is minor. | Widening a bound ships as a major version. | Widening shipped as minor and older readers reject valid documents. | `COMPATIBILITY_RULE_VIOLATION` | Undetected interoperability failure between versions. |
| `CAN-015` | The input to any digest or signature MUST be the exact canonical byte sequence, with no framing, whitespace, transport metadata, or re-encoding. | The FD-019 chain is only verifiable if the hashed bytes are unambiguous. | Digest over canonical bytes. | Digest over a pretty-printed copy. | `DIGEST_INPUT_INVALID` | Unverifiable integrity claims and chain breakage. |
| `CAN-017` | RFC 9557 IXDTF suffixes MUST be rejected on any date or timestamp, including time-zone names, calendar tags, `[key=value]` extensions, and suffixes marked critical with `!`. | Suffixes give one instant many forms, and silently ignoring a critical-flagged suffix discards data the producer marked essential. | `2026-08-01T04:12:33.000000Z`. | `2026-08-01T16:12:33+12:00[Pacific/Auckland]` or `...[!u-ca=hebrew]`. | `TIMESTAMP_FORM_INVALID` | Divergent canonical forms, or a consumer discarding data flagged as mandatory. |
| `CAN-016` | Canonicalisation MUST be deterministic and MUST NOT depend on locale, platform, insertion order, map iteration order, or system time. | `KRN-023` makes reproducibility a financial control. | Same input, same bytes, any host. | Output varies with locale collation. | `NONDETERMINISTIC_RESULT` | Results that cannot be audited or reproduced. |

## Boundary and failure vectors

**Canonical form:** member reordering; duplicate keys; nested object ordering; empty object and
empty array; byte order mark present; trailing newline; insignificant whitespace.

**Numbers:** a financial value as a JSON number; a non-integer number; an exponent; a value
above the signed 64-bit range; negative zero as a number.

**Strings:** invalid UTF-8; unpaired surrogate; a string that differs from its NFC form, which
MUST survive unchanged; control characters in an identifier field and in a free-text field.

**Absent and null:** a field absent, the same field null, and confirmation that the two produce
different canonical bytes; a round trip that must not convert one to the other.

**Identifiers:** uppercase, braced, and unhyphenated UUID forms; a version 7 and a version 4
identifier accepted equally; a report attempting to order by embedded identifier time.

**Timestamps and dates:** a local offset; three, six, and nine fractional digits; a date carrying
a time component; a leap-second representation; an IXDTF time-zone suffix; an IXDTF calendar
tag; an IXDTF suffix marked critical with `!`.

**Schemas:** an undefined field; an extension container with namespaced content; an extension
field attempting to change a validation outcome; a schema with a remote `$ref`.

**Versioning:** a document declaring an unsupported major version; a widened bound presented as
a minor change.

## Alternatives

### Define a bespoke canonicalisation instead of using RFC 8785

Rejected. A published scheme with existing implementations is more likely to be implemented
correctly than a project-specific one, and its known weakness is avoidable by restricting
numbers rather than by inventing a new format.

### Use a binary encoding such as CBOR or Protocol Buffers

Rejected for the public contract. Canonical binary encodings are compact and have their own
determinism rules, but the project's records must remain legible to a human auditor reading an
export, and the assurance argument in draft RFC 0002 depends on that legibility. A binary
transport encoding remains possible above this contract, provided digests are still computed
over these canonical bytes.

### Ignore unknown fields, the usual convention

Rejected, with the cost accepted, for the reason given under Strictness. A silently discarded
field the producer considered material is a financial defect, not a compatibility feature.

### Adopt RFC 9557 IXDTF suffixes

Rejected for contract data. The annotations are genuinely useful for conveying a user's time
zone or calendar alongside an instant, but that is presentation, and presentation belongs above
the kernel. Carrying them in canonical data would multiply the representations of one instant
and would oblige every consumer to implement critical-flag handling correctly. Where an
originating time zone or calendar genuinely matters to a business fact, it belongs in an
explicit named field with its own schema, not in a suffix on a timestamp.

### Allow variable timestamp precision

Rejected. Variable precision gives one instant several canonical forms, which breaks digests
for no benefit that fixed precision plus explicit padding does not already provide.

### Mandate UUIDv7 for all identifiers

Rejected. It would give uniform index locality, but it forces every identifier to carry an
implicit creation time that a reader could mistake for one of the three explicit temporal
values. Permitting any version, with `CAN-007` denying the embedded time any meaning, keeps the
benefit available without the hazard.

### Normalise strings to NFC during serialisation

Rejected. Normalisation changes bytes the producer supplied, and text in this system may be
evidence. Producers who want normalised text normalise before the data becomes contract data.

## Consequences

### Accounting integrity

- `KRN-023` becomes testable at the byte level rather than in principle.
- Every value whose correctness matters is kept out of IEEE 754 by construction.
- Absent and null stop being interchangeable, closing a common defect.
- An identifier can no longer imply a time that contradicts the explicit temporal record.

### Security and privacy

- Strict validation fails closed on unknown data instead of accepting a partly understood
  record.
- Extensions are carried and signed but cannot influence a decision, preserving FD-015.
- Validation has no network dependency, so no third party can change a validation outcome.
- Bounded numbers, bounded binary fields, and rejection rather than truncation limit what
  hostile input can achieve.
- This RFC introduces no personal or financial data; it is structural.

### New Zealand policy

Nothing here interprets a New Zealand rule. Presentation formats, including any local date or
number display, remain a presentation concern above the kernel.

### Compatibility and operations

- RFC 0005 receives an unambiguous byte sequence to digest, sign, and chain.
- #8 receives encodings and a set of failure names to register.
- #9 receives a vector file format.
- A change to canonical encoding invalidates prior digests, so RFC 0005 must state what
  happens to an existing chain when this contract has a major version change. That is recorded
  as an open question rather than assumed away.

## Validation

1. Every value kind has exactly one canonical encoding.
2. Every `CAN-*` invariant is judged mechanically testable in principle.
3. The vector families cover canonical form, numbers, strings, nullability, identifiers,
   temporal values, schemas, extensions, and versioning.
4. During sole-maintainer bootstrap, the maintainer MAY accept this documentation-only RFC
   after recording a self-review and disclosing that it is not independent. Independent
   security review MUST record whether parsing strictness, canonicalisation determinism,
   extension isolation, and digest-input integrity are addressed. That review is tracked as
   debt in `../../GOVERNANCE.md` and issue #38.
5. Maintainer review confirms consistency with RFC 0001, draft RFC 0003, the founding
   decisions, the conformance direction, and the threat model.
6. RFC 0005, #8, and #9 confirm or refine the deferred details without weakening these
   invariants.
7. Documentation links and source-register entries are current, `git diff --check` passes,
   required repository checks pass, and the DCO sign-off is present.

## Decision register

| ID | Decision | Status | Rationale | Owner or follow-up |
| --- | --- | --- | --- | --- |
| `CAN-D001` | RFC 8785 is the canonical base, with a project profile that is stricter and never looser. | Proposed | Reuses a published scheme rather than inventing one. | #6 |
| `CAN-D002` | JSON numbers are restricted to bounded non-financial integers. | Proposed | Removes RFC 8785's number weakness by construction. | #6 |
| `CAN-D003` | String content is never normalised or altered by serialisation. | Proposed | Text may be evidence under `KRN-028`. | #6, evidence model in #10 |
| `CAN-D004` | Absent and null are distinct and every schema declares which are permitted. | Proposed | Conflating them changes meaning silently. | #6, envelopes in #8 |
| `CAN-D005` | Identifiers are RFC 9562 UUIDs of any version, and embedded time has no accounting meaning. | Proposed | Keeps index locality available without letting identity imply a fourth time. | #6 |
| `CAN-D006` | Timestamps are RFC 3339 UTC with exactly six fractional digits, rejecting finer precision, and RFC 9557 IXDTF suffixes are rejected outright. | Proposed | One canonical form per instant, matching the FD-005 reference store, and no silently ignored critical suffix. | #6, persistence in #11 |
| `CAN-D007` | Schemas use JSON Schema 2020-12, declared explicitly, with no validation-time network references. | Proposed | Current stable dialect; reproducible offline validation. | #6 |
| `CAN-D008` | Validation is strict: unknown fields are rejected, and extension data is namespaced, carried, signed, and inert. | Proposed | A discarded material field is a financial defect; fail closed as elsewhere. | #6, extension model in #18 |
| `CAN-D009` | Both widening and narrowing a bound are major changes. | Proposed | Under strict validation each breaks one side. | #6 |
| `CAN-D010` | Digest and signature input is the exact canonical byte sequence. | Proposed | FD-019 verifiability depends on unambiguous input. | RFC 0005 |

## Open questions

| ID | Question | Required owner |
| --- | --- | --- |
| `CAN-Q001` | What happens to an existing FD-019 chain when this contract takes a major version change that alters canonical bytes? | RFC 0005 |
| `CAN-Q002` | Which digest algorithm and agility mechanism are adopted, and how is the algorithm recorded alongside a digest? | RFC 0005 |
| `CAN-Q003` | Should a transport binary encoding be permitted above this contract, and if so how is the canonical-bytes digest preserved through it? | #8 |
| `CAN-Q004` | When the successor JSON Schema dialect is released, what migration path applies to published schemas? | #6 revisit, tracked as a watch item |
| `CAN-Q005` | Does any contract need a free-text field large enough to require a length bound beyond the general schema bounds? | #8 |
| `CAN-Q006` | How are leap seconds represented or rejected, given RFC 3339 permits second value 60? | #6 with security review |

## Review record

### Security review

None yet. A security review MUST cover parsing strictness, canonicalisation determinism,
extension isolation, digest-input integrity, and resource bounds before acceptance.

### Maintainer review

None yet.

## Decision

Not yet decided. This RFC is a draft and MUST NOT be treated as accepted design or used to
justify implementation work. It depends on draft RFC 0003, which is itself unaccepted, and a
material change there changes the value encodings here.
