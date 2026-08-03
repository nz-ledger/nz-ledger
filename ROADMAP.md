# Roadmap

The roadmap is outcome-based and has no promised dates. The community may work on accepted items in parallel where dependencies and review capacity allow.

## Phase 0: Founding repository

- Establish the organisation and ownership continuity.
- Publish the documentation-only monorepo and organisation profile.
- Adopt governance, contribution, security, RFC, and ADR processes.
- Configure repository and supply-chain security baselines.
- Record the architecture, capability boundary, source register, and public backlog.

## Phase 1: Foundational contracts and conformance

Critical path: the exact-value contract blocks nearly every later phase and is worked first.

- Define the canonical domain model and invariants.
- Define exact values, currencies, and instruments (draft RFC 0003).
- Define commands, queries, events, and evidence.
- Define canonical serialisation and language-neutral schemas.
- Design golden vectors and the black-box conformance approach.
- Define the canonical error contract and the asynchronous operation contract shared by every access surface.
- Define signature, digest, signer-identity, and verification contracts (FD-018) and the tamper-evident book hash chain (FD-019) alongside canonical serialisation.

## Phase 2: Deterministic accounting kernel

- Define and implement accounts, books, journals, and periods.
- Implement immutable posting, reversals, adjustments, and locks.
- Implement multicurrency measurement and revaluation.
- Define report calculation contracts and core financial invariants.

## Phase 3: Evidence and persistence

- Implement PostgreSQL atomicity, idempotency, concurrency, and isolation.
- Preserve effective and recorded time.
- Implement immutable evidence storage and provenance.
- Implement export, backup, restore, and the transactional outbox.

## Phase 4: New Zealand policy and reporting

- Implement the effective-dated policy-pack framework.
- Add reviewed GST, reporting, tax-reconciliation, and fixed-asset policy packs.
- Produce filing-ready outputs, snapshots, and mappings. The project itself never registers with, files to, or holds credentials for any government gateway; accountable organisations deploying or hosting the platform secure and operate their own filing relationships.
- Maintain primary-source links and reviewed golden examples.

## Phase 5: Human and agent controls

- Implement principals, delegation chains with attenuation-only sub-delegation, mandates, approvals, separation-of-duties constraints, and cascading revocation.
- Implement exception handling, close controls, decision records, and audit.

## Phase 6: Extension platform

- Define SDK contracts, manifests, permissions, webhooks, and sandboxing.
- Publish extension conformance requirements and a reviewed reference connector.
- Deliver the reference access surfaces: HTTP API, CLI, and MCP server as conformance-tested peer adapters over the command gateway (FD-021).

## Phase 7: Common business modules and close

- Deliver receivables, payables, banking, fixed assets, inventory accounting, projects and job costing, payroll integration, close, and workpapers through accepted support paths.

## Phase 8: Emerging finance

- Add digital-instrument recording, wallet and custodian reconciliation, stable-value instrument accounting, restricted payment orchestration, and treasury controls within reviewed legal and security boundaries.

## Phase 9: Production readiness

- Deliver auditor-facing assurance capabilities: chain verification, re-performance from export, and auditor principal mandates (RFC 0002 when accepted).

- Complete independent domain and security review.
- Verify recovery and migration.
- Establish release provenance, compatibility policy, limitations, and version 1.0 gates.

Passing an earlier phase does not waive a later security, domain, policy, or operational gate.
