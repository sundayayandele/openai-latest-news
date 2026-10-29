# EMBRACE / Codex Embedded-Browser Security — Architecture Decision Record

**Date:** 2026-10-29  
**Status:** Proposed defensive reference architecture; not a vendor implementation  
**Companion:** [Research article](../articles/2026/10/2026-10-29-embrace-codex-embedded-browser-security.md) · [Mermaid trust-boundary diagram](2026-10-29-embrace-codex-browser-trust-boundaries.mmd)

## Context

Forever Security's October 29 EMBRACE disclosure documents weaknesses in browser capabilities embedded in five AI desktop tools. Researchers say the Codex and VS Code local-file findings remained unpatched at publication. The threat is a failure of isolation between untrusted HTML content and privileged local files or agent actions, not proof that every coding-agent use is compromised. Vendor versions, fixes and affected feature paths must be checked separately.

## ADR-01 — Treat embedded previews as a separate attack surface

**Decision:** Inventory and govern embedded browser/preview capabilities independently from the coding agent and normal web browser.

**Rationale:** A browser integrated with an agent may receive file-system or application privileges unavailable to a standard browser. A tool's general sandbox or workspace-trust dialog is not sufficient evidence of containment.

**Tradeoff:** More endpoint inventory and user education.  
**Verification:** Audit which tools and preview paths can open local HTML and what process privileges they inherit.

## ADR-02 — Isolate untrusted local HTML and repositories

**Decision:** Render untrusted local HTML in a disposable, restricted environment or a suitable standard browser, rather than a privileged embedded preview. Separate untrusted repository evaluation from the developer's primary workstation where practical.

**Rationale:** A trust boundary is stronger when untrusted content cannot see the user's home directory, credentials, or unrelated projects.

**Tradeoff:** Preview convenience and file-system integration may be reduced.  
**Verification:** A synthetic local report cannot read mock secrets outside the isolated test directory; confirm without using exploit payloads.

## ADR-03 — Minimize workstation credential exposure

**Decision:** Prefer secret-manager retrieval, short-lived credentials, least-privilege tokens and separate identities for development versus production.

**Rationale:** Even with preview controls, a compromised desktop process has less valuable material to expose.

**Tradeoff:** Extra authentication, rotation and secret-management complexity.  
**Verification:** Secret inventory finds no unnecessary long-lived production credentials on developer endpoints.

## ADR-04 — Enforce agent action approvals and outbound access policy

**Decision:** Keep explicit human approval for consequential commands and external actions; use egress controls and audit logs where possible. Do not assume approval gates repair browser file-isolation flaws.

**Rationale:** The research covers both agent-mediated execution and direct local-file exposure, requiring different controls.

**Tradeoff:** Slower automation and potential false-positive alerts.  
**Verification:** Synthetic policy tests demonstrate rejected unapproved commands and logged blocked outbound destinations.

## ADR-05 — Implement a patch-status evidence gate

**Decision:** Track affected product/interface, version, disclosure date, official vendor advisory and regression-test result. Only say "fixed" after verifying the relevant feature path.

**Rationale:** Researcher-reported patch status can change quickly; EMBRACE is a class name, not a single CVE with one remediation version.

**Tradeoff:** Editorial and operational overhead.  
**Verification:** Every published patch claim links to a dated vendor advisory or tested release note.

## ADR-06 — Protect incident response and evidence quality

**Decision:** For suspected exposure, capture relevant logs and process/network indicators under the organization's incident-response procedure, assess whether secrets were accessed, and rotate credentials when warranted.

**Rationale:** Avoid treating all users as breached while still responding decisively to a plausible workstation credential incident.

**Tradeoff:** Investigation time and operational disruption.  
**Verification:** Tabletop exercise covers untrusted project intake, unexpected preview execution, credential containment and recovery.

## ADR-07 — Use safe, transparent demonstrations

**Decision:** Demonstrate the trust boundary with fake credentials and an isolated policy gate. Do not reproduce live file-exfiltration exploits or claim access to an affected product/version without testing.

**Rationale:** The educational goal is to improve defensive engineering, not to distribute exploit steps or overstate certainty.

**Tradeoff:** Viewers see a simulated workflow rather than a live compromise.  
**Verification:** Video labels simulation, uses synthetic data, and cites primary research and its limitations.

## Recommended rollout

**Immediate:** Notify engineering teams of the new research, identify whether embedded local HTML preview workflows are used, and reduce exposure to untrusted previews.  
**Within 48 hours:** Validate product/version scope, check vendor fixes, audit endpoint credential posture, and publish internal guidance.  
**Within one week:** Establish policy tests and tabletop exercises for AI-agent preview boundaries; feed findings into endpoint management and developer onboarding.

## Sources

- Forever Security (Oct 29): https://forever.security/blog/embrace-hacking-5-ai-assistants-embedded-browsers
- Technical companion (Oct 29): https://forever.security/blog/embrace-hacking-5-ai-assistants-embedded-browsers-technical
- Help Net Security (Oct 29): https://www.helpnetsecurity.com/2026/10/29/research-ai-assistant-browser-vulnerabilities/
- Hackread (Oct 29): https://hackread.com/embrace-flaws-ai-coding-tools-code-execution-theft/

**Important:** This ADR set is independent design guidance and is not a description of the exact implementation of Codex, VS Code, or other vendor products.
