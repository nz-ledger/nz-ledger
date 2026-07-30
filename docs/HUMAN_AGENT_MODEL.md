# Human and Agent Operating Model

Humans, software agents, integrations, and scheduled rules are first-class principals operating through one controlled command surface. Actor type changes interaction and risk, not the financial invariants.

## Principals, delegation, and mandates

Each principal has a stable identity, actor type, authenticating authority, and audit history. Delegation records the delegator and may only narrow existing authority.

A mandate defines permitted capabilities, legal entities, books, accounts, counterparties, instruments, monetary limits, time limits, evidence requirements, approvals, purpose, expiry, and revocation. Mandates are explicit and inspectable. A model's confidence, an integration token, or a scheduled trigger does not grant authority.

## Autonomy levels

### Level 1: Draft only

The principal may collect evidence, classify, propose coding, prepare a command, and explain uncertainty. A human with authority reviews and submits the command.

### Level 2: Bounded autonomous posting

The principal may submit defined, reversible financial commands within a current mandate, limits, evidence requirements, risk rules, and monitoring. Exceptions, ambiguity, limit breaches, new counterparties, unusual instruments, and policy uncertainty escalate.

### Level 3: Externally consequential actions

Filing, payment, transfer, exchange, custody, contractual acceptance, and other externally consequential actions require a separate highly permissioned capability and risk-based approval. The accounting kernel does not execute payments or hold private keys.

## Action lifecycle

```text
Observe evidence
  -> retain original and provenance
  -> classify with explicit unknowns
  -> propose intent
  -> evaluate identity, mandate, limits, policy, risk, and evidence
  -> obtain required approvals
  -> execute through the controlled command surface
  -> record outcome and decision record
  -> reconcile expected and actual effects
  -> escalate exceptions
```

Acceptance, posting, and external completion are distinct states. Idempotency and correlation prevent duplicated intent from silently creating duplicated financial effects.

## Risk-based approvals

Approval policy considers value, counterparty, instrument, irreversibility, novelty, data sensitivity, policy certainty, separation of duties, and external consequence. Approval is attributable, scoped, time-bounded, and recorded. The approving principal must possess authority independent of the requesting principal where separation is required.

## Human exception experience

Exceptions present the evidence, proposed action, reason for escalation, unknowns, policy version, financial effect, alternatives, and authority required. The system does not pressure a reviewer to accept a recommendation or hide uncertainty behind a confidence score.

## Decision records

Material decisions retain principal, delegator, mandate, model and tool version where applicable, inputs and evidence references, structured facts extracted, recorded confidence, effective and recorded time, policy and rules evaluated, proposed and approved action, command, limits evaluated, approvers, result, journal relationship, correlation and idempotency identifiers, and correction links. Confidence is retained as evidence about the proposal, never as authority. Records retain concise reasons and evidence, not hidden chain-of-thought or private model reasoning.

## Unknown is valid

Unknown, disputed, incomplete, or awaiting evidence are valid states. They trigger a controlled exception or provisional workflow rather than invented facts, silent defaults, or unauthorised posting.

## Override, reversal, and audit

Authorised humans can deny, amend a draft, revoke a mandate, suspend a principal, or issue corrective commands. Posted history is never mutated. Corrections use reversing or adjusting journals and preserve evidence, decision records, policy versions, and linkage to the original action.

Payment execution, exchange, transfer, and custody remain separate from accounting recording and require independent legal and security review before any future implementation.
