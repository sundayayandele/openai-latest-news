# Architecture Decision Record: Defensive Agent Memory and Compaction Boundaries

**ADR ID:** ADR-2026-10-27-01  
**Status:** Proposed reference architecture, not an OpenAI implementation claim  
**Date:** 2026-10-27  
**Related research:** [Compaction Smuggling article](../articles/2026/10/2026-10-27-openai-compaction-smuggling-reasoning-leak.md)  
**Diagram:** [Mermaid architecture](2026-10-27-compaction-smuggling-defense.mmd)

## Context

Long-running AI agents need memory and compaction to preserve task context. Xint's October 26, 2026 disclosure describes a historical confidentiality failure involving encrypted reasoning artifacts, a server-side compaction transformation, and cross-model artifact portability. Xint says OpenAI fixed the issue October 23. We are designing controls for **application-owned** agent systems and provider integrations, not reconstructing the undisclosed OpenAI patch.

## Decision 1 — Explicitly typed continuation artifacts

**Decision:** Distinguish reasoning, summary, compacted state, tool output, and human-authored instructions. Maintain a signed or server-validated type, origin, purpose, expiry, tenant, and consumer policy.

**Alternatives:** A single opaque string or blob; rely on model text to describe provenance.

**Rationale:** A type boundary prevents accidental treatment of a lower-trust artifact as a higher-trust one. A model cannot be the source of truth about its own artifact privileges.

**Consequences:** More metadata, validation code, and migration planning. Does not itself guarantee secrecy against a privileged service that is allowed to decrypt.

## Decision 2 — Server-side policy before and after compaction

**Decision:** Authorize compaction based on identity and purpose, then validate resulting artifact classification and allowed consumers. Treat compaction as a privileged transformation.

**Alternatives:** Client-defined compaction instructions with unrestricted output routing; solely encrypt all artifacts.

**Rationale:** Encryption cannot prevent disclosure if an authorized decrypting transformation can be instructed to re-express secrets.

**Consequences:** Potentially less flexibility and more rejection events; improved separation of concerns.

## Decision 3 — No privilege promotion through summaries

**Decision:** Compacted memory may contain task facts, but never gain system/developer authority, credential access, or policy override powers. Tool outputs and generated summaries remain untrusted data.

**Alternatives:** Flatten all history into one text prompt and trust the newest summary.

**Rationale:** Long-running agents should not silently change their governing instructions based on generated continuation text.

**Consequences:** Explicit instruction hierarchy and a stable external policy store are needed.

## Decision 4 — Restrict cross-model and cross-tenant reuse

**Decision:** Enforce permitted consumer models, project ownership, tenant isolation, purpose binding, and expiration on state transfers. Reject unknown or incompatible artifact types.

**Alternatives:** Allow any compatible model to consume any continuation state.

**Rationale:** Model switching is operationally valuable but introduces new confidentiality boundaries.

**Consequences:** Some convenient migrations require explicit export/approval; additional integration tests.

## Decision 5 — Privacy-preserving telemetry

**Decision:** Log request IDs, artifact IDs or hashes, origin, destination, policy outcomes, size bands, and unusual replay attempts. Avoid recording decrypted hidden reasoning, customer secrets, or entire prompts by default.

**Alternatives:** Full request/response capture; no observability.

**Rationale:** Detect misuse without creating another sensitive data store.

**Consequences:** Forensics may require a controlled, time-limited diagnostic mode with access approvals.

## Decision 6 — Local defensive validation, not live exploitation

**Decision:** Demonstrate failures using synthetic tokens and mocked services; test denial of cross-purpose, cross-tenant, expired, and unapproved model transitions.

**Alternatives:** Reproduce the historical extraction against live third-party APIs.

**Rationale:** The former provides repeatable evidence for defenders without creating unnecessary confidentiality risk or relying on a patched external vulnerability.

**Consequences:** A simulator validates the proposed policy layer, not the provider's actual production controls.

## Security acceptance criteria

| Test | Expected result |
|---|---|
| Same tenant, approved purpose, valid artifact type, approved consumer | Allow |
| Different tenant | Deny and log metadata |
| Expired artifact | Deny |
| Reasoning artifact supplied where compaction is expected | Deny |
| Summary contains instruction to override system policy | Treat as data; do not elevate |
| Unapproved model consumer | Deny |
| Missing artifact provenance | Deny |
| Sensitive raw content in audit event | Reject/redact event |

## Non-goals

- Claiming to know OpenAI's proprietary mitigation.
- Demonstrating extraction of actual proprietary model reasoning.
- Proving the historical disclosure remains exploitable.
- Claiming compliance certification or production readiness without independent review.

## Sources

- https://xint.io/blog/compaction-smuggling-reasoning-traces
- https://www.securityweek.com/stolen-ai-reasoning-could-help-china-build-cheaper-rivals/
- https://developers.openai.com/api/docs/guides/compaction
- https://openai.com/index/introducing-the-agents-api/
