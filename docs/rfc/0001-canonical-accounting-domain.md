# RFC 0001: Canonical accounting domain and invariants

- **Status:** Accepted
- **Author:** `@nz365guy`
- **Reviewers:** `@nz365guy` (sole-maintainer domain and decision review), Codex Security (security review); independent accounting-domain review deferred under the bootstrap exception below
- **Created:** 2026-07-31
- **Related issues:** #3

## Problem and outcome

NZ Ledger needs one policy-neutral accounting vocabulary and one mechanically testable invariant set before exact values, public contracts, conformance, persistence, policy packs, or implementation can be designed coherently.

The founding documents establish important properties, but they do not yet define every core concept's identity, lifecycle, relationships, temporal basis, and isolation boundary. They also do not yet separate universal posting semantics from business subledgers, changing policy, and presentation with enough precision for independent implementations to produce equivalent results.

This RFC defines that stable domain. Its intended observable outcome is that two implementations given the same explicit state, command inputs, and accepted contract versions can decide whether a proposed journal is valid and describe the same resulting domain facts without relying on a user interface, transport, database, reporting framework, or New Zealand policy rule.

The words **MUST**, **MUST NOT**, **SHOULD**, and **MAY** are normative in this RFC. Stable invariant IDs are normative. Example field names illustrate semantics and do not pre-empt the language-neutral schemas in #6.

## Scope and non-goals

### In scope

- the accounting-kernel boundary;
- tenant, organisation, legal-entity, ledger, and book context;
- accounts, effective-dated classifications, dimensions, and periods;
- journal proposals, posted journals, lines, reversals, and adjustments;
- control-account, business-event, evidence, policy-version, report-calculation, audit-event, and external-identifier references;
- effective and recorded time;
- isolation, immutability, balance, correction, attribution, and determinism invariants;
- lifecycle and relationship rules;
- golden scenarios that test architectural durability; and
- decisions, deferred questions, and ownership of follow-up work.

### Non-goals

This RFC does not define:

- implementation code, package structure, database schema, API, CLI, or user interface;
- a chart of accounts, account numbering scheme, reporting taxonomy, or financial-statement layout;
- exact-decimal bounds, scale, currency metadata, rate, valuation, rounding, or allocation semantics, which belong to #5;
- canonical bytes, schemas, identifier syntax, or compatibility rules, which belong to #6;
- command, query, event, evidence, or error envelopes, which belong to #8;
- detailed evidence storage or retention, which belongs to #10;
- persistence, transaction, or concurrency design, which belongs to #11;
- bitemporal report and filing-snapshot design, which belongs to #12;
- New Zealand policy-pack rules, which belong to #13 and later policy issues;
- multicurrency recognition, settlement, or revaluation rules, which belong to #15;
- foundational authority semantics, which belong to #17;
- reconciliation and close workflows, which belong to #19;
- consolidated, combined, or separate financial-statement calculations;
- payment execution, custody, tax filing, or external completion; or
- a claim of legal, tax, accounting, regulatory, security, or production approval.

## Source boundary

Current official New Zealand sources were checked on 31 July 2026 to test, not dictate, the kernel boundary:

- XRB's accounting-standards framework distinguishes sectors and tiers. Those changing applicability and presentation requirements therefore belong above the universal kernel.
- The 2018 NZ Conceptual Framework states that a reporting entity can be one entity, part of an entity, or more than one entity, and is not necessarily a legal entity. A reporting-entity boundary therefore cannot safely replace the legal-entity posting boundary.
- XRB's PBE material also treats reporting entities separately from legal entities and applies a different framework to public-benefit entities.
- NZ IAS 8 addresses accounting policies, estimates, and correction of prior-period errors for its stated scope. It informs policy and reporting design but does not establish one universal mutation rule for every organisation served by NZ Ledger.
- Companies Act 1993 section 194 requires applicable companies to keep accounting records that correctly record their transactions and maintain satisfactory control. This supports integrity and recoverability as design tests but is not treated as a universal definition for every entity type.

The resulting architecture deliberately uses stable accounting mechanics below effective-dated policy and reporting layers. No source above is converted into an unreviewed universal accounting rule.

## Separation of concerns

### Accounting kernel

The kernel owns only the stable facts and transitions required to produce controlled accounting records:

- scoped identities and typed references;
- ledgers, books, accounts, account classifications, dimensions, and periods;
- journal proposals as inputs and posted journals as immutable outcomes;
- exact functional effects through the contract later accepted in #5;
- correction relationships;
- deterministic invariant evaluation;
- domain versions and accounting sequences; and
- audit-domain facts produced by accepted transitions.

The kernel MUST NOT fetch time, identifiers, rates, policies, identity-provider data, evidence, or external outcomes. Every nondeterministic input MUST be supplied explicitly through a controlled boundary.

### Business domains and subledgers

Business domains retain invoices, bills, settlements, assets, inventory, payroll results, projects, contracts, and other operational meaning. A subledger MAY compile an accepted business event into a journal proposal and reconciliation totals. It MUST use the controlled command layer and MUST NOT write accounting state directly.

The kernel records typed references to the accepted business event and relevant control account. It does not make the general ledger the only store of business-domain detail.

### Policy

Policy determines recognition, classification, measurement choices, mappings, evidence requirements, effective dates, and other rules that differ by entity, framework, jurisdiction, or time. Policy MUST be versioned and selected explicitly outside the kernel. A posted journal retains the policy-version references used to compile or authorise it when policy affected the result.

The kernel enforces only invariants that remain true regardless of policy pack. It MUST NOT infer a policy version or silently recalculate posted history when policy changes.

### Presentation

Reports, financial statements, workpapers, filing mappings, consolidation, elimination, comparative views, and user experiences are projections over controlled records. Each material output identifies its report-calculation version, selected policy versions, effective-time basis, recorded-time basis, and source records.

Presentation logic MUST NOT mutate posted journals or become an alternative posting path.

## Canonical context and ownership

```text
Tenant
  |-- Organisation
  |     `-- Organisation part
  `-- Legal entity
         `-- Ledger
                |-- Book
                |     |-- Account
                |     |-- Dimension and dimension value
                |     |-- Accounting period
                |     `-- Posted journal
                |            `-- Journal line
                `-- Audit-domain sequence

Business event, evidence, policy, report calculation, and external identifiers
remain independently owned records referenced through typed, versioned links.
```

A tenant is the top security and administration boundary. An organisation is an administrative grouping. A legal entity is the mandatory accounting ownership and isolation boundary. A ledger and book refine that boundary for accounting purpose and measurement. A reporting entity is a presentation composition and is not a posting owner.

