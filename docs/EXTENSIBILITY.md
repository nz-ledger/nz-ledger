# Extensibility

NZ Ledger is infrastructure. Any developer should be able to create connectors, business and industry modules, user experiences, reports, workflows, agents, analytics, organisation-specific rules, and close controls through stable public contracts.

## Extension categories

- **Connectors** exchange evidence, commands, events, and controlled data with external systems.
- **Business modules** implement specialist workflows while preserving kernel and authority contracts.
- **Experience applications** provide human interfaces without becoming alternate systems of record.
- **Agent and workflow extensions** automate bounded actions under explicit mandates.
- **Reporting and analytics extensions** consume governed queries and events without direct table coupling.
- **Organisation-specific policy extensions** add local policy without silently replacing official New Zealand policy packs.

## Public surface

### Commands

Versioned commands express intent and carry authentication, principal, mandate, legal entity, idempotency, correlation and causation identifiers, effective time, evidence references, expected version for optimistic concurrency, requested action, and contract version. They support authorised dry-run validation, structured errors, and audit attribution. Acceptance is not evidence that a consequential external action completed.

### Queries

Queries return authorised, versioned views. They declare scope, pagination, effective and recorded time basis, and data classification. Internal database tables are not public contracts.

### Events

Events are immutable notifications of facts produced through a transactional outbox. Every envelope carries event ID, event type, schema version, legal entity, effective time, recorded time, actor, correlation ID, causation ID, data classification, and payload. Consumers must handle at-least-once delivery, idempotency, ordering boundaries, redaction, replay, and compatibility. Events do not grant authority to issue commands.

### Evidence

Evidence contracts preserve source, content hash, media type, classification, residency, retention, provenance, versions, and access decisions. Derived extraction never replaces the original.

### Agent tools

Agent tools expose bounded operations over the same command and query contracts. Tool descriptions, model confidence, and workflow configuration do not expand a principal's mandate.

## Manifest

A future extension manifest will identify:

- extension name and publisher identity;
- version and compatible contract range;
- extension category and endpoints;
- requested commands, queries, events, and evidence access;
- subscribed events and outbound endpoints;
- data classifications, residency, and retention;
- monetary, counterparty, instrument, and time boundaries;
- network and external-service requirements;
- public signing key, signatures, and provenance;
- health endpoint where applicable;
- support status and documentation;
- install, upgrade, disable, and uninstall behaviour;
- owned data and export behaviour;
- failure and recovery modes;
- compatibility, conformance, and review evidence.

The manifest format will be defined through an accepted design issue. No marketplace or installable extension exists yet.

## Permissions and authority

Permissions are deny-by-default, explicit, least-privilege, time-bounded where practical, and revocable. They can be scoped by organisation, legal entity, account or subledger, operation such as read, propose, post, approve, execute, or administer, transaction type, counterparty, financial instrument, monetary threshold, time window, data classification, evidence type, event subscription, and expiry. Read, command, event-subscription, evidence, network, and administrative permissions are separate. Every extension has its own service principal. Installation does not create financial authority. A human or agent principal still requires an applicable mandate and approvals for each consequential command.

Extensions receive no data beyond granted permissions. Sensitive fields and evidence can require purpose-specific grants. Audit records capture publisher, installed version, permissions, installer, decisions, and changes.

## Isolation and failure

Extensions do not:

- write directly to core storage;
- run arbitrary code inside the accounting kernel;
- bypass financial invariants or authority controls;
- silently alter official policy packs;
- receive broad tenant data by default;
- block the immutable recording of accepted financial actions;
- convert an unknown state into an assumed fact.

Third-party extensions normally execute in their own service, container, cloud environment, customer-managed infrastructure, or local process using public contracts. They cannot add arbitrary tables or columns to the core financial schema. Sandbox requirements, process and network isolation, resource limits, timeout and retry policy, event back-pressure, secret delivery, evidence handling, and failure quarantine must be designed before executable extensions are supported. Self-hosted extension execution is not part of the founding repository.

## Compatibility and conformance

Public contracts use semantic versioning, versioned schemas, stable event envelopes, documented deprecation, compatibility negotiation, migration guides, golden vectors, a supported-version matrix, a local sandbox, mock integrations, and example manifests. Consumers must not depend on unspecified fields, ordering, internal identifiers, or storage layout. Breaking changes require an RFC, migration path, and compatibility window.

Extensions must pass language-neutral schema, canonical serialisation, command, query, event, evidence, permission, failure, and determinism tests appropriate to their category. Conformance does not represent accounting, tax, legal, regulatory, or security approval.

## Marketplace readiness without a marketplace

The project will design publisher identity, signed manifests, permissions, install and uninstall lifecycle, compatibility checks, security and support metadata, customer-controlled telemetry, discovery metadata, provenance, revocation, and export so a future ecosystem is possible. It will not operate a marketplace at bootstrap. Any future directory is a distribution and discovery layer, not a redesign of the platform. Discovery and distribution remain ordinary source and package mechanisms until a reviewed need, governance model, security boundary, and operating capacity exist.
