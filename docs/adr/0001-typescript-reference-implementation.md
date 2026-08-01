# ADR 0001: TypeScript for the reference implementation

- **Status:** Accepted
- **Date:** 2026-08-01
- **Owners:** `@nz365guy`
- **Related issue or RFC:** Founding decision FD-004
- **Supersedes:** None

## Context

FD-004 locked TypeScript as the reference-implementation language in a single line. A
determinism-critical accounting kernel deserves a recorded alternatives analysis, even
where the conclusion stands. The platform is defined by language-neutral schemas,
canonical serialisation, and black-box conformance, so the reference language is an
implementation convenience, not a platform property. The founding maintainer builds
primarily with AI coding agents, whose tooling and training strength is currently
deepest in the TypeScript ecosystem, and the intended early adopters are business
software developers, where TypeScript adoption is broad.

## Decision

The reference implementation uses TypeScript on Node.js. Exact financial values use a
coefficient-and-scale representation built on `bigint`, or an equivalently exact design
accepted in issue #5. Public contracts serialise financial values as decimal strings.
Native floating point is never used for financial values, in any language.

## Consequences

Beneficial: fastest AI-assisted development velocity; largest contributor pool among
business-software developers; one language across kernel, contracts tooling, and future
reference services. Adverse: JavaScript number semantics make floating-point mistakes
easy, so lint rules, contract validation, and conformance vectors must actively forbid
float paths; long-running deterministic replay is slower than systems languages; the
runtime is not memory-safe by construction against native-dependency defects.
Mitigations: the conformance suite is language-neutral by design, so any implementation,
including a future systems-language kernel, can prove equivalence without renegotiating
the platform.

## Alternatives considered

**Rust.** Strongest determinism and memory-safety story and a natural fit for a kernel.
Rejected for the founding phase: materially slower AI-agent-assisted iteration for this
maintainer, smaller reviewer pool in the target community, and no platform-level gain
because conformance already makes the kernel language-swappable.

**JVM (Kotlin or Java).** Mature `BigDecimal` arithmetic and strong long-running service
characteristics. Rejected: weaker fit with the intended contributor community and no
decisive advantage over TypeScript-plus-`bigint` once floats are structurally excluded.

**Go.** Simple deployment and good tooling. Rejected: no native arbitrary-precision
decimal ergonomics comparable to `bigint`-based coefficient-and-scale designs, and the
same conformance argument applies.

## Verification

Conformance golden vectors, canonical-serialisation byte tests, and determinism tests
(KRN-023) must pass identically for the TypeScript reference implementation and for any
alternative-language implementation claiming equivalence. Lint and contract validation
reject floating-point financial values.

## Revisit triggers

Conformance evidence shows TypeScript blocks a core property (per FD-004), or a
maintained alternative-language implementation passes the full conformance suite and
offers materially stronger integrity or performance characteristics.