No posted journal crosses a tenant, legal entity, ledger, or book boundary. Intercompany and group activity creates separate balanced journals for each affected legal entity, linked through business-event and reconciliation references above the posting transaction.

## Canonical concepts

### Tenant

**Meaning:** The highest application isolation boundary for one controlled customer or administrative domain. Tenant is a security concept, not an accounting-policy conclusion.

**Identity:** One stable internal `tenant_id`. A tenant identifier MUST never be inferred from another identifier.

**Lifecycle:** `active -> suspended -> closed`. Suspension blocks new consequential commands but does not alter history. Closure prevents new activity and preserves export, recovery, and required access. Reactivation, if later supported, is a separately authorised, audited transition.

**Relationships:** A tenant owns organisations, legal entities, principals, mandates, and all tenant-scoped references. Every kernel aggregate belongs to exactly one tenant.

**Temporal basis:** Lifecycle transitions carry explicit recorded time. Closure and suspension do not change the effective or recorded time of existing records.

**Isolation:** A reference resolved in one tenant MUST NOT resolve to an object in another tenant, even if opaque identifier values collide.

### Organisation

**Meaning:** A real-world or administrative grouping used to organise people, legal entities, and operations. It does not own posted financial effects unless it is also represented by a legal entity.

**Identity:** One stable internal `organisation_id` within a tenant, plus optional typed external identifiers.

**Lifecycle:** `active -> inactive`. Structural changes create effective-dated relationship versions rather than rewriting prior ownership.

**Relationships:** An organisation MAY relate to one or more legal entities and MAY contain organisation parts. Legal ownership, control, and reporting composition are separate effective-dated relationships and MUST NOT be inferred from display hierarchy.

**Temporal basis:** Membership and structural relationships have explicit effective intervals and recorded versions.

**Isolation:** An organisation belongs to one tenant. An organisation hierarchy cannot bridge tenants.

### Legal entity

**Meaning:** The person, body, trust, partnership, or other accountable party whose books record its financial effects. This is the mandatory posting and isolation boundary. The term does not assert a particular statutory form.

**Identity:** One stable internal `legal_entity_id` within a tenant, with typed external legal identifiers where applicable.

**Lifecycle:** `active -> inactive -> closed`. A change of name, legal form metadata, or external identifier creates an effective-dated version. Historical identity is not replaced. A merger, combination, or succession does not silently reuse an identity.

**Relationships:** A legal entity owns one or more ledgers. It MAY relate to organisations and organisation parts. Group and reporting-entity relationships are presentation metadata, not permission to post across the legal-entity boundary.

**Temporal basis:** Existence and attributes have effective intervals and recorded versions. A journal's references MUST be valid for its accounting date under the recorded version used by the command.

**Isolation:** A posted journal belongs to exactly one legal entity. Cross-entity transactions require independently balanced postings per entity.

### Organisation part, branch, or location

**Meaning:** An operating component within one organisation and, where used in accounting, one legal entity. It is not automatically a separate ledger or reporting entity.

**Identity:** One stable typed identifier with an explicit part kind. Display names are not identities.

**Lifecycle:** `active -> inactive`, with effective-dated parent and legal-entity relationships. Reorganisation creates new relationship versions.

**Relationships:** A part MAY be represented by a dimension value, subledger owner, or reporting filter. Its hierarchy MUST be acyclic. A part associated with multiple legal entities requires distinct entity-scoped accounting representations.

**Temporal basis:** All parent, type, and accounting mappings are effective-dated and recorded.

**Isolation:** A part reference on a journal line MUST resolve inside the journal's tenant and legal entity.

### Ledger

**Meaning:** A durable accounting namespace owned by one legal entity. It groups books and their accounting sequences without prescribing storage layout.

**Identity:** One stable `ledger_id` within a tenant and legal entity.

**Lifecycle:** `defined -> active -> closed`. A closed ledger accepts no new postings but remains fully readable and exportable. Replacing a ledger creates a new identity and explicit succession link.

**Relationships:** A ledger belongs to exactly one legal entity and contains one or more books. It owns ledger-scoped version and audit ordering boundaries.

**Temporal basis:** Activation and closure are explicit recorded transitions. Ledger metadata is versioned.

**Isolation:** No journal, account, book, period, or dimension reference can resolve outside its ledger context.

### Book

**Meaning:** A coherent, independently balanced set of accounting records for one legal entity, ledger, purpose, and functional measurement context.

**Identity:** One stable `book_id` within a ledger.

**Lifecycle:** `defined -> active -> closed`. Purpose or measurement changes that would alter historical meaning require an effective-dated version or new book, as later defined by #5 and #15.

**Relationships:** A book belongs to exactly one ledger and legal entity. It contains accounts, dimensions, periods, posted journals, and a deterministic aggregate version. A book references one functional currency or instrument for each non-overlapping effective interval.

**Temporal basis:** Book configuration is effective-dated and recorded. The selected configuration version is explicit for each posting decision.

**Isolation:** A journal balances within one book. There is no cross-book journal. Transfers between books use separate postings and an explicit shared business-event or reconciliation reference.

### Account and account classification

**Meaning:** An account is a stable book-scoped record that accumulates one class of financial effect. Classification describes its effective-dated semantic or presentation role without changing identity.

**Identity:** One stable `account_id` within a book. Codes and names are mutable business identifiers, not internal identity.

**Lifecycle:** `defined -> active -> inactive`. An inactive account remains resolvable for history. Reactivation or replacement is an explicit recorded transition. Existing posted lines are never re-pointed.

**Relationships:** An account belongs to one book. It MAY have effective-dated classification, hierarchy, control-account, and dimension-rule references. Any hierarchy MUST be acyclic. Classification namespaces and versions are explicit.

**Temporal basis:** Availability, code, name, classification, and relevant constraints have non-overlapping effective intervals and recorded versions. The applicable version is selected by the journal accounting date and the command's recorded-state basis.

**Isolation:** An account reference MUST match the journal's tenant, legal entity, ledger, and book.

### Dimension and dimension value

**Meaning:** A dimension is a controlled axis for retaining accounting analysis such as branch, project, job, or cost centre without making that business concept a universal kernel aggregate.

**Identity:** A stable `dimension_id` and stable `dimension_value_id`, both scoped to one book. Codes and labels are not identities.

**Lifecycle:** Definitions and values are `defined -> active -> inactive`. Rules, labels, parent relationships, and mappings are effective-dated. Historical references remain resolvable.

