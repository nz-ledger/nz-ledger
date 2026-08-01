# RFC 0003: Exact quantities, currencies, instruments, and valuations

- **Status:** Draft
- **Author:** `@nz365guy` (drafted with AI assistance, Claude, August 2026)
- **Reviewers:** None yet. This RFC requires independent human accounting-domain review and independent security review before acceptance. AI-assisted review may inform, but does not satisfy, either requirement
- **Created:** 2026-08-01
- **Related issues:** #5. Depends on accepted RFC 0001 and founding decisions FD-006, FD-008, FD-009, and FD-010. Bounded by #6, #8, #9, #13, #15, and #16

## Problem and outcome

RFC 0001 defines the canonical accounting domain and its invariants, but deliberately defers
every numeric detail. `KRN-005` to `KRN-008` require exact effects, exact balance, exact
values, and one functional measurement context, while explicitly making the meaning of
"exact" subject to a later contract. `DOM-Q001` records that gap, and `DOM-Q013` records an
unresolved question about journal line counts that depends on it.

Until that contract exists, no dependent design can proceed honestly. Canonical serialisation
(#6) cannot fix bytes for a value whose form is undefined. Command and event envelopes (#8)
cannot type a field whose bounds are unknown. Conformance vectors (#9) cannot assert
equivalence without a comparison rule. Multicurrency recognition and revaluation (#15) and
digital-instrument accounting (#16) both assume a valuation model that has not been written.
`ROADMAP.md` therefore places this contract on the Phase 1 critical path.

This RFC defines that contract. Its intended observable outcome is that two independent
implementations, given the same explicit inputs, agree exactly on whether a value is valid,
whether two values are equal, whether a journal balances, what a valuation derives, and how an
allocation splits, without sharing a language, a numeric library, a locale, or a storage
engine.

The words **MUST**, **MUST NOT**, **SHOULD**, and **MAY** are normative in this RFC. Stable
`EXV-*` identifiers are normative. Example field names illustrate semantics and do not
pre-empt the language-neutral schemas in #6.

## Scope and non-goals

### In scope

- the exact value model, its coefficient and scale semantics, and its bounds;
- the language-neutral lexical form of a public financial value;
- semantic value classes and the bound that applies to each;
- sign, zero, comparison, ordering, and aggregation rules;
- permitted arithmetic and the conditions under which rounding may occur;
- named rounding modes with exact definitions;
- currency and financial-instrument identity, metadata, and effective-dated versions;
- functional valuation, quotation direction, and rate provenance;
- allocation, residual ownership, and the normative split algorithm used above the kernel;
- exact-value invariants refining `KRN-005` to `KRN-008`;
- boundary and failure vectors; and
- conformance requirements, decisions, and deferred questions with named owners.

### Non-goals

This RFC does not define:

- canonical bytes, digests, identifier syntax, schemas, or compatibility rules, which belong
  to #6;
- command, query, event, evidence, or error envelopes, or the registration of the failure
  names used below, which belong to #8;
- the vector file format or conformance runner, which belong to #9;
- policy-specific rounding, tax calculation, or New Zealand rules of any kind, which belong to
  #13 and later policy work;
- multicurrency recognition, settlement, revaluation, or functional-currency change, which
  belong to #15;
- custody, wallet, exchange, transfer, or any regulated digital-asset service boundary, which
  belongs to #16 and remains outside the kernel;
- reconciliation, close, or exception workflow, which belongs to #19;
- implementation code, numeric library selection, database column types, or storage layout;
- any interpretation of a New Zealand statutory or accounting requirement; or
- a claim of legal, tax, accounting, regulatory, professional, security, or production
  approval.

## Source boundary

Current sources were researched as of August 2026 to test, not dictate, the contract. No
source below is converted into an unreviewed universal accounting rule, and each is recorded
in `../SOURCE_REGISTER.md` with its access date.

- **ISO 4217** remains the international standard for currency codes, supplying an alpha-3
  code, a numeric code, and a minor-unit relationship for each active currency. Its published
  code list continues to change as currencies are added, redenominated, and withdrawn. The
  contract therefore treats a currency's scale as declared, effective-dated data rather than a
  constant compiled into an implementation.
- **ISO 24165-1:2025** and **ISO 24165-2:2025** (Digital Token Identifier, second edition,
  published May and June 2025) define registration, assignment, structure, and data elements
  for a registry-assigned identifier for digital tokens. Adopting an existing registry-backed
  identifier scheme is preferred to inventing a project-specific token namespace, and it keeps
  FD-010 workable without asserting anything about custody or regulated services. Only
  catalogue metadata for both parts has been confirmed; the standard text is paywalled and has
  not been read, so the `dti` scheme in this draft depends on the identifier existing and
  being registry-assigned, not on any structural detail of the standard.
- **IEEE 754-2019** remains the current floating-point standard. Its `decimal128` interchange
  format carries 34 significant digits. This RFC uses that figure only as a sizing reference
  for a global digit ceiling, so that a conforming implementation may be backed by a decimal
  interchange format, an arbitrary-precision decimal type, or a scaled integer without
  changing observable behaviour. Binary floating point remains prohibited for public financial
  values under FD-008 and `KRN-007`.
- **ISO 20022** amount conventions in wide financial-messaging use are reported to permit only
  the full stop as a decimal separator, to forbid digit grouping, and to constrain amounts by
  a total and fraction digit count with per-currency decimal limits taken from ISO 4217. This
  corroborates the lexical direction already locked by FD-008, but it is a corroboration only:
  the primary ISO 20022 specification has not been read directly, the register row records
  that, and nothing in this contract depends on the claim being exact.
- **RFC 8785 (JSON Canonicalization Scheme)** is already the adopted canonicalisation
  baseline. Its known weakness, IEEE 754 number canonicalisation, does not reach this contract
  because every public financial value defined here is a string, never a JSON number.
- **NZ IAS 21** and the XRB framework material already in the register inform the separation
  of functional measurement from presentation. Their applicability is a policy and reporting
  decision under #13, not a kernel rule.

## The exact value model

### Representation

A financial value is a pair:

- `coefficient`: a signed integer with no bound other than the class bounds below; and
- `scale`: a non-negative integer.

The value is `coefficient x 10^-scale`. This satisfies FD-008 directly. An implementation MAY
store the pair in any equivalent form, provided every observable result defined by this RFC is
identical.

Scale is significant and MUST be preserved. A value supplied as `"10.00"` retains scale 2 and
is not silently normalised to `"10"`. Two consequences follow, and the contract keeps them
strictly separate:

- **Numeric equality** compares mathematical value and ignores scale. `"10.00"` and `"10.0"`
  are numerically equal.
- **Canonical-form equality** compares the exact string. `"10.00"` and `"10.0"` are not
  form-equal.

Balance evaluation, comparison, ordering, and aggregation use numeric equality. Digest,
signature, and canonical-byte comparison use canonical-form equality and are governed by #6.
Confusing the two is a defect, and the invariant catalogue names both.

### Lexical form

A public financial value is a decimal string. Its grammar, in ABNF:

```abnf
decimal-value = [ "-" ] int-part [ "." frac-part ]
int-part      = "0" / ( non-zero-digit *DIGIT )
frac-part     = 1*DIGIT
non-zero-digit = %x31-39   ; 1 to 9
DIGIT          = %x30-39   ; 0 to 9
```

The following MUST be rejected, without attempting recovery:

- a leading `+`;
- exponent notation in any form, such as `1e3` or `1E-2`;
- digit grouping, thousands separators, or any separator other than a single full stop;
- a comma decimal separator;
- leading or trailing whitespace, and any internal whitespace;
- a missing digit on either side of the point, such as `.5` or `5.`;
- more than one leading zero in the integer part, such as `007`;
- negative zero in any form, such as `-0` or `-0.00`;
- `NaN`, `Infinity`, `-Infinity`, or any other non-numeric token;
- any digit outside ASCII `0` to `9`, including Unicode decimal-digit lookalikes, full-width
  forms, and Arabic-Indic digits; and
- a JSON number of any kind, including an unquoted integer.

Trailing zeros in the fraction part are permitted and significant, because they carry the
declared scale.

A parser MUST apply a pre-parse length guard and reject any literal longer than 40 octets
before interpreting it. The longest value permitted by the class bounds below is shorter than
that guard, so the guard never rejects a valid value; it bounds the work an untrusted input
can cause.

### Semantic value classes and bounds

Every public financial value carries an explicit semantic class. The class is part of the
contract, not an annotation, and each class has its own bounds. Bounds are expressed as the
maximum number of digits before the point (`int-digits`) and the maximum scale, so that both
are directly testable. Every class sits within a global ceiling of 34 coefficient digits, so
one numeric type suffices in any implementation language.

| Class | Meaning | Max int-digits | Max scale | Sign | Notes |
| --- | --- | --- | --- | --- | --- |
| `functional-amount` | A financial effect measured in the book's applicable functional instrument. | 24 | 10 | Non-negative on a journal line; direction is carried by `side`. | Scale MUST equal the functional instrument's declared scale for the accounting date. |
| `instrument-quantity` | A quantity of a transaction instrument, fiat or digital. | 16 | 18 | Non-negative on a journal line. | Scale 18 covers the dominant digital-token precision convention without unbounded growth. |
| `rate` | A conversion rate between two instruments. | 14 | 20 | Strictly positive. | Direction is explicit; see valuation below. |
| `ratio` | An allocation weight or apportionment factor. | 14 | 20 | Non-negative. | Weights are not required to sum to one; normalisation is by their supplied sum. |

Bound rationale, recorded so a reviewer can challenge the numbers rather than the shape:

- 24 integer digits for a functional amount is far beyond any plausible national aggregate in
  any unit, while still permitting historical hyperinflated currency records to be represented
  rather than rejected.
- 10 fractional digits for a functional amount is well above the two decimals used by most
  ISO 4217 currencies and above the fractional precision used in the ISO 20022 amount
  conventions, leaving room for high-precision functional units without permitting a
  rate-sized scale to appear on a money amount.
- 16 integer digits and 18 fractional digits for an instrument quantity accommodate both very
  large token supplies and the eighteen-decimal precision convention common to digital tokens.
- 20 fractional digits for rates and ratios accommodate high-precision quotations and exact
  apportionment factors.

A value that satisfies the grammar but breaches its class bound MUST be rejected with a range
failure, not truncated, rounded, or clamped.

### Sign and zero

Direction on a journal line is carried by `side`, `debit` or `credit`, under `KRN-004`. A
journal line value MUST NOT be negative. Negative values remain meaningful outside a line, for
example in a report projection or an allocation total, and the class table states where a
negative value is permitted at all.

Zero is written `"0"` at scale 0, or with the declared scale, for example `"0.00"`. Negative
zero does not exist in this contract. A value of zero is a legitimate financial statement only
under the line-effect rule below.

### Comparison, ordering, and aggregation

- Numeric comparison aligns two values to the greater of their scales by exact integer scaling
  of the coefficient, then compares coefficients. No rounding occurs.
- Ordering is total over a class and follows mathematical value. Equal values with different
  scales tie, and any tie-break required for a deterministic sequence MUST use an explicit
  supplied order, never scale, insertion order, or a locale collation.
- Summation is exact. The result's scale is the greatest scale among the addends. A sum MUST
  NOT depend on addend order, grouping, locale, or platform, satisfying `KRN-023`.
- Balance evaluation under `KRN-006` uses numeric equality with no tolerance whatsoever. There
  is no posting-time epsilon, and none may be introduced by an implementation, a profile, or a
  policy pack.

### Arithmetic and when rounding may occur

- Addition, subtraction, comparison, and negation are exact and MUST NOT round.
- Multiplication and division appear only in valuation and allocation. Every such operation
  MUST name both a target scale and a rounding mode explicitly. There is no default rounding
  mode anywhere in this contract, and an operation that omits either MUST be rejected rather
  than defaulted.
- The kernel itself performs no derivation. It re-performs a published derivation to verify an
  explicitly supplied result, which is a comparison, not an inference. This preserves FD-006
  and `KRN-022` while still making derivations testable.

### Rounding modes

Every mode below is defined on the discarded fraction relative to the target scale. A
conforming implementation MUST support all of them and MUST NOT add an unnamed mode to a
public contract.

| Mode | Definition |
| --- | --- |
| `half-even` | Round to nearest; on an exact half, round to the neighbour whose last retained digit is even. |
| `half-up` | Round to nearest; on an exact half, round away from zero. |
| `half-down` | Round to nearest; on an exact half, round toward zero. |
| `ceiling` | Round toward positive infinity. |
| `floor` | Round toward negative infinity. |
| `up` | Round away from zero. |
| `down` | Round toward zero, discarding the remaining fraction. |
| `exact` | Assert that no rounding is required. If any non-zero fraction would be discarded, the operation fails rather than producing a value. |

`exact` exists so that "this must not round" is expressible in a contract and testable in a
vector, rather than being an unenforced comment.

## Currency and instrument model

### Instrument identity

An instrument is identified by a typed pair, never by a bare code:

- `scheme`: the identifier namespace; and
- `code`: the value within that namespace.

Three schemes are defined. Others require an accepted change to this contract.

| Scheme | Code content | Use |
| --- | --- | --- |
| `iso4217` | The ISO 4217 alpha-3 code, with the numeric code retained alongside it in instrument metadata. | Fiat currencies and the ISO 4217 non-national codes for funds and metals. |
| `dti` | An ISO 24165 Digital Token Identifier. | Digital tokens, including stable-value instruments. |
| `private` | An issuer-scoped code within an explicit tenant-owned issuer namespace. | Entity-defined units that no external registry assigns, such as a unit of account for an internal measure. |

A bare code such as `NZD` or a bare token symbol is never sufficient identity, for the same
reason `KRN-018` rejects a bare external identifier. Symbols collide across registries and
across time; scheme-qualified codes do not.

### Instrument metadata and versions

Instrument metadata is effective-dated and versioned, and the version applicable to the
journal accounting date is the one used, consistent with `KRN-009`:

- `kind`: `fiat`, `digital`, or `unit`;
- `declared-scale`: the number of fractional digits the instrument is recorded to;
- `issuer` or `jurisdiction` where applicable, with the reference typed rather than free text;
- the ISO 4217 numeric code for `iso4217` instruments; and
- the effective interval and recorded version of the metadata itself.

Redenomination, a minor-unit change, a withdrawal, or a replacement creates a new
effective-dated version, and where identity itself changes, a new instrument with an explicit
succession link. It MUST NOT rewrite the scale, value, or meaning of any posted record, which
follows directly from `KRN-027`. A historical record remains readable in the metadata version
that applied when it was posted.

### Digital and stable-value instruments

Under FD-010, a stable-value instrument is a financial instrument identified in its own right.
It MUST NOT be recorded as, substituted for, or silently treated as the fiat currency it
references. A peg, a redemption promise, or a market convention is a valuation input carrying
its own provenance, never an identity claim, and never a reason to omit a valuation record.

This RFC records and values instruments only. Custody, key material, transfer, exchange, and
every regulated service boundary remain outside the kernel and outside this contract, under
FD-010 and issue #16.

## Functional valuation and rate provenance

Every journal line carries an exact `functional-amount` in the book's applicable functional
instrument, satisfying `KRN-008`. A line MAY additionally carry an `instrument-quantity` in a
different transaction instrument. When it does, it MUST carry a complete valuation record.

A valuation record contains, all explicit:

- `source-instrument` and `target-instrument`, each a typed instrument identifier;
- `rate`, in the `rate` class;
- `quotation-direction`, stated explicitly as either `target-per-source` or
  `source-per-target`;
- `rate-source`: a typed identity of the publisher or source system, with its version;
- `rate-observed-at`: the observation time of the rate, which is distinct from the journal's
  effective time, accounting date, and recorded time under `KRN-016`;
- `purpose`: what the valuation is for, so that a transaction-date valuation is never reused
  as though it were a revaluation;
- `rounding-mode` and `target-scale` used to derive the functional amount; and
- `policy-version` reference where policy was material to the choice of rate, source, or
  rounding, under `KRN-029`.

The derivation is published so it can be re-performed:

- for `target-per-source`, `functional = round(quantity x rate, target-scale, mode)`;
- for `source-per-target`, `functional = round(quantity / rate, target-scale, mode)`.

The quotation direction is never inferred, and a rate is never inverted by convention. An
inverted quotation with the wrong direction label produces a plausible but wrong number, which
is exactly the class of error a contract should make impossible rather than unlikely.

The kernel does not compute the functional amount. The functional amount is supplied
explicitly, and the kernel re-performs the published derivation and rejects a mismatch. This
keeps FD-006 intact while making the provenance requirement in issue #5 mechanically testable
rather than declaratory.

Where a rate source publishes a rate whose scale exceeds the `rate` class bound, the supplied
rate MUST be reduced to a permitted scale by an explicit, recorded rounding operation before
use. The reduction is part of the supplied input and its mode and scale are recorded; it is
not something the kernel may perform silently.

## Allocation and residual ownership

### Ownership

The kernel never allocates, apportions, rounds, or normalises a supplied value. It validates
exactness and rejects anything that does not balance, under `KRN-006` and `KRN-022`. Residual
ownership therefore sits with the compiler above the kernel: the subledger, module, or
integration that turns a business event into a journal proposal.

That would leave residuals undefined across implementations, which is not acceptable for a
project whose point is reproducibility. This RFC therefore publishes a normative allocation
algorithm that any compiler MUST follow when it claims conformance, with golden vectors under
#9. The kernel remains free of it.

### Normative allocation

Inputs: an exact `total` `T`; weights `w(1)` to `w(n)` in the `ratio` class; a `target-scale`
`s`; and optionally an explicitly nominated `residual-index`.

1. Reject if `n` is zero, if any weight is negative, or if the sum of weights is zero.
2. Compute each exact share `r(i) = T x w(i) / sum(w)` without rounding, retaining the exact
   value.
3. Set `base(i)` to `r(i)` rounded to scale `s` with mode `down`, that is, truncated toward
   zero.
4. Compute the residual `R = T - sum(base)`. Because weights are non-negative and truncation
   is toward zero, `R` has the same sign as `T`, and its magnitude is strictly less than `n`
   units of `10^-s`.
5. If a `residual-index` is nominated, add the whole of `R` to that entry and stop. The
   nominated entry MUST have a non-zero weight.
6. Otherwise distribute `R` one unit of `10^-s` at a time, in descending order of the
   discarded fraction magnitude `|r(i) - base(i)|`. Ties are broken by ascending supplied
   index. An entry whose weight is zero receives exactly zero and MUST NOT receive a residual
   unit.
7. Post-condition, and the testable invariant: the allocations sum to `T` exactly.

Worked example, with `T` of `"0.10"`, three equal weights, and `s` of 2: each exact share is
one thirtieth, each `base` is `"0.03"`, the residual is `"0.01"`, the discarded fractions tie,
and the first supplied index receives the unit. The result is `"0.04"`, `"0.03"`, `"0.03"`.

Required vectors include a negative total, an entry with zero weight, weights that do not sum
to one, a residual larger than one unit of `10^-s`, a single-entry allocation, and an
allocation whose nominated residual index carries a zero weight and must therefore be
rejected.

## Invariant catalogue

Each invariant is independent of transport and storage. Failure names are stable semantic
names proposed for registration in #8. These invariants refine `KRN-005` to `KRN-008` and MUST
NOT weaken any invariant accepted in RFC 0001.

| ID | Normative statement | Rationale | Valid example | Invalid example | Expected failure | Security consequence |
| --- | --- | --- | --- | --- | --- | --- |
| `EXV-001` | Every public financial value MUST be an exact coefficient and scale pair serialised as a decimal string, and MUST NOT be a binary or decimal floating-point number in any public contract. | FD-008 and `KRN-007` require exactness that binary floating point cannot provide. | `"1234.56"` with class and scale declared. | A JSON number `1234.56`. | `FINANCIAL_VALUE_INVALID` | Precision loss and parser reinterpretation change financial effect. |
| `EXV-002` | A financial value MUST match the published decimal-string grammar exactly. | One grammar makes cross-language parsing equivalent. | `"-0.01"`. | `"+0.01"` or `".01"`. | `VALUE_LEXICAL_INVALID` | Parser differentials permit two implementations to read one document differently. |
| `EXV-003` | Exponent notation, digit grouping, non-ASCII digits, alternative separators, and surrounding whitespace MUST be rejected without recovery. | Lenient parsing is a substitution vector, not a convenience. | `"1000000.00"`. | `"1e6"`, `"1,000.00"`, or a full-width digit sequence. | `VALUE_LEXICAL_INVALID` | Homoglyph and format confusion alter a recorded amount. |
| `EXV-004` | Negative zero MUST be rejected, and zero MUST be written as `"0"` at its declared scale. | Two representations of zero break canonical form and equality reasoning. | `"0.00"`. | `"-0.00"`. | `VALUE_SIGN_INVALID` | Divergent digests and inconsistent comparison results. |
| `EXV-005` | Every public financial value MUST declare a semantic class, and MUST satisfy that class's integer-digit and scale bounds. Breach is rejected, never truncated, rounded, or clamped. | Bounds are only a control if they fail closed. | An `instrument-quantity` with scale 18. | A `functional-amount` with scale 18. | `VALUE_OUT_OF_RANGE` | Silent truncation creates value from nothing or destroys it. |
| `EXV-006` | A parser MUST apply a pre-parse length guard and reject any literal longer than 40 octets before interpretation. | Bounds the work untrusted input can cause. | A 12-character amount. | A one-megabyte digit string. | `VALUE_LEXICAL_INVALID` | Resource exhaustion through hostile input. |
| `EXV-007` | A line's `functional-amount` scale MUST equal the declared scale of the book's functional instrument version applicable to the journal accounting date. | Ties measurement to effective-dated instrument metadata rather than to code. | A two-decimal amount in a two-decimal functional instrument. | A three-decimal amount in the same context. | `SCALE_MISMATCH` | Inconsistent scale hides sub-unit drift across a book. |
| `EXV-008` | A journal line value MUST NOT be negative; direction is carried solely by `side`. | Preserves `KRN-004` and removes sign and side ambiguity. | `side` `credit` with `"10.00"`. | `side` `credit` with `"-10.00"`. | `VALUE_SIGN_INVALID` | A double negation reverses financial effect undetected. |
| `EXV-009` | Numeric equality MUST ignore scale, canonical-form equality MUST compare the exact string, and balance evaluation MUST use numeric equality with no tolerance. | Both comparisons are needed, and conflating them is a defect. | `"10.00"` balances against `"10.0"`. | A posting accepted because the difference is under an epsilon. | `JOURNAL_UNBALANCED` | Any tolerance is an exploitable rounding channel. |
| `EXV-010` | Addition, subtraction, negation, and comparison MUST be exact and MUST NOT round. | Aggregation is where silent rounding hides. | Summing 1000 two-decimal values exactly. | Rounding each partial sum to two decimals. | `ROUNDING_FORBIDDEN` | Accumulated rounding error becomes unattributable loss. |
| `EXV-011` | Every multiplication or division MUST name a target scale and a rounding mode explicitly. There is no default mode. | An implied default is an unreviewed accounting decision. | A valuation naming `half-even` and scale 2. | A valuation naming neither. | `ROUNDING_MODE_REQUIRED` | Divergent defaults make two implementations disagree on money. |
| `EXV-012` | An instrument MUST be identified by a typed scheme and code. A bare code or symbol MUST NOT be accepted as identity. | Symbols collide across registries and over time, as `KRN-018` already recognises for external identifiers. | `iso4217:NZD`. | `NZD` alone, or a bare token symbol. | `INSTRUMENT_IDENTIFIER_INVALID` | Instrument substitution across registries. |
| `EXV-013` | Instrument metadata MUST be effective-dated, and the version applicable to the journal accounting date MUST be the one used. | Currency and token attributes change; history must not. | A posting uses the metadata version effective on its accounting date. | A posting uses today's metadata for a prior year. | `INSTRUMENT_VERSION_INVALID` | Retrospective reinterpretation of posted amounts. |
| `EXV-014` | Redenomination, minor-unit change, withdrawal, or replacement MUST create a new effective-dated version or a new instrument with a succession link, and MUST NOT alter any posted value. | Follows `KRN-027` for the instrument dimension. | A scale change applies prospectively. | An existing posted amount is rescaled in place. | `INSTRUMENT_HISTORY_IMMUTABLE` | Silent rewriting of historical financial records. |
| `EXV-015` | A stable-value or digital instrument MUST be recorded as its own instrument and MUST NOT be substituted for the fiat currency it references. A peg is a valuation input, never an identity. | FD-010 separates issuer, redemption, network, and risk from fiat identity. | A stable-value token valued in NZD with full provenance. | The same token posted directly as NZD. | `INSTRUMENT_SUBSTITUTION_FORBIDDEN` | Unrecognised issuer and redemption risk presented as cash. |
| `EXV-016` | A line carrying an instrument quantity in a non-functional instrument MUST carry a complete valuation record with every named element present. | Provenance is only testable when completeness is defined. | A valuation naming rate, direction, source, version, observation time, purpose, mode, and scale. | A valuation naming only a rate. | `VALUATION_PROVENANCE_INCOMPLETE` | Unattributable or fabricated valuation. |
| `EXV-017` | Quotation direction MUST be explicit, and a rate MUST NOT be inverted by convention or inferred from instrument order. | An inverted rate yields a plausible wrong number. | `source-per-target` stated explicitly. | Direction inferred from the field order. | `RATE_DIRECTION_INVALID` | Systematic mis-valuation that reconciles internally. |
| `EXV-018` | A rate MUST be strictly positive. | A zero or negative conversion rate has no valuation meaning in this contract. | `"0.5942"`. | `"0"` or `"-0.5942"`. | `RATE_INVALID` | Division by zero, or sign inversion of a financial effect. |
| `EXV-019` | The supplied functional amount MUST equal the result of re-performing the published derivation from the supplied quantity, rate, direction, target scale, and mode. | Makes a supplied derived value verifiable without the kernel deriving it. | Supplied amount matches re-performance exactly. | Supplied amount differs by one minor unit. | `VALUATION_MISMATCH` | Hand-adjusted valuations that no control detects. |
| `EXV-020` | The kernel MUST NOT derive, allocate, apportion, round, or normalise any supplied financial value. | FD-006 and `KRN-022` require explicit inputs and no inference. | The kernel rejects an unbalanced proposal. | The kernel rounds a line to make a journal balance. | `DERIVATION_FORBIDDEN` | A kernel that fixes inputs makes its own errors invisible. |
| `EXV-021` | A conforming allocation MUST produce parts that sum to the total exactly, following the published algorithm and tie-break order. | Determinism across compilers is the entire point of publishing the algorithm. | Three parts of `"0.10"` sum to `"0.10"`. | Parts sum to `"0.09"` with a lost unit. | `ALLOCATION_RESIDUAL_UNRESOLVED` | Recurring lost or duplicated minor units. |
| `EXV-022` | An allocation entry with zero weight MUST receive exactly zero and MUST NOT receive a residual unit, and a nominated residual index MUST have a non-zero weight. | Prevents a residual from landing where no share was allocated. | A zero-weight entry receives `"0.00"`. | A zero-weight entry receives the residual. | `ALLOCATION_WEIGHT_INVALID` | Misallocation to an unintended account or dimension. |
| `EXV-023` | A posted line MUST carry at least one non-zero exact value. A zero `functional-amount` is permitted only where a non-zero `instrument-quantity` and a complete valuation record are present. | Refines `KRN-005` with the exact semantics it defers to this contract. | A quantity-bearing line with a zero functional valuation and full provenance. | A line where the functional amount and every quantity are zero. | `LINE_EFFECT_EMPTY` | Meaningless records conceal replay or manipulation. |
| `EXV-024` | A posted journal MUST contain at least two lines. A line with a zero functional amount does not remove the need for a balancing counterpart. | Proposed resolution of `DOM-Q013`; see below. | A two-line balanced journal. | A single-line journal whose functional effect is zero. | `JOURNAL_LINE_COUNT_INVALID` | A second valid journal shape multiplies the paths every control must cover. |
| `EXV-025` | Aggregation results MUST NOT depend on input order, grouping, locale, or platform. | `KRN-023` requires reproducibility as a financial control. | The same total from any input order. | A total that changes with locale collation. | `NONDETERMINISTIC_RESULT` | Results that cannot be audited or independently reproduced. |

## Proposed resolution of `DOM-Q013`

RFC 0001 leaves open whether two lines are universally required once zero-value and
multi-instrument semantics exist. This RFC proposes retaining the two-line minimum, recorded
as `EXV-024`.

The argument for the minimum: a journal balances when total debits equal total credits
numerically. A single line can satisfy that only when its functional amount is zero, which
means the sole shape a single-line journal could take is a quantity-only memorandum record.
Permitting it would create a second valid journal shape that every invariant, vector,
projection, reversal rule, and export check must then handle, in exchange for a record that
can be expressed today as two lines against a designated quantity-tracking account pair. The
cost falls on every implementation forever; the benefit is narrow.

The argument against, recorded honestly for the reviewer: quantity-only tracking of digital
instruments and non-financial units is a real requirement, and forcing an artificial
counterpart line can produce accounts whose sole purpose is to satisfy a structural rule.

This is an accounting-domain judgement, not a technical one. `EXV-024` is therefore recorded
as **proposed**, and `DOM-Q013` remains open until an independent accounting-domain reviewer
confirms or rejects it. Dependent work in #6, #8, and #9 may build against the proposal, and
MUST record that it did so.

## Boundary and failure vectors

Issue #5 names boundary and failure vectors as a required artefact. The families below are
required; #9 defines their file format and runner.

**Lexical rejection:** leading `+`; bare `.5` and `5.`; `007`; `-0` and `-0.00`; `1e6`;
`1,000.00`; comma separator; leading, trailing, and internal whitespace; `NaN` and
`Infinity`; full-width and Arabic-Indic digits; an unquoted JSON number; an empty string; a
string of only `-`.

**Bounds:** a value at each class's maximum integer digits and maximum scale; a value one
digit beyond each; a value at the 40-octet guard and one octet beyond; a `functional-amount`
carrying a rate-sized scale; an `instrument-quantity` at scale 18 with 16 integer digits.

**Sign and zero:** a negative line value; `"0"`, `"0.00"`, and their numeric equality; a zero
functional amount with and without a non-zero quantity.

**Comparison:** `"10.00"` against `"10.0"` under both equality rules; ordering of equal values
at different scales; a thousand-addend sum evaluated in several orders; a sum whose result
scale is the greatest addend scale.

**Balance:** an exactly balanced journal; a journal out by one minor unit; a journal that would
balance only under a tolerance, which MUST be rejected.

**Rounding:** every mode at an exact half, above a half, and below a half, in both signs;
`exact` where no fraction is discarded and where one is.

**Valuation:** both quotation directions; a direction label inverted against its rate; an
incomplete valuation record missing each named element in turn; a supplied functional amount
one minor unit away from re-performance; a rate at scale 20 and one beyond; a zero and a
negative rate.

**Instruments:** a bare code; an unknown scheme; a metadata version not effective on the
accounting date; a redenomination boundary crossing a posted record; a stable-value instrument
posted as its referenced fiat currency.

**Allocation:** the `"0.10"` over three equal weights case; a negative total; a zero-weight
entry; weights that do not sum to one; a residual larger than one unit; a single entry; a
nominated residual index with zero weight; an allocation of a total whose scale exceeds the
target scale.

## Conformance requirements

`../CONFORMANCE.md` gains exact-value, valuation, and allocation vector families. A
conformance claim covering this contract MUST state which value classes and bounds the
implementation supports, and MUST NOT claim conformance while widening any bound. A profile
MAY narrow a bound, and MUST declare that it has done so, because a narrower bound changes
which documents an implementation accepts.

Cross-language equivalence is the test that matters: the same vectors run against
implementations backed by a decimal interchange format, an arbitrary-precision decimal type,
and a scaled integer MUST produce identical results, including identical failures.

## Alternatives

### Integer minor units only, amounts as whole cents

Rejected. It works for a single two-decimal currency and fails everywhere else: rates and
allocation factors need far higher scale, instrument quantities need eighteen decimals,
currencies differ in minor unit, and a redenomination changes what a unit means. It also
hides scale inside a convention rather than declaring it, which is the opposite of what a
public contract should do.

### Adopt IEEE 754 `decimal128` as the public contract type

Rejected. It binds public contracts to a machine interchange format, reintroduces
floating-point parsing at every boundary, and makes a wire value's meaning depend on the
reader's numeric library. Using its 34-digit precision as a sizing reference captures the
practical benefit, broad language support, without the coupling.

### Unbounded arbitrary precision with no class bounds

Rejected. Unbounded values give an untrusted input unbounded cost, provide no cross-language
equivalence guarantee, and make "does this implementation conform" unanswerable. Bounds that
fail closed are a control; unbounded values are an unstated assumption.

### Signed amounts without a `side` field

Rejected under `KRN-004`. Sign and side encode the same information in two places, and any
disagreement between them silently reverses a financial effect.

### Kernel-side allocation and residual assignment

Rejected under FD-006 and `KRN-022`. Allocation is derivation, and derivation inside the
kernel means the kernel can produce a value nobody supplied. Publishing a normative algorithm
for compilers above the kernel achieves determinism without moving derivation inside the
boundary.

### Rational values as numerator and denominator

Rejected. Rationals are exact and appealing for rates, but they have no canonical form without
normalisation rules, they are not legible to a human auditor reading an export, and they
displace the rounding decision to display time, where it is least controlled.

### Do nothing and let #6 fix numeric form alongside canonical bytes

Rejected. Canonical bytes describe how to serialise a value whose semantics already exist.
Deciding bounds, classes, valuation, and residual ownership inside a serialisation issue would
bury accounting decisions in an encoding document, where a domain reviewer would not look for
them.

## Consequences

### Accounting integrity

- `KRN-005` to `KRN-008` become mechanically testable rather than aspirational.
- Balance has no tolerance anywhere, at any layer, under any profile.
- Rounding becomes an explicit, named, recorded decision at every point it can occur.
- Residual ownership sits with the compiler, and two conforming compilers split identically.
- A valuation can be re-performed by anyone holding the export, which supports the
  re-performance goal in draft RFC 0002.

### Security and privacy

- Lenient parsing, homoglyph digits, and format confusion are rejected rather than recovered.
- A pre-parse length guard bounds hostile input cost.
- Instrument substitution requires defeating a typed, scheme-qualified identity.
- Every arithmetic path that could hide value has a named failure and a required vector.
- No element of this contract carries personal or financial data; the values described here
  are structural.

### Authority and evidence

- A valuation names its source, version, and observation time, so a rate is attributable.
- Policy-material rounding and source selection carry a policy-version reference under
  `KRN-029`.
- Actor type has no effect on any rule here, consistent with FD-014.

### New Zealand policy

- Nothing in this contract interprets a New Zealand rule. GST rounding, tax calculation, and
  reporting presentation remain effective-dated policy-pack concerns under #13.
- The contract is deliberately capable of expressing policy rounding rules without containing
  any, which is what FD-011 requires.

### Compatibility and operations

- #6 receives a value form to canonicalise and digest; the decimal-string values defined here
  are strings under the RFC 8785 baseline, never JSON numbers.
- #8 receives typed classes and a set of failure names to register.
- #9 receives the vector families listed above.
- #15 and #16 receive an instrument and valuation model to build recognition, revaluation, and
  digital-instrument accounting on.
- FD-018 signing and the FD-019 hash chain gain deterministic value bytes to sign and chain,
  once #6 fixes the encoding.
- Widening a bound later is a breaking change for readers; narrowing one is a breaking change
  for writers. Both follow the compatibility rules accepted in #6.

## Validation

Acceptance and subsequent delivery use the following gates:

1. Every value class has explicit bounds, sign rules, and comparison behaviour.
2. Every `EXV-*` invariant is judged mechanically testable in principle.
3. The boundary and failure vector families cover lexical, bounds, sign, comparison, balance,
   rounding, valuation, instrument, and allocation cases.
4. During sole-maintainer bootstrap, the maintainer MAY accept this documentation-only RFC
   after recording a self-review and disclosing that the review is not independent. An
   independent accounting-domain reviewer MUST record whether the value classes, bounds,
   rounding modes, valuation provenance, allocation rule, and the `DOM-Q013` proposal are
   suitable before product implementation depends on this RFC or before production use,
   whichever is earlier. That review is tracked as review debt in `../../GOVERNANCE.md` and
   issue #37.
5. A security reviewer records whether parsing, bounds, substitution, rounding, valuation, and
   resource-exhaustion threats are addressed.
6. Maintainer review confirms consistency with RFC 0001, the charter, architecture, founding
   decisions, glossary, threat model, conformance direction, and source register.
7. #6, #8, #9, #13, #15, and #16 confirm or refine the deferred details without weakening
   these invariants.
8. Documentation links and source-register entries are current, `git diff --check` passes,
   required repository checks pass, and the DCO sign-off is present.

## Decision register

| ID | Decision | Status | Rationale | Owner or follow-up |
| --- | --- | --- | --- | --- |
| `EXV-D001` | A financial value is an exact coefficient and scale pair, serialised as a decimal string, with scale significant and preserved. | Proposed | Satisfies FD-008 without binding contracts to a machine numeric format. | #5 |
| `EXV-D002` | Numeric equality and canonical-form equality are separate, named comparisons with separate uses. | Proposed | Balance needs value equality; digests need form equality. | #5, form rules in #6 |
| `EXV-D003` | Bounds are per semantic value class, within a global ceiling of 34 coefficient digits. | Proposed | A money amount and a rate have different risks and should not share one permissive bound. | #5 |
| `EXV-D004` | Journal line values are non-negative; direction is carried solely by `side`. | Proposed | Preserves `KRN-004` and removes a silent sign-reversal path. | #5 |
| `EXV-D005` | Addition and subtraction are exact; rounding occurs only in explicitly parameterised multiplication and division. | Proposed | Silent rounding in aggregation is the classic unattributable loss. | #5 |
| `EXV-D006` | There is no default rounding mode; every rounding operation names its mode and target scale. | Proposed | A default is an unreviewed accounting decision made by an implementer. | #5 |
| `EXV-D007` | Instruments are identified by a typed scheme and code, using ISO 4217 for fiat, ISO 24165 DTI for digital tokens, and an issuer-scoped private scheme for entity-defined units. | Proposed | Reuses registry-backed identity rather than inventing a namespace. | #5, digital-instrument boundary in #16 |
| `EXV-D008` | Instrument metadata is effective-dated, and redenomination never rewrites a posted value. | Proposed | Extends `KRN-027` to the instrument dimension. | #5, #15 |
| `EXV-D009` | A non-functional instrument quantity requires a complete valuation record, and the kernel re-performs rather than derives the functional amount. | Proposed | Makes provenance testable while preserving FD-006. | #5, revaluation in #15 |
| `EXV-D010` | The kernel never allocates or rounds; a normative allocation algorithm is published for compilers above the kernel. | Proposed | Determinism without moving derivation inside the deterministic boundary. | #5, workflow in #19 |
| `EXV-D011` | The two-line minimum is retained, recorded as a proposal pending independent accounting-domain review. | Proposed | Resolves `DOM-Q013` for dependent work without pre-empting a domain judgement. | #5 with accounting-domain reviewer |

Taken together, `EXV-D001` to `EXV-D010` are the proposed resolution of `DOM-Q001` in RFC 0001,
which asked what exact coefficient, scale, sign, zero, currency, instrument, valuation,
rounding, and residual rules implement `KRN-005` to `KRN-008`. `EXV-D011` proposes a
resolution of `DOM-Q013`, which stays open until independent accounting-domain review. Neither
question closes while this RFC is a draft.

## Open questions

These do not block review of the value model, but each MUST be resolved by its named owner
before dependent implementation:

| ID | Question | Required owner |
| --- | --- | --- |
| `EXV-Q001` | How are these decimal strings canonicalised, digested, and schema-constrained under the RFC 8785 baseline, and how is the value class carried in the schema? | #6 |
| `EXV-Q002` | How are the failure names used here registered, versioned, and carried in the error envelope? | #8 |
| `EXV-Q003` | What is the vector file format for the boundary and failure families listed here, and how are cross-language runs compared? | #9 |
| `EXV-Q004` | Which policy-pack contract expresses jurisdiction-specific rounding without leaking any rule into the kernel? | #13 |
| `EXV-Q005` | How do recognition, settlement, revaluation, and a functional-currency change use this valuation model over time? | #15 |
| `EXV-Q006` | Does the `dti` scheme need any additional metadata for stable-value instruments, and where does the custody boundary sit relative to valuation? | #16 |
| `EXV-Q007` | Is a `functional-amount` scale bound of 10 sufficient for every functional instrument a book may adopt, including non-fiat functional units? | #5 with accounting-domain reviewer |
| `EXV-Q008` | Should a profile ever be permitted to widen a class bound, given that widening is a breaking change for readers? | #6 compatibility rules |
| `EXV-Q009` | Is `DOM-Q013`, the two-line minimum, confirmed or rejected? | Independent accounting-domain reviewer, tracked in #37 |

## Review record

### Accounting-domain review

None. No accounting-domain review, independent or otherwise, has been performed on this RFC.
It is a draft.

### Security review

None yet. A security review MUST cover parsing strictness, bounds and resource exhaustion,
instrument substitution, rounding and residual channels, valuation provenance completeness,
and deterministic failure behaviour before acceptance.

### Maintainer review

None yet. Maintainer review will confirm consistency with RFC 0001, the founding decisions,
and the surrounding documents.

## Decision

Not yet decided. This RFC is a draft and MUST NOT be treated as accepted design, cited as a
settled contract, or used to justify implementation work. Acceptance is a maintainer decision
under the bootstrap exception in `../../GOVERNANCE.md`, and would create the independent
review debt already tracked in issue #37.
