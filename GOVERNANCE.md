# Governance

NZ Ledger begins with lightweight founder-led governance. The process is public by default, except for vulnerabilities, private conduct reports, personal data, and other material that must be protected.

## Stewardship

Mark Smith ([@nz365guy](https://github.com/nz365guy)) is founder and initial maintainer. Maintainers may triage work, moderate community spaces, review and merge changes, protect scope and security, approve RFCs and ADRs, manage releases, and administer the repository within this charter.

Maintainer authority does not override the licence, immutable project history, security reporting confidentiality, accepted governance, or the requirement for current primary sources and appropriate review.

## Public work records

Use Discussions to explore questions and proposals. Use issues for accepted or reproducible work. Material proposals identify purpose, scope, alternatives, dependencies, security and accounting consequences, compatibility, and acceptance criteria.

## RFCs and ADRs

An RFC is required for changes to public contracts, core domain semantics, security boundaries, extension or agent-authority models, policy-pack structure, compatibility promises, repository strategy, or primary language strategy.

An ADR records an accepted implementation decision with durable architectural consequences. Typographical fixes, minor documentation corrections, semantics-preserving refactoring, routine maintenance, and tests that do not change semantics need neither.

Templates and indexes live in `docs/rfc/` and `docs/adr/`. Maintainers decide when a proposal has enough evidence for acceptance. Dissent and unresolved risks remain recorded.

## Security and policy decisions

Security-sensitive changes follow private reporting where disclosure would increase risk, then use a public decision record when safe. Official New Zealand policy packs require primary sources, effective dates, explicit interpretation, golden examples, applicable domain review, and security review where integrity is affected.

## Releases

Only maintainers explicitly authorised for release may approve a release. Before the first software or package release, the project must define and pass the security, provenance, conformance, domain, compatibility, recovery, and publication gates in the architecture and roadmap.

## Maintainers

Maintainer candidates demonstrate sustained, accurate, respectful contributions; sound judgement; security and privacy care; willingness to preserve neutral naming and scope; and capacity to review or operate the project. Nomination and acceptance are public unless privacy or safety requires a limited record.

Conflicts of interest are disclosed. A conflicted maintainer does not act as the sole reviewer or decision-maker. Disputes seek evidence, project principles, and a written decision. Conduct concerns use the private process in `CODE_OF_CONDUCT.md`.

## Bootstrap review exception and review debt

The founding phase operates under a recorded bootstrap exception: some reviews that this
governance normally requires were deferred because the project began with one maintainer.
Deferred reviews are debt, not waivers. Each deferred review is tracked in a public issue
labelled `independent-review-required`, in priority order.

Current deferred reviews, highest priority first:

1. Independent accounting-domain review of RFC 0001 (canonical accounting domain).
2. Independent review of the exact-value contract when accepted (issue #5).
3. Independent review of canonical serialisation and schemas when accepted (issue #6).

Expiry trigger: the bootstrap exception ends before Phase 2 implementation work is accepted.
No kernel implementation issue may be accepted, and no release gate may pass, until RFC 0001
has received independent human accounting-domain review by a reviewer who is not the author.
AI-assisted review may inform, but does not satisfy, an independent human review requirement.

## Succession and ownership continuity

Founding decision FD-002 requires a trusted backup owner for the `nz-ledger` GitHub
organisation. The backup owner holds organisation owner rights with secure two-factor
authentication and documented credential recovery, so that repository administration,
security response, and release control survive the loss or unavailability of any single
person. The appointment of the backup owner, and any change to it, is recorded publicly.
If the founding maintainer becomes unavailable, the backup owner assumes interim
stewardship under this governance document until maintainers are appointed.

## Evolution

Governance may evolve through an accepted RFC when contributor scale, risk, or stewardship requires it. At bootstrap there is no foundation, board, token, decentralised autonomous organisation, or formal voting system.
