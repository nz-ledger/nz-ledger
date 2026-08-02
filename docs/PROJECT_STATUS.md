# Project status

**Last updated:** 3 August 2026 (Pacific/Auckland)

This page answers one question: where is NZ Ledger right now. It is a current record, not a
historical snapshot, and it is updated when work lands. Where another document is
authoritative, this page links to it rather than restating it, so there are few places for it
to go stale.

No private data, personal data, credentials, tokens, recovery material, or secrets are
recorded here. Nothing on this page claims legal, tax, accounting, government, regulatory,
professional, security, or production approval.

## Where the project is

Phase 0 is complete and Phase 1 is in progress. See [`ROADMAP.md`](../ROADMAP.md) for what
each phase means.

The repository is documentation only. No application, API, SDK, CLI, database, package, or
hosted service exists, and none will until the gates below are passed.

## In flight

Draft RFC 0003, the exact-value contract, is in the repository and awaiting a decision on
acceptance. It defines what a financial value is: exact coefficient and scale serialised as
decimal strings, bounds per semantic value class, comparison and aggregation rules, named
rounding modes with no default, typed instrument identity, valuation provenance, and
allocation residual ownership. It is the Phase 1 critical-path item, because canonical
serialisation, contract envelopes, conformance vectors, multicurrency, and digital-instrument
work all depend on it.

It is a draft. It has had no accounting-domain review, no security review, and no maintainer
review, and it must not be cited as settled design.

Draft RFC 0004, canonical serialisation, schemas, and compatibility, is also in the repository
and awaiting a decision. It covers the first half of issue #6: canonical bytes, value
encodings, JSON Schema conventions, strict validation, identifier syntax, and the compatibility
policy. Digests, signing, and the FD-019 hash chain are deliberately left to RFC 0005, which
builds on it. RFC 0004 depends on draft RFC 0003, and records that a material change there
changes its value encodings.

## Decided and proposed

| Record | Status | Where |
| --- | --- | --- |
| Founding decisions FD-001 to FD-020 | Locked | [`DECISIONS.md`](DECISIONS.md) |
| RFC 0001, canonical accounting domain and invariants | Accepted, with independent review deferred | [`rfc/0001`](rfc/0001-canonical-accounting-domain.md) |
| ADR 0001, TypeScript reference implementation | Accepted | [`adr/0001`](adr/0001-typescript-reference-implementation.md) |
| RFC 0002, auditability and assurance | Draft, not accepted | [`rfc/0002`](rfc/0002-auditability-and-assurance.md) |
| RFC 0003, exact values, instruments, and valuations | Draft, not accepted | [`rfc/0003`](rfc/0003-exact-values-instruments-valuations.md) |
| RFC 0004, canonical serialisation, schemas, and compatibility | Draft, not accepted | [`rfc/0004`](rfc/0004-canonical-serialisation-and-schemas.md) |

The [RFC index](rfc/README.md) is authoritative for RFC status. A draft carries no weight in
dependent design beyond being a proposal that dependent work may build against, provided it
records that it did so.

## What is blocking the project

One thing, and it is not technical.

RFC 0001 was accepted under the sole-maintainer bootstrap exception recorded in
[`GOVERNANCE.md`](../GOVERNANCE.md). Its accounting-domain review was a self-review by the
author, disclosed as such. **No Phase 2 implementation work may be accepted, and no release
gate may pass, until RFC 0001 receives independent human accounting-domain review by someone
who is not the author.** AI-assisted review may inform that requirement but does not satisfy
it.

Deferred reviews are tracked as debt, not waived, in public issues labelled
`independent-review-required`, in priority order. Accepting RFC 0003 would add the exact-value
contract to that list.

Recruiting a suitably qualified independent reviewer is therefore the highest-value
contribution anyone can make to this project at present, ahead of any design or code work.
The request is set out in full in [`REVIEWER_REQUEST.md`](REVIEWER_REQUEST.md), including what
is asked, what is out of scope, and what the review explicitly is not.
Volunteers comment on [issue #36](https://github.com/nz-ledger/nz-ledger/issues/36), which is
the single thread for it.

## Open maintainer decisions

- Whether to accept RFC 0003. Acceptance settles the exact-value contract for dependent design
  and incurs the independent-review debt above.
- `DOM-Q013`, whether a minimum of two lines is universally required for a valid posted
  journal. RFC 0003 proposes retaining the minimum and records the argument against. This is
  an accounting-domain judgement, so it stays open until independent review.

## Next work, in order

1. Recruit an independent New Zealand accounting or tax domain reviewer for RFC 0001. Human
   work, and the binding constraint on everything downstream.
2. Decide whether RFC 0003 is accepted.
3. RFC 0005, digests, FD-018 signing, and the FD-019 tamper-evident book hash chain, building
   directly on draft RFC 0004. This completes the second half of issue #6.
4. Refine draft RFC 0002. The SAF-T research it depended on is complete and recorded in the
   source register and in the RFC: SAF-T alignment is proposed as an optional derived export
   profile at module level only, never the canonical export. One research lead remains open in
   the register: verifying the New Zealand eInvoicing timeline against MBIE primary sources.
5. Issue #8, command, query, event, and evidence contracts, which registers the failure names
   RFC 0003 and RFC 0004 propose, then issue #9, golden vectors and the conformance approach.

The [Phase 1 milestone](https://github.com/nz-ledger/nz-ledger/milestone/2) and the issue
backlog remain authoritative for scope and priority.

## Known gaps in the evidence base

Not every source in [`SOURCE_REGISTER.md`](SOURCE_REGISTER.md) has been verified to the same
standard, and the register says so explicitly in its status note. Some rows carry placeholder
metadata from repository bootstrap. Others were confirmed only through a catalogue listing
because the publisher blocks automated retrieval or paywalls the text, and one rests on
secondary summaries.

Treat a row as a research lead until its status says otherwise. No policy proposal may rely on
a row that still carries placeholder metadata, and no accepted design should depend on an
unconfirmed structural detail.

## Keeping this page current

Update this page in the same change that lands the work it describes, not afterwards. If a
change alters what is in flight, what is decided, what is blocking, or what comes next, it
alters this page too. A status page that lags the repository is worse than none, because it
invites a reader to trust it.
