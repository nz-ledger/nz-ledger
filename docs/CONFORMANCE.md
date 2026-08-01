# Conformance

NZ Ledger will define observable, language-neutral behaviour so independent implementations can prove equivalence. No conformance suite or certification exists in the founding repository.

## Contract formats

- JSON Schemas define versioned command, query, event, evidence, error, and report payloads.
- Canonical serialisation adopts RFC 8785 (JSON Canonicalization Scheme) as its baseline, defining Unicode handling, object-key ordering, arrays, absent versus null values, identifiers, dates, timestamps, exact decimals, hashes, and signatures. RFC 8785's known weakness, IEEE 754 number canonicalisation, does not apply because public financial values are decimal strings, never JSON numbers. The baseline is a default subject to confirmation when the canonical-serialisation design issue is worked; any departure requires recorded evidence.
- Financial values are decimal strings with explicit precision and scale rules, never floating-point JSON numbers. Draft RFC 0003 proposes the exact value model, the decimal-string grammar, per-class bounds, comparison rules, rounding modes, instrument identity, valuation provenance, and the normative allocation algorithm those vectors test.
- Compatibility rules define additive, deprecating, and breaking changes.

## Test layers

- **Schema tests:** valid, invalid, boundary, and forward-compatibility documents.
- **Canonical serialisation tests:** byte-identical results and stable hashes. Draft RFC 0004 proposes the canonical profile, value encodings, schema conventions, strict validation, and compatibility rules these vectors test, and defines the vector file format.
- **Exact-value tests:** decimal-string grammar acceptance and rejection, per-class bounds at and beyond each limit, sign and zero handling, numeric versus canonical-form equality, order-independent aggregation, and every named rounding mode at, above, and below an exact half.
- **Valuation tests:** both quotation directions, inverted direction labels, incomplete provenance records, rate bounds, and re-performance of a supplied functional amount from its quantity, rate, target scale, and mode.
- **Allocation tests:** residual distribution and tie-breaking, negative totals, zero weights, weights that do not sum to one, nominated residual targets, and the exact-sum post-condition.
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
