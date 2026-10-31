# Architecture decisions — Codex shadow AI relay defenses (2026-10-30)

## ADR-01: Restrict sandbox modes with managed policy
**Decision:** Admin-managed `requirements.toml` or equivalent workspace policy must disallow `danger-full-access` for managed endpoints. Prefer `workspace-write` with `on-request` approvals for local development. **Why:** a user-writable default can be rewritten by a relay setup script. **Trade-off:** occasional approval friction. **Validation:** controlled policy-denial test, supported CLI version check.

## ADR-02: Enforce approved AI destinations at the network boundary
**Decision:** Egress proxy/endpoint firewall allowlists provider destinations and alerts on unapproved base URLs. **Why:** endpoint file monitoring alone may miss environment-variable overrides. **Trade-off:** maintenance as legitimate endpoints change. **Validation:** harmless simulated request to a local unapproved test domain is blocked.

## ADR-03: Monitor config and environment-variable drift
**Decision:** Collect integrity events for `~/.codex/config.toml`, `~/.claude/settings.json`, managed policy, and suspicious user-scope provider/credential environment variables. Redact secret values. **Why:** genuine software can be unsafe after reconfiguration. **Trade-off:** noisy detections during legitimate onboarding. **Validation:** synthetic fixture change generates a SIEM event without revealing tokens.

## ADR-04: Separate credentials from agent execution
**Decision:** Prefer short-lived, least-privilege scoped tokens and a trusted broker; keep long-lived secrets outside agent-readable sandboxes. **Why:** a compromised or untrusted relay can see data submitted to it. **Trade-off:** extra credential-management infrastructure. **Validation:** synthetic agent cannot read application API key.

## ADR-05: Require reviewed installation provenance
**Decision:** Distribute approved CLI packages through managed channels, forbid unreviewed one-line remote scripts, and require review for alternate model providers. **Why:** the observed script was apparently run by users after legitimate CLI installation. **Trade-off:** slower experimentation. **Validation:** endpoint management flags unauthorized scripts and provider configuration.

## ADR-06: Keep detection claims evidence-based
**Decision:** Distinguish observed configuration rewrites from hypothetical data theft, malware or exploitation. **Why:** Symantec explicitly observed no operator abuse. **Trade-off:** less sensational messaging. **Validation:** every incident assertion maps to a dated primary or independent source.

## ADR-07: Use an isolated, non-exploit demonstration
**Decision:** Demonstrate only read-only audits and synthetic misconfiguration fixtures. Never fetch or execute suspicious scripts, contact relay infrastructure, or expose real credentials. **Why:** reproducible education without spreading a risky setup workflow. **Trade-off:** no live attack reproduction. **Validation:** lab has no real keys or outbound access to reported domains.

## Acceptance criteria
- Read-only audit identifies unexpected provider endpoint and `danger-full-access` in synthetic configuration.
- Admin policy prevents forbidden mode in supported CLI.
- Network allowlist blocks unapproved destinations.
- SIEM receives a redacted alert with a remediation path.
- Video explicitly states this is **not** a Codex or Claude Code vulnerability and no abuse has been demonstrated.

## Sources
- https://www.security.com/blog-post/openai-claude-unauthorized-relays
- https://www.darkreading.com/threat-intelligence/ai-relay-service-openai-codex-claude-code-sandboxes
- https://developers.openai.com/docs/sandboxing
- https://developers.openai.com/docs/config-file/config-reference
- https://developers.openai.com/api/docs/guides/agents-api/environments/security