**Relationships:** A dimension declares allowed value structure and explicit account or posting constraints. A value belongs to one dimension. Hierarchies MUST be acyclic. The kernel validates supplied values and applicable rules but does not infer a missing value from evidence or another dimension.

**Temporal basis:** Definitions, values, and applicability rules are valid for explicit intervals and recorded versions.

**Isolation:** A dimension value MUST resolve within the journal's book and legal entity.

### Accounting period and lock state

**Meaning:** A book-scoped range of accounting dates with an explicit posting state.

**Identity:** One stable `period_id` within a book. The date interval is an attribute, not the identity.

**Lifecycle:** `defined -> open -> locked`. A locked period MAY later transition to open only through a separately authorised and audited reopen action defined by close controls. No transition changes existing journal dates or contents.

**Relationships:** Periods belong to one book. Their accounting-date intervals MUST NOT overlap. Every posted journal resolves to exactly one period by its explicitly supplied accounting date.

**Temporal basis:** Date range is effective-time data. State transitions carry explicit recorded time, principal, authority decision, reason, and aggregate version.

**Isolation:** A period from another book or entity is invalid even if its dates match.

### Journal proposal

**Meaning:** A controlled, versioned proposal for financial effects. It is not posted history and has no ledger effect.

**Identity:** One stable `journal_proposal_id` plus immutable revision number. Each revision has its own canonical payload digest when #6 is accepted.

**Lifecycle:** `draft -> submitted -> accepted | rejected | withdrawn | superseded`. Successful acceptance MUST create exactly one posted journal and record one complete outcome containing that journal's identity in the same atomic transition. No committed `accepted` state exists without that journal. Rejection, withdrawal, and supersession are non-posting terminal outcomes. Editing creates a new revision; it does not rewrite a submitted or decided revision.

**Relationships:** A proposal names one tenant, legal entity, ledger, and book; proposed lines; business-event, evidence, policy, and authority references; command and idempotency context; effective time; accounting date; and all nondeterministic inputs.

**Temporal basis:** Effective time, accounting date, and each revision's recorded time are explicit and distinct.

**Isolation:** Proposal validation fails closed on any unresolved or mismatched scoped reference. A proposal cannot itself be queried as a posted balance.

### Posted journal

**Meaning:** The immutable, accepted record of balanced financial effects in one book.

**Identity:** One stable `journal_id` within a book, generated outside the kernel and supplied explicitly. The journal also carries an immutable book sequence and aggregate version assigned deterministically from accepted state.

**Lifecycle:** A posted journal has one state: `posted`. It is never edited, deleted, unposted, or changed to `reversed`. Correction status and remaining unreversed effect are derived from later linked journals.

**Relationships:** A journal belongs to exactly one tenant, legal entity, ledger, book, and period; contains at least two valid lines unless a later accepted exact-value design proves another mechanically balanced form; and retains command, proposal, principal, authority-decision, business-event, evidence, policy-version, correlation, causation, and correction references applicable to the posting.

**Temporal basis:** `effective_at`, `accounting_date`, and `recorded_at` are explicit. The accounting date selects the period and effective-dated accounting configuration. Recorded time identifies when the platform accepted the fact. Later knowledge never rewrites either value.

**Isolation:** Every contained and referenced accounting object is validated in the journal's full scope.

### Journal line

**Meaning:** One immutable debit or credit effect on one account within a posted journal.

**Identity:** One stable `journal_line_id` unique within the journal and a stable explicit line order for canonical output.

**Lifecycle:** A line is created atomically with its journal and never changes independently.

**Relationships:** A line references exactly one account, one side (`debit` or `credit`), an exact functional effect, optional explicitly valued instrument quantities, and zero or more valid dimension values. Evidence or business-event detail MAY be referenced at line level when attribution differs by line.

**Temporal basis:** A line inherits the journal's effective, accounting, and recorded basis. Any separately supplied valuation timestamp remains distinct.

**Isolation:** Account and dimensions MUST resolve in the same book as the journal. A line cannot transfer value across scope.

### Reversal

**Meaning:** A posted journal whose declared correction kind offsets all or an explicit part of one or more earlier posted journals without changing them.

**Identity:** The reversal is an ordinary posted journal with its own `journal_id`. Its typed correction links identify target journals and line mappings.

**Lifecycle:** It is posted once and immutable. A reversal may itself be corrected only by another separately linked posted journal.

**Relationships:** Targets MUST be in the same tenant, legal entity, ledger, and book. Each mapped effect MUST be the exact opposite side and value under the #5 contract. Cumulative reversal cannot exceed the target's unreversed effect. A full reversal offsets every target effect; a partial reversal identifies exactly what remains.

**Temporal basis:** A reversal is recorded after every target. Its accounting date MUST be in an open period. Its effective time and reason for correction are explicit. It does not change the target's times.

**Isolation:** Cross-entity or cross-book reversal is invalid. Related cross-entity corrections require separate reversal journals.

### Adjustment

**Meaning:** A posted journal that corrects, accrues, reallocates, or otherwise updates financial position while explicitly relating to earlier records and preserving them.

**Identity:** The adjustment is an ordinary posted journal with its own `journal_id` and typed correction links.

**Lifecycle:** It is posted once and immutable. Later changes use another linked correction.

**Relationships:** An adjustment MUST identify its reason, applicable prior journal or business-event references, evidence, policy version where relevant, and authority decision. Unlike a reversal, it need not be an exact inverse.

**Temporal basis:** The adjustment uses its own open accounting period, effective time, and recorded time. Reports decide, under versioned calculations and policy, whether to present an as-filed or current-corrected view.

**Isolation:** All corrected accounting records are in the same tenant, legal entity, ledger, and book. Cross-entity consequences use separate journals.

### Control account

**Meaning:** An account explicitly designated to reconcile a named subledger or controlled source to the general ledger.

**Identity:** The underlying account keeps its `account_id`; the control designation is an effective-dated typed relationship with its own version.

**Lifecycle:** Designations are `proposed -> active -> inactive` and retain history. Changing a designation does not reclassify old postings silently.

**Relationships:** A designation names the subledger or source namespace, reconciliation basis, and exactly one accepted active posting policy. Missing, inactive, or ambiguous policy fails closed. Direct posting is not universally forbidden, but it is permitted only when the effective policy explicitly allows the authenticated source or a separately authorised and evidenced exception. The policy version and decision MUST be enforced through the command layer and recorded.

