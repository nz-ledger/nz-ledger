# Architecture

## Direction

NZ Ledger will begin as a modular monolith around a deterministic, headless accounting kernel. Stable public contracts separate layers while one deployable reference implementation keeps transactions, testing, and operational reasoning simple.

```text
Human experiences       Agent experiences       External systems
       |                        |                       |
       +------------------------+-----------------------+
                                |
                    Identity and control plane
                                |
                    Command and intent gateway
                                |
                    Evidence and business events
                                |
          +---------------------+----------------------+
          |                     |                      |
      Subledgers          NZ policy packs       Reconciliation
          |                     |               and close engine
          +---------------------+----------------------+
                                |
                 Deterministic accounting kernel
                                |
           PostgreSQL journal store and evidence storage
                                |
          Reports, workpapers, returns, exports, integrations
```

## Identity, commands, and authority

Humans, agents, integrations, and scheduled rules are principals. They submit commands through the same gateway. Identity, delegator, mandate, permissions, monetary and counterparty limits, instrument limits, time limits, evidence requirements, approvals, and expiry are evaluated before a command reaches a financial boundary. Model confidence is never authority.

## Evidence and business events

Original input is recorded before financial compilation. Sources may include invoices, receipts, bank transactions, payments, payroll results, contracts, approvals, custodian records, external events, and declarations. Records retain source system and identifier, legal entity, counterparty, received time, effective dates, tax point, settlement date, original payload or document, cryptographic hash, extraction version, classification history, correlation identifier, and idempotency key.

AI extraction is derived data and never replaces original evidence.

## Deterministic accounting kernel

The kernel owns legal entities, ledgers, books, the chart of accounts and account classifications, journals and lines, exact values, instruments, periods and locks, dimensions, reversals, adjustments, valuations, audit-domain events, and report-calculation contracts. Its foundational reports include Trial Balance, General Ledger, Journal Register, Profit and Loss, and Balance Sheet.

It has no dependency on user interfaces, HTTP, database implementations, authentication providers, cloud services, AI models, telemetry, external rate services, system time, or random identifier generation. Nondeterministic values are supplied explicitly.

A posted journal is immutable and remains posted. Corrections use separate linked reversing or adjusting journals. Reversal status is derived from the linked entries.

## Exact values, currencies, and instruments

Money, rates, quantities, tax amounts, allocations, and balances never use JavaScript floating point. The reference exact-value primitive will use a coefficient and scale with a `bigint`-safe representation, or an equivalently exact design. Public contracts serialise values as decimal strings.

The model distinguishes functional, transaction, settlement, and presentation currencies or instruments; instrument quantity; functional valuation; rate, source, timestamp, and purpose; rounding; and realised or unrealised movement. Journals balance in the legal entity's functional currency while retaining other quantities and valuations.

Stable-value digital instruments remain financial instruments. Their metadata includes issuer, jurisdiction, reference asset, redemption, network, identifier, precision, custody, valuation sources, classifications, regulatory status, and risk status. The initial platform may record and reconcile them but does not hold private keys, provide custody, execute payments, or operate an exchange.

## Policy packs and subledgers

Effective-dated New Zealand policy packs distinguish primary-source fact, project interpretation, and implementation. They retain source authority, applicability, evidence, review status, golden examples, and package version. Historical outputs keep the policy version used.

Subledgers compile controlled business events into proposed journals while preserving evidence and domain detail. Future areas include receivables, payables, banking and settlement, fixed assets, inventory accounting, projects and job costing, payroll control imports, equity and shareholder accounts, intercompany transactions, digital instruments, and treasury. They do not bypass the command layer or kernel invariants, and they reconcile to general-ledger control accounts. Specialist workflows may remain in external systems when controlled events, totals, evidence, and reconciliation enter NZ Ledger.

## Reconciliation, close, reporting, and workpapers

Reconciliation compares authoritative sources, subledger totals, control accounts, settlements, and valuations. The close engine provides evidence-backed controls for bank accounts, cards, wallets and custodians, receivables, payables, GST, payroll control accounts, fixed assets, inventory, shareholder accounts, foreign-currency balances, suspense, accruals, prepayments, duplicates, missing evidence, unusual transactions, and balance-sheet substantiation. A period may hard-close only when required controls pass or an authorised person accepts a recorded exception.

Reports and workpapers are derived from posted history through versioned calculation contracts and link back to evidence. The intended surface includes financial statements, comparative periods, accounting policy statements, tax and GST reconciliations, fixed-asset schedules, equity movements, filing mappings, associated-party schedules, judgement and exception registers, evidence indexes, as-filed snapshots, current corrected positions, and complete exports.

## Persistence and accountability

PostgreSQL is the future reference transactional store. Posting, idempotency, period locks, journal writes, audit events, and transactional-outbox records share atomic boundaries. Evidence uses immutable, versioned object storage. Read models may be separate where useful, but do not become authoritative financial history.

The data model distinguishes effective time from recorded time. It must reproduce both the current corrected position and the position known at a prior filing, approval, or close. Export and tested recovery are required platform capabilities.

## Extension boundary

Extensions use versioned command, query, event, evidence, and agent-tool contracts. They receive only granted data and permissions. They cannot write core storage directly, run arbitrary code inside the kernel, bypass authority or invariants, replace official policy packs silently, or couple consumers to internal tables.

## Future logical package direction

The following shape is illustrative only. These directories do not exist yet and will not be created until accepted design issues define the implementation milestone.

```text
packages/
|-- core/
|-- contracts/
|-- conformance/
|-- nz-policy/
|-- postgres/
`-- agent-controls/

modules/
|-- receivables/
|-- payables/
|-- banking/
|-- assets/
`-- close/
```

The launch architecture is not microservices, does not event-source every workflow, and does not create a generic international tax engine.
