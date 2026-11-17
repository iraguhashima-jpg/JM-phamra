# Architecture direction (proposed; not implemented)

This document records implementation constraints for the empty JM Pharma repository. It describes intended boundaries, not capabilities currently available in software.

## Proposed application shape

- Laravel application as the authoritative owner of authentication, validation, authorization, tenant scope, transactions, audit events, scheduling, and notification dispatch.
- Vue 3 + TypeScript + Inertia for role-specific, responsive interfaces. UI visibility is never an authorization boundary.
- MySQL-compatible relational storage with foreign keys, explicit unique constraints, indexes, and transactional writes.
- Laravel queues and scheduler for background work. A database-backed queue is the lightweight local fallback; external delivery providers are opt-in adapters configured only via environment secrets.
- Private filesystem storage for health evidence. Retrieval must pass an authorization check and be streamed through a controlled endpoint or short-lived authorized mechanism; no public evidence URLs.

## Tenant and access boundaries

Every pharmacy-owned operation must resolve an authorized pharmacy context on the server. Pharmacy staff may only access records associated with that pharmacy and a permitted assignment. Patients may access only their own records. Super-admin access must be explicit, policy-checked, and audited where sensitive records are accessed. Policies and service-layer checks must be exercised with cross-tenant negative tests.

## Medication history and dose reporting

Prescription instructions and schedule definitions are not mutated by a patient's report. A scheduled dose occurrence is distinct from its report/event history. Record patient-reported taken/missed outcomes as reports, never as proof of ingestion. Cancelled doses are excluded from adherence denominators; unconfirmed doses remain distinct from reported misses. Dose creation and notification dispatch require stable idempotency keys and database uniqueness protections.

## Evidence lifecycle

Evidence submissions reference an authenticated patient, an eligible dose/schedule, private object metadata, consent context, and review state. Validate content and size server-side, assign generated storage names, and never trust the submitted filename or client MIME type alone. Reviews are attributable to an authorized pharmacist and time-stamped; subsequent patient reports and review decisions must not erase the original record. Define retention/deletion rules before collecting real health data.

## External services and safety

Email, SMS, push, Firebase, and WhatsApp require provider credentials, delivery/error tracking, and documented consent/opt-out behavior. Keep adapters disabled until configured; local fallbacks must not masquerade as real delivery. Messaging must state it is not an emergency service. No AI diagnosis, prescribing, or image-based claim of medication ingestion.

## Decisions still to validate

- Supported PHP and Laravel versions and package compatibility at implementation time.
- Identity, invitation, consent, and account-recovery workflows.
- Applicable Rwanda privacy, health-data, retention, and hosting obligations with qualified legal/security review.
- Supported notification providers, delivery semantics, retries, and patient preferences.
- Backup encryption, recovery objectives, monitoring, and production hosting topology.

No item above is currently implemented or independently assessed.
