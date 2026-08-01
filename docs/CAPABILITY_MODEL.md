# Capability Model

This living matrix records the intended coverage boundary without making a market-share or coverage-percentage claim.

## Boundary and support paths

If a New Zealand organisation can reasonably use a mainstream business accounting platform as its primary financial system, NZ Ledger should be capable of underpinning an equivalent or better human-and-agent-first solution for that organisation.

The boundary includes sole operators, partnerships, companies, supported trusts and not-for-profits, services, construction and trades, retail and hospitality, agriculture, property, e-commerce and importing, transport, manufacturing with integrated specialist operations, multi-location organisations, practical multi-entity groups, multicurrency organisations, and organisations using multiple financial instruments.

Delivery paths are **Native**, **Module**, or **Integration**. A capability is supported only when correct books, evidence, reconciliation, controls, close, reporting, and New Zealand obligations are preserved. A manual journal alone is insufficient.

## Delivery sequencing: Profile 1

The first delivery target is **Profile 1: a single-entity New Zealand services company**.
Its intended shape is one tenant, one legal entity, functional-currency NZD, general
ledger with exact values, evidence-backed sales invoicing and purchase records, bank
statement import and reconciliation, GST-ready outputs, and the foundational reports
(Trial Balance, General Ledger, Journal Register, Profit and Loss, Balance Sheet).

Profile 1 is a sequencing decision, not a boundary change. The charter's capability-based
market boundary is unchanged and, as the charter states, is not defined by any one
founder's business; Profile 1 simply orders which capabilities are designed, implemented,
and validated first so that a complete, conformant vertical slice exists before breadth.
Capability rows outside Profile 1 remain governed by their own evidence requirements.

## Status vocabulary

Each capability uses exactly one of: `Not assessed`, `Research required`, `Design required`, `Native planned`, `Module planned`, `Integration planned`, `Implemented`, `Validated`, or `Unsupported`.

At founding, all capabilities remain `Not assessed` or `Research required`. Planned, implemented, and validated statuses require accepted evidence in an issue or decision record.

## Coverage matrix

| Group | Capability | Status | Intended path | Evidence required to advance |
| --- | --- | --- | --- | --- |
| Universal accounting | Entity structures and policy packs | Research required | To assess | Supported structures, source-backed obligations, and boundaries |
| Universal accounting | General ledger, books, accounts, journals, and dimensions | Not assessed | To assess | Accepted domain model and invariants |
| Universal accounting | Exact values and allocation residuals | Not assessed | To assess | Accepted exact-value contract and vectors |
| Universal accounting | Quotes, invoices, bills, and credit notes | Not assessed | To assess | Workflow, evidence, posting, and correction design |
| Universal accounting | Accounts receivable | Not assessed | To assess | Control-account and reconciliation design |
| Universal accounting | Accounts payable | Not assessed | To assess | Control-account and reconciliation design |
| Universal accounting | Banking, cards, and settlement | Research required | To assess | Source, matching, settlement, and reconciliation research |
| Universal accounting | Expenses and source documents | Not assessed | To assess | Evidence, approval, and posting design |
| Universal accounting | GST | Research required | To assess | Current primary sources, reviewed interpretations, and golden examples |
| Universal accounting | Tax reconciliation | Research required | To assess | Source-backed scope and domain review |
| Universal accounting | Period close | Not assessed | To assess | Lock, exception, reopening, sign-off, and workpaper design |
| Universal accounting | Financial reporting | Research required | To assess | Report contracts and applicable source-backed requirements |
| Common business | Payroll accounting and payroll integrations | Research required | To assess | Boundary, source events, control totals, privacy, and reconciliation |
| Common business | Fixed assets | Not assessed | To assess | Register, movements, policies, and reconciliation design |
| Common business | Inventory accounting | Not assessed | To assess | Quantity, valuation, costing, and control-account design |
| Common business | Projects and job costing | Not assessed | To assess | Dimension, allocation, evidence, and close requirements |
| Common business | Cost centres, branches, locations, and reporting dimensions | Not assessed | To assess | Dimension semantics and access-control design |
| Common business | Cashflow, budgeting, and forecasting | Not assessed | To assess | Actual-versus-plan contracts and authority boundary |
| Common business | Multicurrency | Not assessed | To assess | Recognition, settlement, revaluation, and vectors |
| Common business | Multi-entity administration | Not assessed | To assess | Isolation, consolidation boundary, and authority design |
| Industry modules | Construction, trades, agriculture, property, manufacturing, transport, retail, hospitality, and services | Research required | To assess | Neutral business-activity mapping by segment |
| External operations | Point of sale, field service, rostering, practice management, warehouses, scheduling, farm, fleet, and specialist production | Research required | To assess | Event completeness, evidence, controls, and reconciliation |
| Emerging finance | Multiple financial instruments | Not assessed | To assess | Instrument registry, quantity, valuation, and policy design |
| Emerging finance | Digital asset and stable-value instrument accounting | Research required | To assess | Accounting, tax, regulatory, custody, and valuation sources |
| Close and assurance | Evidence and audit traceability | Not assessed | To assess | Evidence-to-output model and retention design |
| Close and assurance | Reconciliation and exception management | Not assessed | To assess | Control definitions and close lifecycle |
| Close and assurance | Filing and approval snapshots | Not assessed | To assess | Bitemporal and historical-reproduction design |
| Statutory boundary | Government gateway filing (submission) | Unsupported | Integration | Permanently out of project scope: accountable organisations deploying the platform hold their own gateway registrations, credentials, and filing relationships. The platform produces filing-ready outputs and snapshots only. |
| Security and authority | Agent and user authority | Not assessed | To assess | Principal, mandate, approval, and revocation model |
| Security and authority | Tenant and legal-entity isolation | Not assessed | To assess | Threat model, enforcement points, and tests |
| Extensibility | Extension and integration support | Not assessed | To assess | Manifest, permissions, isolation, and conformance design |
| Recovery | Data export and recovery | Not assessed | To assess | Open format, backup, restore, and portability design |
| Statutory boundary | New Zealand statutory and regulatory boundaries | Research required | To assess | Current primary sources and reviewed scope statements |

## Updating the matrix

Every change must link to evidence, state the delivery path, identify accounting and security consequences, and avoid named product comparisons. `Implemented` means code and tests exist. `Validated` additionally requires the defined conformance, domain, policy, security, and operational evidence for that capability.