**Temporal basis:** The designation and restrictions are effective-dated and recorded. Reconciliation outputs retain the version used.

**Isolation:** A control designation cannot relate an account to a subledger in another tenant or legal entity.

### Business-event reference

**Meaning:** A typed, versioned link to a business-domain fact that caused or explains proposed financial effects.

**Identity:** Explicit source namespace, stable event identifier, version, and optional canonical digest as later defined by #6 and #8.

**Lifecycle:** Owned by its business domain. The kernel stores an immutable reference, not a mutable copy or inferred completion state.

**Relationships:** One event MAY cause proposals or journals in multiple legal entities, but each resulting journal remains separately balanced and isolated. Correlation does not merge accounting ownership.

**Temporal basis:** Source occurrence, effective, and recorded times remain distinct where supplied.

**Isolation:** Resolution is tenant-scoped and subject to legal-entity access. A reference does not grant authority.

### Evidence reference

**Meaning:** A typed, versioned link to original or derived material supporting a decision or record.

**Identity:** Stable evidence identifier, version, digest, and source namespace under the minimal contract accepted in #8.

**Lifecycle:** Owned by the evidence domain. Original evidence is immutable and never replaced by an extraction. New evidence or correction creates a new version or derivation link.

**Relationships:** Journals, lines, proposals, decisions, and events MAY reference evidence. A reference MUST identify whether the material is original or derived and preserve provenance.

**Temporal basis:** Acquisition, effective, and recorded times are explicit and not collapsed.

**Isolation:** Access to a journal does not automatically grant access to all evidence content. Missing or unauthorised evidence is not interpreted as proof.

### Policy-version reference

**Meaning:** A typed link to the exact effective-dated policy or interpretation used in a decision.

**Identity:** Policy namespace, policy identifier, immutable version, and integrity reference.

**Lifecycle:** Owned by the policy domain. Supersession is prospective and does not alter references on existing records.

**Relationships:** A proposal, authority decision, valuation, journal, report, or correction records every policy version material to its result.

**Temporal basis:** Policy effective interval and recorded selection time remain explicit.

**Isolation:** Policy selection is scoped and authorised. The kernel never substitutes a newer or different policy silently.

### Report-calculation reference

**Meaning:** A typed link to the exact calculation contract used to project accounting records into a report, workpaper, filing mapping, or export view.

**Identity:** Calculation namespace, identifier, immutable version, and digest.

**Lifecycle:** Owned by the presentation domain. A new calculation version does not alter prior report artefacts.

**Relationships:** Report outputs reference source journals, policy versions, calculation version, effective basis, recorded basis, and output digest. Posted journals do not depend on a report calculation to remain valid.

**Temporal basis:** The calculation's effective applicability and the output's generation time are explicit.

**Isolation:** A report calculation cannot expand the caller's authorised tenant or legal-entity scope.

### Audit-domain event

**Meaning:** An immutable fact that a controlled accounting-domain transition occurred or was rejected.

**Identity:** Stable event identifier supplied explicitly, typed event name and version, aggregate identity and version, and deterministic ordering boundary.

**Lifecycle:** Created once and immutable. Delivery, acknowledgement, or projection state is not part of the domain fact.

**Relationships:** Events reference the command, principal, authority decision, aggregate, correlation, causation, contract version, and resulting or rejected record. Event publication later uses the envelope accepted in #8.

**Temporal basis:** Occurrence, effective, and recorded time are explicit where applicable. Delivery time is operational metadata and MUST NOT rewrite the event.

**Isolation:** An event carries tenant and legal-entity scope. Consumers receive only authorised data, and receipt grants no command authority.

### External identifier

**Meaning:** A typed identifier assigned by another authority or source system. It supports interoperability but never replaces stable internal identity.

**Identity:** A tuple of identifier type, namespace or issuer, value, and effective version where applicable. String value alone is never sufficient.

**Lifecycle:** `active -> superseded | inactive`, with history retained. Reuse and uniqueness follow the explicit issuer contract rather than assumption.

**Relationships:** An internal object MAY have multiple external identifiers. One external identifier mapping resolves to at most one active object in its declared tenant, type, issuer, and effective interval.

**Temporal basis:** Mappings are effective-dated and recorded. Historical lookup uses the applicable version.

**Isolation:** External identifier lookup always includes tenant, object type, and issuer scope. Identifier value substitution cannot cross a legal-entity boundary.

## Temporal model

The domain preserves, at minimum, three different temporal meanings:

1. **Effective time:** when the underlying fact or relationship applies in the represented world.
2. **Accounting date:** the book date on which a journal enters balances and an accounting period.
3. **Recorded time:** when NZ Ledger learned, accepted, changed, or recorded the information.

These values MUST be supplied explicitly where applicable. They MUST NOT be derived from system time inside the kernel. A valuation source time, evidence acquisition time, event occurrence time, approval time, and external completion time remain separately named when present.

An implementation MUST be able to preserve both the current corrected record and the facts known at an earlier recorded-time boundary. The query and persistence mechanisms for doing so are deferred to #8, #11, and #12.

## State transitions

```text
Journal proposal revision
  draft -> submitted -- accept --> accepted + exactly one posted journal
                     |             one atomic transition and recorded outcome
                     |-- reject --> rejected
                     |-- withdraw -> withdrawn
                     `-- replace --> superseded by a new immutable revision

Posted journal
  posted -> posted forever
       later reversal or adjustment adds a separate linked posted journal

Accounting period
  defined -> open -> locked
                    `-> open only through a separately authorised audited reopen

Account or dimension value
  defined -> active -> inactive
  historical references remain resolvable in every state
```

State transitions that affect command eligibility MUST increment the applicable aggregate version and emit an audit-domain fact. The exact command and event envelopes are deferred to #8.

## Invariant catalogue

Each invariant below is independent of transport and storage. Failure codes are stable semantic names proposed for later registration in #8. Exact numeric comparison is subject to the accepted #5 contract.

