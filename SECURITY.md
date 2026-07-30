# Security Policy

## 1. Supported versions

NZ Ledger is in a documentation-only founding phase. No production software or supported runtime version exists. Security issues in repository configuration, workflows, documentation, governance, or the future architecture are still welcome.

## 2. System and scope

The intended system is a deterministic financial platform for New Zealand business software. The current scope is this public repository and its GitHub configuration. See `docs/SECURITY_ARCHITECTURE.md` and `docs/THREAT_MODEL.md`.

## 3. Threat model summary

Security includes financial integrity, tenant and legal-entity isolation, agent authority, evidence and policy integrity, valuation, privacy, supply chain, release provenance, availability, and recovery. External inputs, extensions, agents, integrations, and pull requests are untrusted until validated and authorised.

## 4. Security invariants

- No principal bypasses explicit authority.
- No tenant or legal entity crosses an isolation boundary.
- No posted journal or original evidence is silently mutated.
- No financial value uses floating point.
- No command is silently duplicated and no external completion is inferred from intent.
- No policy change silently rewrites historical output.
- No extension accesses ungranted data or core storage.
- No release is published without reviewed provenance gates.

## 5. Reportable findings

Report vulnerabilities or credible weaknesses in repository permissions, Actions, dependency or release supply chain, identity and authority design, tenant isolation, evidence or policy integrity, financial invariants, exact-value handling, temporal history, extensions, integrations, privacy, recovery, or future implementation.

## 6. Financial-integrity examples

Examples include unauthorised posting, history mutation, duplicate or omitted effects, balance or rounding bypass, rate or instrument substitution, period-lock bypass, false reconciliation, evidence replacement, policy downgrade, cross-entity access, mandate escalation, and incorrect recovery of financial history.

## 7. Private reporting

Use GitHub private vulnerability reporting in this repository. Select **Security**, then **Report a vulnerability**. Do not create a public issue, Discussion, pull request, or social post containing vulnerability details, secrets, personal data, or real financial records.

If private vulnerability reporting is unavailable, use the public contact methods on [the initial maintainer's GitHub profile](https://github.com/nz365guy) only to request a private reporting channel. Do not include vulnerability details in that public request.

## 8. Coordinated disclosure

Please allow maintainers time to confirm scope, assess impact, prepare corrections and advisories, and coordinate affected parties. Do not access data that is not yours, degrade a service, persist access, or test against real financial information.

## 9. Response process

Maintainers will acknowledge a usable report, protect reporter and affected-party information, triage severity and scope, preserve evidence, coordinate remediation and disclosure, credit the reporter if requested and safe, and document lessons and control changes. Timing depends on evidence, impact, and project capacity; no response-time guarantee is made at bootstrap.

## 10. Out of scope

General support, feature requests, unverified scanner output without a plausible impact path, social engineering, denial-of-service testing, testing on third-party systems, and legal or accounting advice requests are out of scope for the private vulnerability channel.

## 11. Known limitations

No application, package, database, deployment, release, hosted service, or production environment exists. Runtime controls, CodeQL application coverage, dependency review, tenant-isolation tests, conformance, release attestations, recovery tests, and penetration testing are deferred until their prerequisites exist.
