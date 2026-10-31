# Architecture decisions — October 2026 AI developer-platform credential incidents

**Status:** Proposed defensive pattern, not a claim about any specific vendor's controls.

## ADR-01: Provider keys never reach untrusted clients or prompts
**Decision:** Store provider secrets only in authorized server-side secret systems. **Rationale:** stolen keys are monetizable through model consumption. **Trade-off:** backend proxy complexity. **Acceptance:** no provider keys in source, frontend bundles, agent prompts or ordinary logs.

## ADR-02: Limit the scope and lifetime of credentials
**Decision:** Separate per-project, per-environment and per-workload credentials; use least-privilege roles and shorter lifetimes where supported. **Rationale:** constrain blast radius. **Trade-off:** rotation overhead. **Acceptance:** a staging credential cannot access production resources.

## ADR-03: Monitor usage independently from agent runtime
**Decision:** Collect API usage and cloud audit signals outside the workload, correlating key alias, model, source, region and cost. **Rationale:** an attacker controlling the runtime can tamper with its local logs. **Trade-off:** additional telemetry cost. **Acceptance:** synthetic new-region and high-rate events trigger alerts.

## ADR-04: Treat alerts and hard limits differently
**Decision:** Use both spend alerts and enforceable quotas/limits where providers offer them. **Rationale:** a dashboard budget may not prevent ongoing spend. **Trade-off:** limits can interrupt legitimate traffic. **Acceptance:** document which controls actually block calls and test them safely.

## ADR-05: Keep a rehearsed cross-vendor rotation runbook
**Decision:** Rotate exposed credentials, update dependent workloads, confirm old-key rejection, preserve logs and contact providers. **Rationale:** vendor incidents cross cloud and AI boundaries. **Trade-off:** operational interruption. **Acceptance:** tabletop drill completes without exposing secrets.

## ADR-06: Isolate agent execution and CI
**Decision:** Separate build, agent sandbox and production secrets; restrict network egress and third-party integrations. **Rationale:** reduce credential access paths. **Trade-off:** developer friction. **Acceptance:** sandbox cannot read unrelated secret mounts or reach unauthorized endpoints.

## ADR-07: Avoid over-attribution
**Decision:** Treat Wiz's two Bedrock cases as confirmed research observations, but do not label them OpenAI API breaches. **Rationale:** prevents incorrect security claims. **Acceptance:** every quantitative claim maps to primary research and states its uncertainty.

## ADR-08: Maintain vendor-specific incident scope
**Decision:** Follow each platform's current advisory rather than assuming all nine share an exploit. **Rationale:** root causes differ and investigations are ongoing. **Acceptance:** remediation checklist names the affected vendor, service, dates and required key classes.

**Primary sources:** https://www.wiz.io/blog/black-october-for-ai-developer-platforms ; https://blog.railway.com/p/preliminary-report-october-security-incident