| ID | Normative statement | Rationale | Valid example | Invalid example | Expected failure | Security consequence |
| --- | --- | --- | --- | --- | --- | --- |
| `KRN-001` | Every command, proposal, journal, line reference, and resulting event MUST carry one consistent tenant, legal entity, ledger, and book context. | Prevents ambiguous ownership and substitution. | All lines resolve in book B1 owned by entity E1 in tenant T1. | One line names an account in E2. | `CONTEXT_MISMATCH` | Cross-tenant or cross-entity financial corruption. |
| `KRN-002` | A ledger MUST belong to exactly one legal entity, and a book MUST belong to exactly one ledger and legal entity for each effective interval. | Establishes a single accounting owner. | B1 belongs to L1 and E1. | B1 is concurrently attached to E1 and E2. | `OWNERSHIP_AMBIGUOUS` | Isolation and accountability bypass. |
| `KRN-003` | A posted journal MUST belong to exactly one book and MUST NOT contain cross-book or cross-entity effects. | Balance is meaningful only within one measurement boundary. | Intercompany activity creates one journal per entity. | One journal debits E1 and credits E2. | `JOURNAL_SCOPE_INVALID` | Concealed unbalanced entity records. |
| `KRN-004` | Every posted line MUST specify exactly one side, debit or credit, and MUST NOT represent both. | Removes sign and side ambiguity. | A line has side `debit`. | Debit and credit fields are both populated. | `LINE_SIDE_INVALID` | Parser differentials can alter financial effect. |
| `KRN-005` | A posted line MUST contain at least one non-zero exact financial effect. A zero functional effect is allowed only when an accepted #5 contract records another non-zero quantity and explicit valuation semantics. | Prevents audit noise while preserving non-fiat quantity cases. | A non-zero instrument quantity has explicit zero functional valuation and provenance permitted by #5. | Functional effect and every quantity are zero. | `LINE_EFFECT_EMPTY` | Meaningless records can hide replay or manipulation. |
| `KRN-006` | Total debit functional effects MUST equal total credit functional effects exactly within the journal's book. No posting-time tolerance or implicit rounding is allowed. | Balance is a core accounting invariant. | Exact debit 10.00 equals exact credit 10.00. | Debit 10.00 and credit 9.999. | `JOURNAL_UNBALANCED` | Direct financial-integrity loss. |
| `KRN-007` | Every financial effect, quantity, rate, and valuation MUST use the accepted exact-value semantics from #5; public financial values MUST NOT be floating-point numbers. | Prevents precision loss and parser reinterpretation. | Decimal strings and explicit semantic types are supplied. | A JSON number `0.1` supplies a journal amount. | `FINANCIAL_VALUE_INVALID` | Rounding exploitation or nondeterministic balances. |
| `KRN-008` | A book MUST have exactly one applicable functional currency or instrument for the journal accounting date, and every line MUST provide an exact functional effect in that context. | Establishes the measurement basis for balance. | All lines are valued in B1's applicable NZD context. | A line omits functional valuation or uses another book's context. | `FUNCTIONAL_CONTEXT_INVALID` | Rate or instrument substitution. |
| `KRN-009` | Account, account classification, dimension, dimension value, book configuration, and legal-entity references MUST be valid for the journal accounting date under the explicit recorded-state basis. | Prevents backdating into unavailable configuration. | Account A1 is active for the supplied date and version. | A1 was not yet active on that date. | `REFERENCE_NOT_EFFECTIVE` | Backdated or misclassified posting. |
| `KRN-010` | Every required dimension MUST be supplied explicitly, every supplied value MUST be allowed, and the kernel MUST NOT infer a value. | Preserves analytical and authority boundaries. | Project is supplied where the account rule requires it. | Required branch is omitted and defaulted from user profile. | `DIMENSION_INVALID` | Misallocation or cross-branch leakage. |
| `KRN-011` | Exactly one book period MUST contain the journal accounting date, and that period MUST be open when posting is accepted. | Enforces period control deterministically. | Date falls in one open period. | Date falls in a locked or overlapping period. | `PERIOD_NOT_OPEN` or `PERIOD_AMBIGUOUS` | Lock bypass and retrospective manipulation. |
| `KRN-012` | A posted journal and every contained line MUST be immutable and undeletable through domain commands. | Preserves accountable history. | A correction creates J2 linked to J1. | An update changes J1's amount or account. | `POSTED_HISTORY_IMMUTABLE` | Evidence destruction or concealed fraud. |
| `KRN-013` | Every correction MUST be a separately balanced posted journal with a declared kind, reason, and typed link to earlier records. | Makes correction attributable without mutation. | J2 is an adjustment linked to J1 with evidence. | J1 is edited and annotated as corrected. | `CORRECTION_LINK_REQUIRED` | Silent history rewriting. |
| `KRN-014` | A reversal mapping MUST exactly oppose the declared target effects, and cumulative reversal MUST NOT exceed the remaining unreversed effect. | Makes full and partial reversal mechanically testable. | Credit 10 reverses an earlier debit 10 once. | Two reversals each offset the same full debit 10. | `REVERSAL_EXCEEDS_TARGET` | Duplicate correction or concealed value creation. |
| `KRN-015` | Correction links MUST stay within one tenant, legal entity, ledger, and book and MUST form an acyclic graph directed to records with an earlier recorded order. | Prevents substitution and circular status. | J3 adjusts earlier J1 in the same book. | J1 and J2 claim to correct each other. | `CORRECTION_GRAPH_INVALID` | Cyclic replay and misleading correction state. |
| `KRN-016` | Effective time, accounting date, and recorded time MUST be explicit and MUST retain their distinct meanings. | Supports reproducibility and bitemporal accountability. | A late invoice has earlier effective time and current recorded time. | Recorded time is silently copied into effective time. | `TEMPORAL_BASIS_INVALID` | Backdating, history confusion, or false as-filed results. |
| `KRN-017` | Internal identifiers MUST be typed, stable, unique in their declared scope, supplied explicitly, and never recycled to a different object. | Protects referential integrity and deterministic replay. | A supplied journal ID is unused in B1. | A deleted draft ID is reused for a posted journal. | `IDENTITY_CONFLICT` | Object substitution and audit ambiguity. |
| `KRN-018` | External identifiers MUST include type and issuer or namespace, and MUST NOT be treated as internal identity or global authority. | External values have different uniqueness and lifecycle rules. | `issuer=bank-x`, `type=statement-entry`, `value=123`. | Lookup uses bare value `123` across tenants. | `EXTERNAL_ID_AMBIGUOUS` | Cross-source or cross-tenant substitution. |
| `KRN-019` | Tenant and legal-entity scope MUST be enforced on commands, queries, events, evidence references, background work, exports, and privileged operations. | Isolation must hold across every path. | A T1 export contains only authorised T1/E1 records. | A retry job resolves an account without tenant scope. | `ISOLATION_VIOLATION` | Confidentiality and financial-integrity breach. |
| `KRN-020` | Acceptance MUST be idempotent in the scope and semantics later defined by #8: the same key and canonical payload returns the same complete outcome, including the same `journal_id` after success; a different payload conflicts. | Prevents duplicate financial effects. | A retry returns the original accepted outcome and J1 identity. | A retry returns an accepted outcome without J1 or posts J2. | `IDEMPOTENCY_CONFLICT` | Duplicate or substituted posting. |
| `KRN-021` | Proposal acceptance and internal posting MUST remain distinct audit-domain facts recorded in the same atomic acceptance transition. External submission and external completion remain distinct later facts. No later state is inferred from intent or an earlier state. | Preserves atomic accounting integrity without asserting success across trust boundaries. | Acceptance and J1 posting facts commit together while payment outcome remains unknown. | Acceptance commits without a journal, or posting marks a bank transfer completed. | `OUTCOME_INFERENCE_FORBIDDEN` | Partial internal success, false external completion, or unreconciled loss. |
| `KRN-022` | The kernel MUST NOT infer missing identifiers, time, values, rates, evidence, policy, authority, dimensions, success, or external state. | Determinism requires complete explicit inputs. | Missing rate rejects the proposal. | Current system time or a fetched rate fills the gap. | `EXPLICIT_INPUT_REQUIRED` | Nondeterminism and authority bypass. |
| `KRN-023` | Identical explicit input, accepted versions, and initial state MUST produce the same decision, financial result, audit-domain facts, aggregate version, and canonical data before transport metadata. | Reproducibility is a financial control. | Locale and insertion order do not change output. | Host timezone changes the accounting period. | `NONDETERMINISTIC_RESULT` | Results cannot be audited or independently reproduced. |
| `KRN-024` | A proposal MUST NOT affect balances, period totals, account activity, or posted-history queries before acceptance. Successful acceptance, exactly one posted journal and its lines, the acceptance and posting facts, attribution, and the complete idempotent outcome MUST commit atomically; failure commits none of them. | Separates intent from financial fact and excludes partial acceptance. | A submitted proposal appears only in workflow queries; acceptance commits J1 and its complete outcome together. | Acceptance commits without a journal, or a journal becomes visible without its acceptance outcome. | `PROPOSAL_NOT_POSTED` | Unauthorised, partial, or misleading financial state. |
| `KRN-025` | A consequential transition MUST record the authenticated principal, applicable authority decision, command, correlation, causation, evidence, and aggregate version required by accepted contracts. | Makes every effect attributable. | J1 links the permitting decision and command. | A scheduled job posts with no principal. | `ATTRIBUTION_REQUIRED` | Repudiation and mandate bypass. |
| `KRN-026` | Every posting to an active control account MUST resolve exactly one accepted effective posting policy. Missing, inactive, ambiguous, or source-mismatched policy is denied. Only a matching authenticated source or a separately authorised and evidenced exception MAY post. A subledger, integration, extension, or agent MUST NOT bypass this decision through direct storage access. | Preserves subledger reconciliation while allowing explicit legitimate exceptions. | A matching subledger command posts under the recorded active policy, or an authorised exception posts with its evidence. | A general-journal command posts when no policy exists, or an extension writes a row directly. | `CONTROL_ACCOUNT_RESTRICTED` | Concealed control-account drift or authority bypass. |
| `KRN-027` | Period, account, classification, dimension, control-designation, and relationship changes MUST be versioned and audited; they MUST NOT rewrite the configuration used by prior postings. | Preserves historical interpretation. | A new classification begins prospectively. | An old account version is overwritten. | `CONFIGURATION_HISTORY_IMMUTABLE` | Silent historical reclassification. |
| `KRN-028` | Original evidence MUST remain distinguishable from derived data, and a derived record MUST retain its provenance link. | Prevents extraction from replacing source truth. | An extracted total references the immutable source document version. | Parsed text overwrites the original evidence. | `EVIDENCE_PROVENANCE_INVALID` | Fabricated or unverifiable financial support. |
| `KRN-029` | Policy and report-calculation versions material to a result MUST be explicit and retained; supersession MUST NOT change a prior posting or output silently. | Separates stable mechanics from changing rules. | A corrected report records a new calculation version. | Updating a policy silently alters an as-filed output. | `VERSION_REFERENCE_REQUIRED` | Policy downgrade or retroactive manipulation. |
| `KRN-030` | Aggregate versions and book accounting sequences MUST advance monotonically and without duplicate positions for accepted transitions. | Supports ordering, optimistic concurrency, and replay. | Accepted posting moves version 7 to 8 once. | Two accepted journals claim book sequence 42. | `AGGREGATE_VERSION_CONFLICT` | Lost update, replay, or ambiguous order. |

