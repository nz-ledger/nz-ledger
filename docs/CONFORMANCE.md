# Conformance

NZ Ledger will define observable, language-neutral behaviour so independent implementations can prove equivalence. No conformance suite or certification exists in the founding repository.

## Contract formats

- JSON Schemas define versioned command, query, event, evidence, error, and report payloads.
- Canonical serialisation defines Unicode handling, object-key ordering, arrays, absent versus null values, identifiers, dates, timestamps, exact decimals, hashes, and signatures.
- Financial values are decimal strings with explicit precision and scale rules, never floating-point JSON numbers.
- Compatibility rules define additive, deprecating, and breaking changes.

## Test layers

- **Schema tests:** valid, invalid, boundary, and forward-compatibility documents.
- **Canonical serialisation tests:** byte-identical results and stable hashes.
- **Command tests:** identity, mandate, idempotency, effective and recorded time, invariants, and error behaviour.
- **Query tests:** authorisation, temporal basis, pagination, redaction, and stable result semantics.
- **Event tests:** envelope, ordering boundary, duplication, replay, redaction, and compatibility.
- **Evidence tests:** hashing, versions, provenance, classification, retention metadata, and access.
- **Golden accounting vectors:** exact posting, reversal, adjustment, period, dimension, allocation, multicurrency, large value, and negative value behaviour.
- **Policy-pack vectors:** effective dates, supersession, rounding, historical reproduction, and source-linked examples.
- **Failure vectors:** unknown input, missing evidence, limit breach, invariant violation, stale version, and unavailable dependency.

## Black-box runner

The future runner will send published inputs through a documented adapter and compare canonical outputs, errors, events, and hashes. It must not depend on implementation internals or storage layout. Runs record suite version, contract version, implementation identity and version, environment, selected profiles, and results.

Determinism requires the same explicit inputs and versions to produce the same canonical financial result. Cross-language runners use identical vectors and comparison rules.

## Conformance claims

A claim must name the implementation version, conformance-suite version, contract profile, policy-pack versions, test environment, result, date, and known exclusions. Preferred wording is:

> This implementation passed NZ Ledger conformance suite `<version>` for profile `<profile>` under the recorded environment. This is a technical compatibility result, not legal, tax, accounting, regulatory, security, or production approval.

No generic "NZ Ledger certified" wording is permitted without a future accepted governance and assurance process.
