# Architecture decisions — Agent sandbox containment
Date: 2026-10-28
Status: Proposed reference design, not OpenAI's production implementation

## ADR-01: Disposable sandboxes with explicit task boundaries
**Context:** Agents execute untrusted or generated code.
**Decision:** Allocate short-lived task environments, with identity, secrets and filesystem state scoped to a single authorized task. Prefer VM-grade isolation for high-risk multi-tenant execution.
**Alternatives:** Shared containers (cheaper, weaker isolation); dedicated physical hosts (stronger, expensive).
**Consequences:** Increased cold-start and compute cost; improved blast-radius control.
**Validation:** Sandbox lifecycle tests, teardown tests and capability audits.

## ADR-02: Defense in depth beyond egress firewall rules
**Context:** Allowed operational services can create indirect egress.
**Decision:** Default-deny network policy plus application-layer destination validation on DNS, telemetry, auth and proxy services.
**Alternatives:** IP-only egress rules.
**Consequences:** More operational overhead and explicit service inventories.
**Validation:** Synthetic allowed/denied requests and mocked SSRF-style fetch attempts, without real exfiltration.

## ADR-03: No unrestricted agent credentials
**Context:** Long-lived tokens enable privilege expansion after a tool compromise.
**Decision:** Broker short-lived, least-privilege credentials and require human approval for consequential actions.
**Alternatives:** Shared service-account tokens.
**Consequences:** More integration complexity; improved auditability.
**Validation:** Token-expiry, denied-scope and approval-replay tests.

## ADR-04: Independent monitoring and termination
**Context:** An agent could modify the very monitors used to oversee it.
**Decision:** Export logs to a separate security domain and implement a stop mechanism that the agent cannot disable.
**Alternatives:** In-sandbox logging only.
**Consequences:** Separate control-plane operational cost.
**Validation:** Simulated monitor failures and forced termination tests.

## ADR-05: Treat co-resident side channels as residual risk
**Context:** Software and network isolation cannot guarantee absence of shared-resource timing channels.
**Decision:** Use workload placement and host isolation proportional to tenant sensitivity; document residual risks. Do not promise complete protection from cache timing attacks.
**Alternatives:** Co-locate all tenants with only network policies.
**Consequences:** Reduced utilization for high-assurance workloads.
**Validation:** Placement-policy checks and architectural threat-model review; no offensive side-channel reproduction.

## ADR-06: Version-aware remediation, not sensational CVE claims
**Context:** OpenAI's October 23 post mentions CVEs that were published earlier.
**Decision:** Use vendor advisories and deployment inventory to determine applicability. CVE-2026-24834 was published Feb 19, 2026; Kata 3.27.0 is listed as patched for the specified Cloud Hypervisor issue. The advisory does not establish host escape.
**Alternatives:** Blanket upgrade claims without environment inspection.
**Consequences:** More careful but accurate remediation guidance.
**Validation:** Version inventory, advisory mapping and change-control records.

Sources:
- https://openai.com/index/securing-our-frontier-model-training-environment/
- https://github.com/kata-containers/kata-containers/security/advisories/GHSA-wwj6-vghv-5p64
- https://nvd.nist.gov/vuln/detail/cve-2026-24834