## Golden-scenario outline

These scenarios define required architectural outcomes, not implementation fixtures. #9 will turn accepted scenarios into language-neutral vectors.

### `DOM-S001`: Sole operator

One tenant, organisation, legal entity, ledger, and book records a balanced journal. The scenario proves that administrative simplicity does not remove legal-entity, period, exact-value, attribution, or evidence boundaries.

Expected outcome: one immutable journal in one open period; deterministic audit-domain facts; no policy or report assumption in the kernel.

### `DOM-S002`: Multi-entity group

One tenant administers two legal entities with different books and functional currencies. A shared business event has effects in both.

Expected outcome: two independently authorised and balanced journals linked by the shared event and later reconciliation. A single cross-entity journal is rejected. Group reporting is a presentation composition.

### `DOM-S003`: Branch, project, job, asset, and inventory detail

A legal entity uses branch and project dimensions while asset and inventory modules retain their own domain records.

Expected outcome: supplied valid dimensions remain on journal lines; modules create controlled proposals and reconcile to control accounts; the kernel does not acquire asset-register or inventory-item semantics.

### `DOM-S004`: Structured invoice evidence

A fictional structured invoice is retained as original evidence, parsed into derived fields, accepted as a business event, and compiled by a receivables subledger.

Expected outcome: the journal references event, original evidence, derivation, policy, and command records. Parsed content never replaces original evidence.

### `DOM-S005`: Bank match and reconciliation

A fictional bank statement entry is matched to an existing settlement intent.

Expected outcome: the bank event is evidence of an external observation, not journal truth by itself. Posting and reconciliation remain separate controlled outcomes. Unknown bank state is not inferred as completion.

### `DOM-S006`: Bounded agent proposal

An agent with a limited mandate supplies a complete proposal inside one entity and book.

Expected outcome: actor type does not change invariants. The proposal remains non-posting until authority and approval requirements permit the command. Successful acceptance creates exactly one posted journal in the same atomic transition and records a complete outcome containing its identity. A same-key, same-payload retry returns that outcome without creating another journal. Confidence and tool availability grant no authority.

