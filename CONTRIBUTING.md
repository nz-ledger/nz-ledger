# Contributing

Thank you for helping build open financial infrastructure for New Zealand.

## Start with a work record

For a question or early design, start a Discussion. For a reproducible defect or bounded accepted task, use an issue form. Before substantial work, comment on the issue and wait for a maintainer to confirm scope and ownership. This reduces duplicate effort and avoids implementing unresolved accounting, policy, security, or contract decisions.

Good first issues are bounded and name a maintainer reviewer. They do not ask a new contributor to decide a statutory or core accounting question alone.

## Scope and public content

- Do not start product implementation without an accepted issue.
- Use neutral business-capability language. Do not name, compare, imitate, or position NZ Ledger against an accounting product or vendor.
- Do not claim legal, tax, accounting, government, regulatory, professional, security, or production approval.
- Do not include confidential financial data, personal data, credentials, recovery material, or secrets.
- New Zealand policy work requires current primary official sources, effective dates, explicit interpretation, and review status.
- Keep the deterministic kernel, immutable history, evidence, exact values, and authority boundaries intact.

Report suspected vulnerabilities privately as described in `SECURITY.md`. Do not open a public issue for a vulnerability.

## Proposals and decisions

Use an RFC when changing public contracts, core semantics, security boundaries, extension or agent-authority models, policy-pack structure, compatibility, repository strategy, or primary language strategy. Use an ADR after accepting an implementation decision with durable architectural consequences. Small corrections and semantics-preserving maintenance need neither.

## Pull requests

A pull request should:

- link an accepted issue;
- solve one coherent problem with a clear scope;
- explain design, alternatives where material, security, accounting and policy effects, public-contract effects, and compatibility;
- update documentation and decision records;
- add tests once implementation exists;
- avoid unrelated formatting or dependency changes;
- pass repository checks and review;
- contain no private data or secrets;
- use NZ English and avoid em dashes;
- use DCO sign-off on every commit.

Sign off commits with `git commit -s`. The sign-off certifies the [Developer Certificate of Origin 1.1](https://developercertificate.org/). Do not invent another person's identity or email address.

By contributing, you agree that your contribution is licensed under Apache License 2.0 and that you have the right to submit it.

## Generated work

Contributors remain responsible for every submitted line, source, test, licence, security property, and accounting result. Generated code or prose must be reviewed, understood, and validated by the contributor.

State material use of generation tools when it helps reviewers assess provenance, licences, tests, or risk. Do not submit private prompts, confidential context, or hidden model reasoning.

## Review

Maintainers review correctness, scope, evidence, security, accounting and policy consequences, contracts, tests, documentation, and licence provenance. Review may request domain, policy, or security specialists. Acceptance is not guaranteed by effort, prior discussion, or passing automation.
