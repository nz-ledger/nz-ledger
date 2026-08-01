# Coding Agent Instructions

These instructions apply to every coding or writing agent working in this repository.

1. Read `docs/PROJECT_STATUS.md` first to learn what is in flight, what is decided as against merely proposed, and what is blocked. Then read `docs/PROJECT_CHARTER.md`, `docs/ARCHITECTURE.md`, `docs/DECISIONS.md`, `SECURITY.md`, and `docs/THREAT_MODEL.md` before changing the repository.
2. Do not start product implementation without an accepted issue.
3. Do not introduce named third-party accounting product or vendor comparisons.
4. Do not claim legal, tax, accounting, government, regulatory, professional, security, or production approval.
5. Do not implement changing New Zealand rules without current primary official sources, explicit effective dates, and required review.
6. Never use floating point for financial values. Public financial values are decimal strings.
7. Do not allow direct writes around the controlled command layer.
8. Do not allow agents, users, integrations, or extensions to bypass identity, mandate, limits, evidence, approvals, or audit.
9. Do not add speculative dependencies, packages, directories, infrastructure, or services.
10. Keep the accounting kernel deterministic and free of UI, HTTP, database, cloud, identity-provider, AI, telemetry, system-time, random-ID, and external-rate dependencies.
11. Preserve original evidence, provenance, bitemporal accountability, and immutable posted history. Correct through linked reversal or adjustment.
12. Use NZ English in prose and avoid em dashes.
13. Never add private data, real financial records, credentials, tokens, recovery material, or secrets.
14. Stop and request a maintainer decision when a change conflicts with a locked decision.
15. Put a material decision to the maintainer as explicit options with a recommendation and the trade-off of each, rather than choosing silently. One decision at a time, and wait for the answer before acting on it.
16. Research current sources before proposing a design that depends on them, record the month and year the research was done, and state plainly where a source could not be verified rather than implying it was.
17. Update `docs/PROJECT_STATUS.md` in the same change that alters what is in flight, what is decided, what is blocked, or what comes next. A status page that lags the repository invites a reader to trust it.
18. Draft status is not a soft form of accepted. Do not cite a draft RFC as settled design, and record the dependency when work builds on one.

Follow `CONTRIBUTING.md`, use the applicable issue, RFC, and ADR process, keep changes narrow, and verify claims against authoritative evidence.
