# Threat Model

## 1. System and scope

The target is a future modular financial platform with human and agent experiences, external integrations, identity and control, evidence, subledgers, policy packs, reconciliation, a deterministic kernel, PostgreSQL, immutable evidence storage, reports, exports, and extensions. The current repository contains documentation and GitHub configuration only.

## 2. Assets

Identities, mandates, approvals, evidence, events, journals, values, instruments, rates, policy packs, periods, reconciliations, reports, filing snapshots, extensions, secrets, source, CI configuration, releases, backups, exports, and audit records.

## 3. Actors

Authorised and compromised humans; bounded and compromised agents; integrations; extension publishers; maintainers; reviewers; CI and platform operators; external service providers; curious, criminal, and supply-chain attackers; and insiders with excess or misused privilege.

## 4. Trusted components

Trust is conditional and minimal. The deterministic kernel, authority evaluator, identity provider, persistence controls, evidence integrity service, policy-selection mechanism, release pipeline, and recovery process require explicit assurance. No component is trusted merely because it is internal.

## 5. Attacker-controlled inputs

Documents, images, extracted text, bank and integration events, API payloads, extension manifests and outputs, agent prompts and retrieved content, user-entered data, filenames, URLs, policy proposals, issue content, pull requests, dependencies, workflow inputs, and restore media.

## 6. Trust boundaries

Principal to experience; experience to command gateway; agent to tool; external system to integration; evidence to extraction; subledger to kernel; policy publisher to policy runtime; extension to platform; tenant to tenant; legal entity to legal entity; CI to source; build to release; production to backup and recovery.

## 7. Financial integrity threats

- Duplicate, omitted, unbalanced, backdated, or unauthorised journals.
- Floating-point loss, overflow, scale confusion, rounding manipulation, or residual concealment.
- Mutation or deletion of posted history, evidence, period locks, or correction links.
- Rate-source, timestamp, valuation-purpose, or instrument-identity substitution.
- Control-account drift, reconciliation suppression, or false close completion.
- Effective-time and recorded-time confusion that rewrites known history.

Controls include exact primitives, canonical contracts, idempotency, atomic posting, immutable history, explicit corrections, source-linked valuations, separation of duties, reconciliation, bitemporal snapshots, and conformance vectors.

## 8. Agent threats

Prompt injection, fabricated evidence, excessive mandate, confused delegation, tool misuse, approval spoofing, repeated commands, hidden uncertainty, unsafe external action, sensitive-data disclosure, and retention of private reasoning. Controls include untrusted-input handling, explicit mandates and limits, evidence binding, idempotency, risk approvals, unknown states, tool isolation, outcome reconciliation, revocation, and concise decision records without chain-of-thought.

## 9. Extension threats

Malicious publisher, overbroad permissions, direct storage access, cross-tenant leakage, event exfiltration, policy substitution, dependency compromise, denial of service, incompatible upgrades, persistent data after uninstall, and command replay. Controls include signed identity, reviewed manifests, least privilege, sandboxing, resource limits, version ranges, conformance, audit, revocation, and export or deletion contracts.

## 10. Evidence and file threats

Malware, parser exploitation, content-type confusion, decompression bombs, forged metadata, hash mismatch, overwritten objects, unsafe previews, privacy leakage, and derived-data substitution. Controls include quarantine, size and type limits, safe parsing, immutable versions, hashes, malware scanning, least privilege, original retention, redaction, and provenance.

## 11. Policy-pack threats

Forged sources, stale rules, wrong effective dates, ambiguous interpretation presented as fact, unsigned substitution, downgrade, and retroactive recalculation. Controls include primary sources, fact and interpretation separation, effective dating, review state, golden examples, trusted publishing, explicit selection, and historical version retention.

## 12. Integration threats

Credential compromise, replay, omission, reordering, schema drift, false completion, partial settlement, webhook forgery, rate limiting, and provider outage. Controls include scoped credentials, signatures where available, idempotency, correlation, sequence and checkpoint handling, contract validation, reconciliation, retry policy, and explicit unavailable states.

## 13. Supply-chain threats

Maintainer takeover, weak recovery, malicious pull request, mutable Action reference, dangerous privileged trigger, secret exposure, dependency compromise, tag movement, build tampering, and unverified package publication. Controls include two owners, secure 2FA, least privilege, protected review, pinned Actions, fork isolation, read-only tokens, scanning, protected tags, OIDC, attestations, SBOM, and immutable releases.

## 14. Availability and recovery threats

Deletion, ransomware, regional outage, resource exhaustion, corrupt backup, unavailable evidence store, unreconciled partial action, and recovery that loses temporal or audit semantics. Controls include quotas, isolation, observability, encrypted backups, restore tests, export, transactional outbox, reconciliation, and recovery objectives defined before production.

## 15. Security invariants

No actor bypasses authority; no tenant crosses isolation boundaries; no posted journal is mutated; no financial value uses floating point; no accepted command is silently duplicated; no original evidence is replaced by derived data; no policy version changes historical output silently; no external completion is inferred from intent; no extension accesses ungranted data; and no release is published without reviewed provenance controls.

## 16. Out of scope for the documentation-only phase

Runtime exploit testing, database and API testing, tenant-isolation tests, dependency review of product packages, release artefact verification, recovery exercises, and penetration testing are impossible because no software or runtime exists.

## 17. Known limitations

The threat model is architectural, not evidence of implementation security. New Zealand policy interpretation, production topology, identity provider, data residency, extension sandbox, release mechanism, and operational recovery remain unimplemented and require review.

## 18. Review triggers

Review when public contracts, core semantics, identity, authority, storage, evidence, policy packs, extensions, AI providers, integrations, tenancy, release, deployment, custody, payment execution, regulated scope, or material dependencies change, and after incidents or significant new threat intelligence.
