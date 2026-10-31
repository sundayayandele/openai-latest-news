# Architecture design decisions — OpenAI / ServiceNow MCP governed ITSM workflows

**Date:** 2026-10-28  
**Status:** Proposed reference architecture (not a tested production deployment)  
**Scope:** Authorized enterprise ChatGPT-to-ServiceNow incident/change workflows.  
**Sources:** [OpenAI](https://openai.com/index/openai-servicenow-partnership-expansion/) · [ServiceNow](https://newsroom.servicenow.com/press-releases/details/2026/ServiceNow-and-OpenAI-accelerate-enterprise-AI-from-models-and-operations-to-cybersecurity-and-agentic-development/default.aspx)

## ADR-01 — Keep ServiceNow as the policy-enforcing system of record

**Context:** An LLM can recommend an update, but model prompts do not enforce access control.  
**Decision:** Route all reads and writes through approved ServiceNow plugin capabilities and ServiceNow Action Fabric, with server-side identity/ACL/business-rule evaluation.  
**Alternatives:** Direct database updates; administrator-scoped automation credentials; prompt-only permission checks.  
**Consequences:** More reliable authorization and traceability, but permissions can still be misconfigured.  
**Validation:** Run permitted and denied actions with different test personas; verify record state and audit entries.

## ADR-02 — Require human approval for high-impact actions

**Context:** Change approvals, privileged account operations, security exceptions and destructive updates can affect availability and compliance.  
**Decision:** Separate suggestion from execution. Require explicit approval by a properly authorized human for high-impact actions.  
**Alternatives:** Fully autonomous writes; blanket denial of all writes.  
**Consequences:** Lower automation speed for critical tasks, stronger operational control.  
**Validation:** Test rejected, pending and approved states; verify no early or duplicate execution.

## ADR-03 — Treat incident content as untrusted data

**Context:** Ticket descriptions and alert payloads can contain malicious or misleading instructions.  
**Decision:** Do not let external text redefine tools, permissions or the system instruction hierarchy; validate allowed action parameters and require server-side authorization.  
**Alternatives:** Trust ticket text as instructions; rely solely on LLM safety prompting.  
**Consequences:** Additional filtering, negative testing and observability.  
**Validation:** Use harmless prompt-injection strings in synthetic tickets and confirm policy enforcement is unaffected.

## ADR-04 — Minimize data sharing and use least privilege

**Context:** ITSM records may contain employee data, secrets, topology, customer details and internal incident histories.  
**Decision:** Expose only the fields, record types and tools needed for a narrowly scoped workflow; apply enterprise retention and consent rules.  
**Alternatives:** Broad instance-level admin integration; full CMDB export.  
**Consequences:** Narrower assistant utility initially; smaller disclosure and blast radius.  
**Validation:** Verify redaction, scoped read permissions and denied cross-department access.

## ADR-05 — Make actions auditable, idempotent and verifiable

**Context:** Agents can retry after a timeout or misinterpret stale state.  
**Decision:** Use correlation IDs where supported, read-back verification, optimistic concurrency where available, explicit retry policies and alerting for failed or unexpected writes.  
**Alternatives:** Fire-and-forget writes with no audit reconciliation.  
**Consequences:** Additional orchestration complexity, reduced duplicate or silent updates.  
**Validation:** Inject timeouts, stale records and repeated requests in a test tenant; compare expected and actual record history.

## ADR-06 — Separate vendor-announced capabilities from lab simulations

**Context:** The plugin is announced as available October 28, but exact tenant access, tool names, pricing and region entitlements are not established in public release materials. Additional security, voice and Codex capabilities are planned for 2027.  
**Decision:** Label diagrams as conceptual. Record actual tenant, access tier and observed actions before claiming a live tutorial. Never present a FastAPI mock as ServiceNow's production implementation.  
**Alternatives:** Assume broad GA availability of every roadmap feature.  
**Consequences:** More accurate educational content and less misleading adoption advice.  
**Validation:** Confirm plugin listing, OAuth/consent flow, available actions, logs and behavior in an authorized non-production instance.

## Implementation and editorial acceptance criteria

- [ ] Real tenant and user entitlements confirmed or mock explicitly labeled.
- [ ] No sensitive real-world incident or customer records used.
- [ ] At least one permitted action, one denied action and one approval-gated action tested.
- [ ] Read-back state and available audit history shown.
- [ ] Failure/retry and injection-resilience behavior recorded.
- [ ] Current plugin distinguished from end-2026 enhancements and 2027 roadmap.
- [ ] No claim of measured YouTube search growth without a suitable time series.
- [ ] Video title, thumbnail and hook accurately reflect what was demonstrated.
