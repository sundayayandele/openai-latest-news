# Architecture Design Decisions — Oracle + OpenAI Enterprise AI (October 27, 2026)

**Status:** Proposed reference design, not an Oracle product implementation.  
**Companion:** [Research article](../articles/2026/10/2026-10-27-openai-oracle-enterprise-ai-and-smb-credits.md) · [Mermaid diagram](2026-10-27-oracle-openai-enterprise-reference.mmd)

## ADR-01: Keep authorization outside model reasoning

**Context:** An agent may suggest actions across ERP, databases and deployment services.  
**Decision:** Enforce SSO/OIDC, tenant identity, least-privilege RBAC/ABAC and transaction approvals in enterprise gateways and source applications, never solely in prompts.  
**Alternatives:** Model-only instruction checks (rejected: non-deterministic); broad service account (rejected: excess privilege).  
**Consequences:** Additional integration work; reliable access boundaries, audit and revocation.  
**Validation:** Negative permission tests, cross-tenant access tests, authorization trace correlation.

## ADR-02: Treat all agent output as untrusted until validated

**Context:** Model outputs may contain mistaken SQL, business conclusions or code changes.  
**Decision:** Schema validation, business-rule checks, policy gates and explicit human approvals for material transactions and production modifications.  
**Alternatives:** Direct agent execution (rejected for high-impact actions).  
**Consequences:** Slower sensitive workflows; improved control and recovery.  
**Validation:** Test malformed outputs, prompt-injection attempts and unauthorized action requests.

## ADR-03: Separate security discovery from validation and remediation

**Context:** Oracle AI Security Mesh describes independent verification and customer-authorized scope.  
**Decision:** Keep discovery, reproduction/validation, remediation proposal, approval, execution and retest as separate stages; test only assets explicitly authorized.  
**Alternatives:** Single autonomous agent that discovers and patches production (rejected: operational and security risk).  
**Consequences:** More evidence handling, lower chance of erroneous production changes.  
**Validation:** Simulated finding, false positive, rejected remediation, rollback and retest exercises.

## ADR-04: Preserve normal software-delivery gates for generated apps

**Context:** Codex/App Composer can generate applications; generated code still carries dependencies, secrets and operational risks.  
**Decision:** Require CI, tests, SAST/SCA, IaC policy checks, artifact provenance, staged rollout and rollback for production deployment.  
**Alternatives:** Immediate deployment from generated output (rejected for production).  
**Consequences:** Additional pipeline overhead; reproducibility and reduced change risk.  
**Validation:** CI gate tests, staged canary, restore drill, vulnerability triage.

## ADR-05: Budget cloud credits as finite, conditional entitlements

**Context:** Oracle advertises up to US$12k/year OCI credits for eligible approved US SMBs, subject to annual renewal and billing for excess use.  
**Decision:** Track monthly OCI spend and remaining awarded credits separately from ChatGPT subscriptions, app free tiers and any one-time usage credits. Apply budgets, alerts, service quotas and an offboarding plan.  
**Alternatives:** Treat advertised credit maximum as guaranteed cash or unrestricted platform use (rejected).  
**Consequences:** Better spend visibility; requires cost governance and entitlement verification.  
**Validation:** Simulated overage, renewal failure, quota enforcement, resource cleanup.

## ADR-06: Explicitly label vendor plans versus verified features

**Context:** Announced offerings have varying availability, and some are under development.  
**Decision:** Publish conceptual diagrams and hypothetical demonstrations with labels; do not call them live Oracle AI Workspace/Security Mesh product tests without verified access.  
**Consequences:** More precise claims, fewer misleading implementation expectations.  
**Validation:** Editorial review against dated official release notes, product docs and direct access checks.

## Open questions before a production proof of concept

- What are current GA/limited-availability terms, tenancy restrictions, API contracts and commercial pricing for each product?
- What audit/event export, data residency, data retention, credential delegation and incident-response controls are exposed?
- What explicit approval mechanism governs high-impact actions and remediation?
- How do annual credit renewal and product entitlements apply to a specific US legal entity?

## Primary references

- [OpenAI partnership announcement](https://openai.com/index/oracle-apps-in-chatgpt/)
- [Oracle press release](https://www.oracle.com/news/announcement/ai-world-oracle-reinvents-enterprise-computing-for-the-ai-era-with-openai-2026-10-27/)
- [Oracle AI Security Mesh](https://www.oracle.com/artificial-intelligence/security-mesh/)
- [Oracle SMB bundle and restrictions](https://www.oracle.com/artificial-intelligence/oracle-openai/)