### `DOM-S007`: Reporting-policy change

A new policy or report-calculation version becomes effective after journals were posted.

Expected outcome: posted journals do not change. A new report can use the new version and a current-corrected basis while the prior report remains reproducible with its original versions.

### `DOM-S008`: Error discovered after period close

Evidence reveals an error in a journal whose period is locked.

Expected outcome: the original remains posted. The locked period rejects ordinary posting. An authorised correction is either posted in an applicable open period or follows a separately accepted reopen workflow. Links, reasons, times, evidence, and versions remain explicit.

### `DOM-S009`: As-filed and current-corrected views

A report was filed before a later adjustment was recorded.

Expected outcome: the as-filed view uses the earlier recorded-time boundary and original calculation and policy versions. The current-corrected view includes the later adjustment. Neither view mutates history.

### `DOM-S010`: Complete export and replay

An authorised export includes all scoped identities, effective-dated versions, posted journals, corrections, references, audit-domain facts, and integrity data required by accepted contracts.

Expected outcome: an independent implementation can reject missing or inconsistent records and reproduce the same domain state without internal database tables.

### `DOM-S011`: Reporting entity differs from legal entity

A report covers a branch, a legal entity, or a controlled group under an applicable reporting framework.

Expected outcome: reporting composition selects and eliminates authorised records above the kernel. Every source journal remains owned by exactly one legal entity and book.

### `DOM-S012`: Identifier substitution attack

A valid command for tenant T1 is altered to reference an account, dimension, evidence item, or correction target from T2 or another legal entity.

Expected outcome: validation fails closed with a context or isolation error before any posting or audit success event is produced.

### `DOM-S013`: Control-account posting policy

A principal authorised for ordinary general-journal commands targets an active control account first without one effective posting policy, then through its matching subledger source, and finally through a separately authorised and evidenced exception.

Expected outcome: the missing policy attempt fails with `CONTROL_ACCOUNT_RESTRICTED`. The matching source and explicit exception may post only when all other invariants pass, and each posted journal records the effective policy version, authenticated source, authority decision, and exception evidence where applicable.

## Alternatives

### Use reporting entity as the posting boundary

Rejected. Current XRB material confirms that a reporting entity can be a legal entity, part of one, or multiple entities. Reporting composition can change by purpose and framework. Using it as the posting owner would weaken legal-entity isolation and correction semantics.

### Use organisation as a combined accounting and administration boundary

Rejected. An organisation can administer multiple legal entities, and a legal entity can participate in more than one organisational or reporting relationship over time. The boundaries need separate typed identities.

### Use one universal `amount` and signed numeric line

Rejected. Side, exact quantity, functional effect, transaction instrument, rate, and valuation have distinct meanings. #5 must define them without floating point or sign ambiguity.

### Let journals change status to reversed or corrected

Rejected. A mutable status can obscure the original fact and depends on incomplete future context. Reversal and correction state is derived from separate linked posted journals.

### Store all business detail in journal lines

Rejected. It would turn the kernel into every future subledger, weaken domain evidence, and make extension dependent on internal storage. Business domains retain detail and produce controlled proposals and reconciliation totals.

### Make the entire platform event-sourced

Rejected. Immutable audit-domain facts and deterministic replay do not require universal event sourcing. Persistence decisions remain with #11 and the locked modular-monolith direction.

## Consequences

### Accounting integrity

- Balance, scope, period, correction, and immutability rules become mechanically testable.
- Reporting and policy can evolve without altering posted history.
- Intercompany and group activity require explicit per-entity accounting rather than a convenience cross-boundary journal.
- Zero functional-value quantity cases remain possible only under explicit #5 semantics.

### Security and privacy

- Tenant, legal-entity, ledger, and book context must be checked on every path, including lookup, background work, events, evidence, exports, and privileged operations.
- Typed identity and reference rules reduce identifier-substitution and confused-deputy risk.
- Access to a journal does not automatically disclose evidence content.
- Direct storage writes remain prohibited because they would bypass invariants and authority.

### Authority and evidence

- Actor type has no effect on accounting validity.
- Every consequential transition must be attributable to an explicit principal and authority decision under #17.
- Original and derived evidence remain distinct and linked.

### New Zealand policy

- Sector, tier, legal-form, reporting, tax, and filing requirements remain effective-dated policy or presentation concerns.
- Current official sources test the architecture but do not become unreviewed kernel rules.
- Group, trust, public-benefit, not-for-profit, and minimum-reporting needs can use the same posting mechanics while applying different policies and reports.

### Compatibility and operations

- #5, #6, #8, #9, #10, #11, #12, #15, #17, and #19 receive explicit semantic boundaries.
- Implementations need durable identity history, configuration versions, aggregate versions, and complete export.
- Database constraints may supplement these rules but cannot be the public definition of conformance.

## Validation

Acceptance and subsequent delivery use the following gates:

1. Every concept above has explicit identity, lifecycle, relationships, temporal basis, and isolation behaviour.
2. Every `KRN-*` invariant is judged mechanically testable in principle.
3. The golden scenarios demonstrate sole-operator, multi-entity, branch, project, subledger, structured-evidence, bank, agent, atomic acceptance, policy-change, close-correction, temporal-view, export, substitution, and fail-closed control-account cases without implementing those modules.
4. During sole-maintainer bootstrap, the maintainer MAY accept this documentation-only RFC after recording a domain self-review and disclosing that the review is not independent. An independent accounting-domain reviewer MUST record whether the vocabulary and invariants are suitable across for-profit, public-benefit, trust, not-for-profit, and minimum-reporting contexts before product implementation depends on this RFC or before production use, whichever is earlier. That review MUST also be requested promptly when a suitably qualified independent reviewer becomes available, including an additional maintainer who is suitably qualified and was not involved in the original drafting or acceptance.
5. A security reviewer records whether isolation, identity, correction, temporal, evidence, idempotency, and direct-write threats are addressed.
6. Maintainer review confirms consistency with the charter, architecture, founding decisions, glossary, threat model, capability model, conformance direction, and human-agent model.
7. #5, #6, #8, #9, #10, #11, #12, #15, #17, and #19 confirm or refine the deferred details without weakening these invariants.
8. Documentation links and source-register entries are current, `git diff --check` passes, required repository checks pass, and the DCO sign-off is present.

## Decision register

