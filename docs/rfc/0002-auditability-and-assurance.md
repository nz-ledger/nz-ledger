# RFC 0002: Auditability and assurance model

- **Status:** Draft
- **Author:** `@nz365guy` (drafted with AI assistance, Claude, August 2026)
- **Reviewers:** None yet. Requires independent human accounting-domain and security review before acceptance.
- **Created:** 2026-08-01
- **Related issues:** To be opened; depends on issues #5 (draft RFC 0003), #6, #8, #12 and founding decisions FD-018 and FD-019.

## Problem and outcome

NZ Ledger's foundations already provide most raw audit material: immutable posted
history, bitemporal records, evidence hashing with provenance, versioned policy,
decision records, and deterministic replay. What is not yet designed is the assurance
layer that lets an external auditor, human or AI, verify those properties cheaply and
independently rather than trusting the operator. The intended outcome is that an
auditor with an authorised export and the public contracts can mechanically verify
history integrity, re-perform the accounting, and map records to audit assertions
without access to internal systems.

## Scope and non-goals

In scope: tamper-evident history verification, re-performance as a designed audit use
case, an auditor principal class, and assertion-aligned export design. Non-goals: this
RFC does not claim audit, assurance, or regulatory approval; does not design a specific
audit product; and does not alter kernel invariants accepted in RFC 0001.

## Proposal

### 1. Tamper-evident history (FD-019)

Each book's accounting sequence forms an append-only hash chain. Every posted journal's
canonical digest (issue #6 bytes) incorporates the digest of the previous entry in the
book sequence. An export therefore permits full-chain verification: any mutation,
deletion, or reordering of posted history is detectable from the data alone. Chain-head
digests are signable under FD-018. Verification is a conformance capability with
published vectors.

### 2. Re-performance as a designed capability

The complete export (RFC 0001 `DOM-S010`) plus the deterministic kernel and conformance
runner constitute a re-performance engine: an auditor replays the accepted inputs and
compares canonical outputs, balances, and reports byte-for-byte. Export completeness is
specified against this bar: everything required to reproduce the domain state must be
present, including nondeterministic inputs that were supplied explicitly.

### 3. Auditor principal class

Auditors are principals under the existing mandate model with a named, read-only
profile: scope-limited, time-boxed, evidence-access-controlled, and fully audited
themselves. Continuous assurance is supported by subscribing an auditor principal to
the event outbox under the same controls. No auditor mandate confers command authority.

### 4. Assertion-aligned export

Export and workpaper contracts document their mapping to standard audit assertions
(existence, completeness, accuracy, cut-off, classification, valuation, rights and
obligations, presentation). Alignment with the OECD Standard Audit File for Tax
(SAF-T) structure is a research task: New Zealand does not mandate SAF-T, but
structural alignment would make exports consumable by existing audit tooling and
future AI audit agents. Research findings will be recorded in the source register
before any mapping is implemented.

## Alternatives

Do nothing: auditors assess controls and trust the operator, as with conventional
platforms. Rejected as the default because cryptographic verifiability is cheap at
design time and expensive to retrofit. Blockchain-based verification: rejected under
FD-020; the optional external-anchoring module remains a recorded revisit trigger.

## Consequences

Adds chain construction to the posting path (performance cost bounded and measurable);
couples chain design to issue #6; adds an export-completeness obligation; strengthens
the security posture by making integrity claims falsifiable.

## Validation

Golden vectors for chain construction and verification, tamper-detection negative
vectors, re-performance equivalence tests, auditor-mandate authorisation tests, and
export-completeness round-trip tests.

## Open questions

Chain granularity (per book confirmed; whether ledgers also carry a summary chain);
digest algorithm agility; anchoring module boundary; SAF-T applicability findings for
New Zealand entities.

## Decision

Not yet decided. This RFC is a draft and must not be treated as accepted design.
