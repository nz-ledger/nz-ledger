# Security Architecture

## Objective and assets

Security preserves the confidentiality, integrity, availability, authenticity, accountability, and recoverability of financial records and controls. Assets include identities, mandates, evidence, business events, journals, periods, policies, valuations, reports, filing snapshots, secrets, extension permissions, audit records, backups, source, workflows, releases, and conformance results.

## Trust boundaries

Trust boundaries exist between users and experiences, agents and model providers, integrations and external systems, the command gateway and financial domains, extensions and platform services, tenants and legal entities, policy publishers and runtime selection, evidence storage and derived data, CI and releases, and production and recovery environments.

Crossing a boundary requires authenticated identity, explicit authorisation, validated contracts, data classification, least privilege, integrity checks, audit, and fail-safe behaviour.

## Identity and authorisation

Every actor is a principal. Authentication is not financial authority. Commands require an applicable mandate, legal-entity scope, limits, evidence, current policy, and approvals. Delegation only narrows authority. Revocation and expiry take effect predictably. High-risk external actions remain separate from the accounting kernel.

## Agent mandates

Agents have no implicit privileges. Tool exposure and confidence do not expand a mandate. Inputs, retrieved content, and evidence are untrusted until classified and validated. Unknown or conflicting information escalates. Decision records retain evidence and concise reasons, not private chain-of-thought.

## Extensions

Extension permissions are deny-by-default and separate reads, commands, events, evidence, network access, and administration. Extensions cannot write core storage, execute inside the kernel, bypass invariants or authority, silently substitute policies, or obtain cross-tenant data. Manifests, publisher identity, signatures, version compatibility, isolation, resource limits, and revocation are required before executable extensions.

## Tenant and legal-entity isolation

Tenant and legal-entity context is carried and enforced at every command, query, event, evidence, job, cache, export, and audit boundary. Database controls supplement, rather than replace, application authorisation. Tests must cover identifier substitution, background processing, exports, failure paths, and privileged operations.

## Evidence and policy integrity

Original evidence is immutable and versioned with cryptographic hashes, provenance, classification, retention, and access history. Derived extraction links to its source and version. Policy packs use trusted publisher identity, source references, effective dates, signatures or equivalent integrity protection, review state, and explicit selection. Historical outputs retain the selected policy version.

## Financial invariants

Security-relevant invariants include exact values, balanced journals in functional currency, immutable posted history, controlled reversals and adjustments, period locks, attributable rates and rounding, idempotent commands, bitemporal history, and reconciliation of accepted intent to actual effect. Failures stop or quarantine the affected action without inventing a successful state.

## Supply chain and release provenance

Repository access uses owner continuity, secure 2FA, least privilege, protected branches and tags, reviewed changes, pinned Actions, read-only default tokens, secret scanning, dependency monitoring, and public security reporting. No self-hosted runner, repository secret, package publishing, release, or deployment exists at bootstrap.

Before a software release, the project requires reviewed tests, dependency review, CodeQL, conformance, a GitHub-hosted build, OIDC trusted publishing, no long-lived publishing token, provenance and artefact attestations, an SBOM, immutable release, protected tag, build-once publication, verification instructions, and rollback and advisory procedures.

## Privacy and breach response

Data collection is purpose-bound and minimised. Classification, regional and retention requirements, access, disclosure, deletion where lawful, export, logging, and AI processing are explicit. A response plan must support containment, evidence preservation, impact assessment, required notification, correction, credential or key rotation, and tested recovery.

## Recovery

Backups are encrypted, isolated, monitored, and restored in tests. Recovery must preserve journal history, evidence links, policy versions, recorded and effective time, identifiers, and audit trails. Export provides an independent path out of the platform.

## Fail-safe behaviour

When financial integrity, authority, policy, evidence, or valuation is uncertain, the platform fails closed, preserves the evidence, creates an exception, and does not invent a value, auto-post, file, pay, or mutate history.

## Deferred controls and gates

The documentation-only phase cannot verify application CodeQL coverage, package dependency review, tenant isolation, database security, API authentication, conformance, recovery, penetration testing, domain validation, SBOM, or release provenance. These are mandatory implementation or release gates and remain tracked in the roadmap and backlog.
