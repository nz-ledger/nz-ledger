# Project Charter

## Mission

Build trusted, open-source, human-and-agent-first financial infrastructure for New Zealand business software.

## Vision

NZ Ledger will be a deterministic, evidence-backed, secure, extensible financial foundation on which developers can build complete business accounting experiences while preserving financial integrity, authority, history, and recoverability.

## Formal scope

NZ Ledger is designed to underpin complete business accounting solutions for the broad range of New Zealand organisations ordinarily served by mainstream business accounting platforms, while remaining extensible for industry-specific operations and emerging forms of finance.

The governing market boundary is capability-based. If a New Zealand organisation can reasonably use a mainstream business accounting platform as its primary financial system, NZ Ledger should be capable of underpinning an equivalent or better human-and-agent-first solution for that organisation. The boundary is not defined by turnover, staff count, transaction volume, legal form, industry, or one founder's business.

## Support modes

- **Native:** the platform implements the capability directly.
- **Module:** a first-party or third-party module implements the specialist workflow through NZ Ledger contracts and controls.
- **Integration:** a specialist external system performs the operational workflow and sends complete, controlled financial events to NZ Ledger.

Support means correct books, evidence, reconciliation, controls, close, reporting, and New Zealand obligations. The ability to post a manual journal by itself is not support.

## Founding principles

1. Correctness over convenience.
2. Simplicity over features.
3. Determinism over automation.
4. Evidence over assertion.
5. Explicit authority over implied trust.
6. Infrastructure over one application.
7. Extension through contracts, not forks.
8. New Zealand first, without undocumented assumptions.
9. Open export and recovery over lock-in.
10. Security and privacy by design.

## Positioning and independence

NZ Ledger is independent open-source infrastructure, not a commercial product belonging to another organisation. It is not a product clone, comparison project, generic international tax engine, blockchain project, custodial wallet, payment processor, or chatbot with accounting attached. It does not claim government, regulatory, tax, accounting, or professional approval. It is not a registered digital service provider and never holds government gateway registrations, credentials, or filing relationships; accountable organisations building on it secure their own. It is not a blockchain project because a legal entity's books have an accountable operator: integrity is delivered through exact values, immutable posted history, a tamper-evident hash chain, native digital signing, and deterministic replay, which provide the verifiability benefits without distributed consensus. Optional external anchoring of chain-head digests remains a recorded module-level revisit trigger, never core.

Requirements are expressed through business capabilities, financial controls, outcomes, New Zealand obligations, architecture, security, and extensibility. Founding repository content does not name, compare, imitate, or position the project against an accounting product or vendor. Connector-specific names may appear only in necessary technical documentation after a connector exists and the naming is reviewed.

## Financial system of record

Specialist systems may operate manufacturing, point of sale, field service, rostering, practice management, warehousing, construction scheduling, farm operations, fleet operations, and other industry workflows. NZ Ledger remains authoritative for financial events, subledger control totals, journals, evidence links, reconciliation, close, reports, policy outputs, filing snapshots, and audit history.

## Non-goals

The founding project does not introduce microservices, full event sourcing for every workflow, a generic international tax engine, a custom tax-rule language, blockchain-backed journals, a second reference language, direct digital-asset custody, payment execution in the kernel, an extension marketplace, speculative packages, or enterprise governance bureaucracy.

No production implementation, tax rule, accounting calculation, database, deployment, package, or hosted service exists in the founding repository.

## Success criteria

Success requires:

- reproducible financial results and immutable posted history;
- evidence-to-output traceability and bitemporal accountability;
- exact values, multicurrency, and financial instruments as foundations;
- effective-dated New Zealand policies based on current primary sources;
- equal controlled command surfaces for humans, agents, and integrations;
- extension without bypassing invariants, authority, isolation, or privacy;
- open export, documented recovery, and language-neutral conformance;
- independent domain, policy, and security review before production use.

## Long-term outcome

The project should enable an ecosystem of interoperable applications, modules, connectors, reports, workflows, and agents that can prove equivalent behaviour through public contracts and conformance tests without weakening the core financial system of record.
