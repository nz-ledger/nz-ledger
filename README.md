# NZ Ledger

**Open financial infrastructure for New Zealand business software.**

> **Status: Founding and design phase. No production software has been released. Do not use this repository for financial reporting, tax filing, payments, custody, or production accounting.**

NZ Ledger is an independent Apache 2.0 open-source project. Its mission is to build trusted, open-source, human-and-agent-first financial infrastructure for New Zealand business software.

## What the project is

NZ Ledger is intended to provide a deterministic financial and accounting foundation for complete business systems. It is New Zealand-first, evidence-backed, extensible through stable public contracts, and designed for both human and software-agent operation.

The project is for developers, accounting and tax specialists, security practitioners, integration builders, and organisations that need an open financial system of record. Material capabilities may be delivered natively, by a module, or by a controlled integration. A manual journal alone does not count as support.

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

## Scope and architecture

The intended platform uses a modular monolith around a deterministic, headless accounting kernel. Evidence and business events flow through controlled subledgers and effective-dated New Zealand policy packs into immutable posted journals. PostgreSQL is the future reference transactional store, with versioned object storage for evidence and a transactional outbox for durable integrations.

The kernel will accept nondeterministic values explicitly and will not depend on user interfaces, HTTP, databases, authentication providers, cloud services, AI models, telemetry, system time, random identifiers, or external rate services. Financial values will be exact and public contracts will serialise them as decimal strings.

Humans, agents, integrations, and scheduled rules will use the same command and authority model. Confidence never grants authority. Extensions will use public command, query, event, evidence, and agent-tool contracts, without direct access to core storage or a path around financial and security controls.

See the [project charter](docs/PROJECT_CHARTER.md), [architecture](docs/ARCHITECTURE.md), [founding decisions](docs/DECISIONS.md), and [roadmap](ROADMAP.md).

## Current phase

This repository currently contains documentation, governance, security configuration, community templates, and a design backlog only. It contains no application, API, SDK, CLI, MCP server, database, package, deployment, or hosted service.

## Contributing

Start with [CONTRIBUTING.md](CONTRIBUTING.md), then open or join an issue or Discussion before undertaking substantial work. Public content must use neutral capability language and must not compare NZ Ledger with named accounting products or vendors.

Report security concerns through GitHub private vulnerability reporting as described in [SECURITY.md](SECURITY.md). Do not place secrets, personal data, or confidential financial information in a public issue or Discussion.

## Licence and stewardship

Licensed under the [Apache License 2.0](LICENSE). Copyright 2026 NZ Ledger contributors.

Founded and initially maintained by Mark Smith ([@nz365guy](https://github.com/nz365guy)).
