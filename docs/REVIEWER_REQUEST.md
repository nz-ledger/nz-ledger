# Request for an independent accounting-domain reviewer

**Status:** Open. This request stands until a reviewer records their findings.

NZ Ledger is an independent Apache 2.0 open-source project building a deterministic
accounting kernel for New Zealand business software. It is in a documentation and design
phase, at a deliberately unhurried pace, and no software has been released.

The project has reached a point where it cannot honestly proceed without a second pair of
eyes. This page describes exactly what is being asked, so that a suitably qualified person can
judge the request before committing to anything.

## Why this is being asked

[RFC 0001](rfc/0001-canonical-accounting-domain.md) defines the project's canonical accounting
vocabulary and its invariant catalogue. Everything downstream depends on it: exact values,
public contracts, conformance, persistence, policy packs, and eventually implementation.

It was accepted under the sole-maintainer bootstrap exception recorded in
[`GOVERNANCE.md`](../GOVERNANCE.md). Its accounting-domain review was performed by the author
reviewing his own work, which is disclosed plainly in the RFC itself and is not independent
review. AI-assisted review informed the drafting, and it does not satisfy this requirement
either.

The project has therefore committed publicly that **no Phase 2 implementation work will be
accepted, and no release gate will pass, until RFC 0001 receives independent human
accounting-domain review by someone who was not involved in drafting or accepting it.** That
commitment is the reason this request exists. It is currently the single thing blocking the
project, ahead of any design or code work.

## What you would be asked to do

Read RFC 0001 and record your assessment of one question: are the vocabulary and invariants
suitable and durable across the range of entities a New Zealand accounting system must serve?

The RFC names the contexts it must hold across: for-profit entities, public-benefit entities,
trusts, not-for-profits, and entities using minimum financial reporting requirements. It is
policy-neutral by design, so the question is whether the underlying mechanics survive
different reporting frameworks, not whether any particular framework is implemented correctly.

Your review would record:

- the scope of what you reviewed;
- your qualifications or relevant experience, in whatever detail you are comfortable with;
- your findings;
- any changes you consider necessary;
- any judgements you could not resolve; and
- whether, in your view, the RFC remains suitable as a foundation.

Disagreement is a useful outcome. Findings that reopen decisions are handled through the
governance process, and dissent stays on the public record rather than being quietly resolved.

There is also a specific open question the RFC could not settle without domain input, recorded
as `DOM-Q013`: whether a minimum of two lines should be universally required for a valid
posted journal, once zero-value and multi-instrument cases exist. Both sides of the argument
are set out in [RFC 0003](rfc/0003-exact-values-instruments-valuations.md).

## What is out of scope

- Security review, which is tracked separately.
- Implementation, code, database design, or user interface.
- Any New Zealand policy rule. Tax and reporting rules live in versioned policy packs above
  the kernel and are deliberately not in RFC 0001.
- Correcting the writing. Substance is what is wanted.

## Pace

RFC 0001 is a single, bounded document. Take whatever time the review needs: a slower, more
sceptical read is more valuable than a fast one, and no estimate is offered because only the
reviewer can judge it.

There is no deadline. This project runs at a hobby pace and will wait.

## What this is not

This matters more than the rest of the page, because it is usually what makes a qualified
person hesitate.

- It is **not an endorsement**. Reviewing the RFC does not put your name behind the project,
  its future software, or any output it produces.
- It is **not a professional opinion, an audit, an assurance engagement, or advice**, and it
  must not be represented as any of those by the project or by anyone else.
- It carries **no liability and no ongoing obligation**. You review once and record what you
  found. You are free to conclude that the RFC is unsuitable.
- It confers **no approval of any kind**. The project claims no legal, tax, accounting,
  government, regulatory, professional, security, or production approval, and a completed
  review does not change that.
- It is **unpaid**. This is a volunteer open-source contribution and the project has no
  funding.

You would be credited by name in the RFC's review record, because the review has to be
attributable to mean anything. If you would prefer a different form of attribution, say so and
it will be discussed before anything is published.

## Who is suitable

Someone with genuine New Zealand accounting or tax domain experience who was not involved in
drafting or accepting RFC 0001. Chartered accountants, tax specialists, financial controllers,
and experienced practitioners across the entity types listed above are all relevant.

Conflicts of interest are disclosed under [`GOVERNANCE.md`](../GOVERNANCE.md). A conflict does
not necessarily disqualify anyone, but it must be on the record.

Deep software knowledge is not required. RFC 0001 is written to be read by an accountant, and
if any part of it cannot be, that is itself a finding worth recording.

## How to volunteer

Comment on **[issue #36](https://github.com/nz-ledger/nz-ledger/issues/36)**. That issue is
the single thread for this request, so that anyone arriving later can see the whole
conversation rather than pieces of it.

If you would rather make first contact privately, use the contact route described in
[`CODE_OF_CONDUCT.md`](../CODE_OF_CONDUCT.md) to ask for a private channel, and the public
record will pick up from there once you are comfortable.

## Reviews needed after this one

Listed so you can see the shape of the commitment. None of these is part of the request above,
and volunteering for one does not commit you to any other.

| Priority | Review | Issue |
| --- | --- | --- |
| 1 | Independent accounting-domain review of RFC 0001 | [#36](https://github.com/nz-ledger/nz-ledger/issues/36) |
| 2 | Independent review of the exact-value contract, RFC 0003, when accepted | [#37](https://github.com/nz-ledger/nz-ledger/issues/37) |
| 3 | Independent review of canonical serialisation and schemas when accepted | [#38](https://github.com/nz-ledger/nz-ledger/issues/38) |
| 4 | Independent domain and security review of draft RFC 0002 | [#39](https://github.com/nz-ledger/nz-ledger/issues/39) |
