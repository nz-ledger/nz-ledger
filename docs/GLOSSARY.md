# Glossary

These definitions describe project usage and do not establish new accounting policy.

| Term | Project meaning |
| --- | --- |
| Account | A classified record used to accumulate financial effects within a book. |
| Adjustment | A separate journal that corrects, accrues, reallocates, or otherwise updates financial position without mutating posted history. |
| Agent | A software principal that observes, proposes, or performs bounded actions under an explicit mandate. |
| Allocation residual | The exact remainder left when a total cannot be divided into parts at a target scale, assigned by an explicit published rule so that the parts always sum to the total. |
| Audit-domain event | An immutable, attributable fact that a controlled accounting-domain transition occurred or was rejected, distinct from its delivery state. |
| Book | A coherent set of accounting records for a legal entity and defined purpose. |
| Business event | A versioned record of something with potential financial significance before or alongside accounting compilation. |
| Coefficient and scale | The exact representation of a financial value as a signed integer coefficient and a non-negative scale, where the value is the coefficient multiplied by ten to the power of the negative scale. |
| Command | A versioned request by a principal to perform a controlled state-changing action. |
| Conformance | Evidence that an implementation matches named public contracts and observable test vectors for a stated version and profile. |
| Control account | An account explicitly designated to reconcile a named subledger or controlled source to the general ledger under effective-dated rules. |
| Dimension | A controlled analytical axis, such as branch or project, whose values add domain detail without creating another posting boundary. |
| Evidence | Original or derived material supporting a business event, decision, journal, reconciliation, report, or output, with provenance and integrity metadata. |
| Exact value | A financial quantity represented and compared without precision loss, serialised as a decimal string within the bounds of its declared semantic class. |
| External identifier | A typed, issuer-scoped identifier assigned by another authority or source system that never replaces stable internal identity. |
| Extension | Independently developed functionality using public contracts and granted permissions without direct core-storage access. |
| Financial instrument | A unit or arrangement with quantity, rights, obligations, valuation, settlement, classification, and risk characteristics, including but not limited to fiat currencies. |
| Functional currency | The currency in which a legal entity's journal balances and primary financial effects are measured. |
| Instrument identifier | A typed scheme and code pair that identifies a currency or other financial instrument, where a bare code or symbol is never sufficient identity. |
| Journal | An immutable posted record of balanced financial effects in one book. |
| Journal line | One immutable debit or credit effect on one account within a posted journal. |
| Journal proposal | A controlled, versioned proposal for financial effects that has no ledger effect until accepted posting succeeds. |
| Legal entity | The accountable party whose financial effects define the mandatory posting and accounting-isolation boundary, without asserting one statutory form. |
| Ledger | A durable accounting namespace owned by one legal entity that groups books and their accounting sequences. |
| Mandate | An explicit, attributable, scoped, limited, expiring, and revocable grant of authority to a principal. |
| Minor unit | The fractional subdivision an instrument is recorded to, expressed as a declared, effective-dated scale rather than assumed from a code. |
| Module | A capability component that implements a business or industry workflow through public contracts and core controls. |
| Organisation | A tenant-scoped administrative grouping of people, legal entities, and operations that is not itself a posting boundary. |
| Organisation part | A branch, location, or other operating component that may be represented by dimensions or subledger detail without becoming a universal kernel aggregate. |
| Period | A non-overlapping book-scoped range of accounting dates with an explicit posting lock state. |
| Policy pack | A versioned, effective-dated collection of source-linked facts, explicit interpretations, mappings, evidence requirements, and golden examples. |
| Principal | An identified human, agent, integration, or scheduled rule capable of requesting or approving actions. |
| Rate provenance | The explicit record of where a conversion rate came from, including source identity and version, quotation direction, observation time, purpose, and the rounding applied. |
| Recorded time | When the platform learned, recorded, corrected, or approved information. |
| Reconciliation | A controlled comparison of authoritative records, expected effects, evidence, balances, or totals, with exceptions and resolution. |
| Reporting entity | A presentation composition that may be a legal entity, part of one, or multiple entities and does not replace the legal-entity posting boundary. |
| Reversal | A separate linked journal that offsets all or part of a posted journal without changing the original. |
| Rounding mode | A named, exactly defined rule for the digits discarded when a value is reduced to a target scale, always stated explicitly and never defaulted. |
| Settlement instrument | The currency or financial instrument used to discharge an obligation. |
| Subledger | A controlled domain component that retains business detail and compiles accepted events into proposed financial effects reconciled to control accounts. |
| Tenant | The highest application security and administration boundary; it is not an accounting or reporting conclusion. |
| Valuation | An attributable measurement of an instrument quantity in a specified currency using a rate, source, timestamp, purpose, and rounding policy. |