| ID | Decision | Status | Rationale | Owner or follow-up |
| --- | --- | --- | --- | --- |
| `DOM-D001` | Tenant is the highest security and administration boundary, not an accounting entity. | Accepted | Separates isolation from accounting and reporting meaning. | #3 |
| `DOM-D002` | Legal entity is the mandatory posting and accounting-isolation boundary. | Accepted | Preserves accountable books even when reporting composition differs. | #3 |
| `DOM-D003` | Ledger is a legal-entity accounting namespace; book is an independently balanced purpose and measurement boundary. | Accepted | Makes ownership, balance, and multiple-book use explicit. | #3 |
| `DOM-D004` | Reporting entity is a presentation composition above legal-entity postings. | Accepted | XRB frameworks do not equate reporting entity and legal entity. | #30 |
| `DOM-D005` | Journal proposal and posted journal are distinct records, but successful acceptance and creation of exactly one posted journal form one atomic transition and complete outcome. | Accepted | Separates intent from immutable financial fact without permitting an accepted-without-journal state. | #3, envelopes in #8 |
| `DOM-D006` | Posted journals have one permanent state, `posted`; correction state is derived from linked journals. | Accepted | Prevents history mutation and ambiguous status. | #3 |
| `DOM-D007` | Every journal is balanced in one book's applicable functional context. | Accepted | Creates a deterministic measurement and isolation boundary. | Exact semantics in #5 and #15 |
| `DOM-D008` | Effective time, accounting date, and recorded time are distinct explicit inputs. | Accepted | Supports period control and historical accountability. | Contracts in #8, persistence in #11, views in #12 |
| `DOM-D009` | Accounts and analytical dimensions use stable identity with effective-dated versions. | Accepted | Historical meaning survives renaming, reclassification, and reorganisation. | #3, canonical form in #6 |
| `DOM-D010` | Business domains retain detail and compile controlled journal proposals. | Accepted | Avoids an overgrown kernel and preserves evidence and reconciliation. | Module issues and #19 |
| `DOM-D011` | Original evidence, derived data, policy versions, and report calculations remain separate typed references. | Accepted | Prevents source substitution and silent retroactive change. | #8, #10, #12, #13 |
| `DOM-D012` | All actors use the same controlled accounting semantics. | Accepted | Actor type cannot weaken integrity. | Authority semantics in #17 |
| `DOM-D013` | An active control account fails closed unless exactly one accepted effective posting policy permits the authenticated source or an explicitly authorised and evidenced exception. | Accepted | Prevents ordinary posting authority or missing configuration from bypassing subledger reconciliation. | #3, operating workflow in #19 |

## Open questions

These questions do not block review of the stable boundary, but each MUST be resolved by its named issue before dependent implementation:

| ID | Question | Required owner |
| --- | --- | --- |
| `DOM-Q001` | What exact coefficient, scale, sign, zero, currency, instrument, valuation, rounding, and residual rules implement `KRN-005` to `KRN-008`? | #5 |
| `DOM-Q002` | What canonical identifier syntax, byte representation, digest, schema, and compatibility rules implement typed identity and references? | #6 |
| `DOM-Q003` | What exact envelopes and error registry carry proposals, queries, events, evidence references, versions, and idempotent outcomes? | #8 |
| `DOM-Q004` | How are domain and invariant claims represented as black-box vectors and profiles? | #9 |
| `DOM-Q005` | What evidence versioning, derivation, access, retention-policy, legal-hold, and disposal model owns evidence references? | #10 |
| `DOM-Q006` | What transaction, isolation, sequence, optimistic-concurrency, and recovery design persists these semantics? | #11 |
| `DOM-Q007` | What query and snapshot model reproduces previously known, as-filed, and current-corrected views? | #12 |
| `DOM-Q008` | What policy-pack contract selects effective New Zealand rules without leaking them into the kernel? | #13 |
| `DOM-Q009` | What recognition, settlement, revaluation, and functional-currency change rules apply to multiple currencies and instruments? | #15 |
| `DOM-Q010` | What principal, mandate, limit, approval, revocation, and decision contract authorises consequential transitions? | #17 |
| `DOM-Q011` | What reconciliation, control-account, period-close, exception, and reopen workflows operate these primitives? | #19 |
| `DOM-Q012` | What public contract defines reporting-entity composition, consolidation, combination, elimination, and separate views? | #30 |
| `DOM-Q013` | Is a minimum of two lines universally required for every valid posted journal once zero-value and multi-instrument semantics are accepted? | #5 with accounting-domain reviewer |

## Review record

### Accounting-domain review

On 31 July 2026, Mark Smith (`@nz365guy`) reviewed the vocabulary, invariants, scenarios, open questions, and deferred issue boundaries in his capacity as founder and sole maintainer and directed acceptance under the documented bootstrap exception. No separate person performed this review, so it is explicitly a domain self-review and not independent accounting-domain review or professional endorsement.

Independent accounting-domain review is deferred, not waived permanently. Before product implementation depends on this RFC or before production use, whichever is earlier, a suitably qualified reviewer must record scope, qualifications or relevant experience, findings, required changes, unresolved judgements, and whether the RFC remains suitable. The review must also be requested promptly when a suitably qualified independent reviewer becomes available, including an additional maintainer who is suitably qualified and was not involved in the original drafting or acceptance. Material findings reopen the affected RFC decisions through the governance process.

### Security review

Codex Security reviewed this RFC on 31 July 2026 for context substitution, cross-tenant and cross-entity lookup, direct writes, mutable history, correction cycles, duplicate acceptance, temporal confusion, evidence substitution, policy substitution, and deterministic failure behaviour. Follow-up changes make control-account policy resolution fail closed and bind successful acceptance to exactly one posted journal in one atomic, idempotent outcome. The exact idempotency namespace and authority-ordering contract remains assigned to #8 and #17 and MUST preserve these invariants.

### Maintainer review

On 31 July 2026, Mark Smith (`@nz365guy`) confirmed consistency with the charter, architecture, founding decisions, glossary, threat model, capability model, conformance direction, and human-agent model. He accepted the RFC with independent accounting-domain review explicitly assigned as the deferred gate above. This founder-led decision is recorded publicly in #3 and does not claim legal, tax, accounting, regulatory, professional, security, or production approval.

## Decision

Accepted on 31 July 2026 by Mark Smith (`@nz365guy`) through the disclosed sole-maintainer bootstrap exception. This RFC defines the canonical accounting-domain vocabulary and invariant catalogue for dependent design work. Acceptance does not authorise product implementation, satisfy the independent-review gate above, or resolve the deferred contracts and open questions. Any later material change follows the RFC process and preserves this decision record.
