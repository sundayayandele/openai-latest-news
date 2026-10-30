# The Real Codex CLI, the Wrong Server: How Shadow AI Relays Disable Sandboxes

**Research verified:** 2026-10-30 (publication dates checked)  
**Disclosure:** 2026-10-29, Symantec Threat Hunter Team  
**Category:** OpenAI/Codex ecosystem, DevSecOps, enterprise agent security  
**Status:** Editorial draft; no live exploitation or vendor compromise demonstrated

## Executive finding

Symantec disclosed a grey-market service that installs **genuine** OpenAI Codex and Anthropic Claude Code command-line clients and then changes their configuration to route requests to an unauthorized intermediary. The observed Codex settings included `sandbox_mode = "danger-full-access"`, with network permissions expanded, relay-issued credentials, and reduced telemetry. Researchers observed the setup in roughly a dozen organizations since early 2026, mainly in mainland China and some multinational operations in nearby markets.

**This is not a vulnerability in either CLI.** The software follows configuration changes made by a user-run setup script. **There is no observed misuse, data theft, or command execution by the relay operator** in the disclosed evidence. The threat is that an intermediary with visibility into prompts and responses, coupled with disabled sandbox restrictions, *could* abuse its position.

Primary: [Symantec, October 29](https://www.security.com/blog-post/openai-claude-unauthorized-relays). Independent reporting: [Dark Reading, October 29](https://www.darkreading.com/threat-intelligence/ai-relay-service-openai-codex-claude-code-sandboxes); [Cybersecurity News, October 30](https://cybersecuritynews.com/unauthorized-openai-codex/).

## What materially changed

This is a newly published *real-world configuration-risk disclosure*, not a new OpenAI model, API, or DevDay feature. It is relevant to DevDay's move toward increasingly capable, long-running Codex agents: genuine agent binaries can be deployed in an untrusted configuration, and developer endpoint security needs to protect **execution permissions, provider endpoints, and credentials** separately.

Symantec describes a user apparently running a one-line downloaded PowerShell or shell setup script after normal npm installation. The script can rewrite `~/.codex/config.toml` or `~/.claude/settings.json`, redirect the base URL, replace credentials, and change sandbox/network/telemetry controls. Researchers report at least eight relay domains and publish defanged indicators in the primary report. Do not contact or run code from those domains.

## Why the opportunity is timely

- **Fresh primary research:** Symantec published October 29.
- **Independent editorial pickup:** Dark Reading published a dedicated October 29 investigation, including analyst interview; Cybersecurity News followed October 30.
- **Practical viewer urgency:** engineering organizations can audit settings and egress today, without waiting for a patch.
- **Demand confidence: moderate:** fresh specialist coverage indicates emerging professional attention. There is **no independently measured YouTube search-volume trend** or audience-size estimate.
- **Creator supply confidence: low–moderate:** targeted indexed searches on October 30 for `Symantec Codex Claude relay sandbox`, `danger-full-access AI relay`, and exact disclosure wording did not surface a dedicated hands-on YouTube defense tutorial. Search indexes are incomplete, so **do not claim zero competitors**.
- **Opportunity score: 8.0/10**, conditional on shipping a reproducible, defensive configuration audit rather than a generic news recap.

## YouTube production package

**Primary keyword:** Codex CLI sandbox security audit  
**Secondary keywords:** Codex danger-full-access; shadow AI relay; Claude Code unauthorized proxy; AI coding agent endpoint security.

**Audience:** DevSecOps and platform engineers, enterprise SOC/IT security, software engineering leads, cloud architects, Codex/Claude Code administrators.

**Recommended title:** **Your Codex CLI Could Be Genuine — and Still Unsafe (Check These 3 Settings)**

**Alternate title:** **Codex & Claude Code Security Warning: Real Tools, Wrong Server, Sandbox OFF**

**Thumbnail:** `REAL CODEX. WRONG SERVER.` / `SANDBOX OFF?`

**Opening hook (approximately 30 seconds):**

> What if your AI coding assistant is the genuine tool, installed from the official package, but its requests are being routed to someone else's server — and its sandbox is switched off? On October 29, Symantec reported seeing exactly this configuration pattern in about a dozen organizations. This is not a new Codex vulnerability, and researchers have not shown data theft. But it exposes a serious blind spot in enterprise AI security. I'll show you how to inspect your own Codex settings, spot unexpected endpoints, and lock down the agent before it touches sensitive code.

**Urgency:** High for a specialized DevSecOps tutorial; publish October 30–November 1, 2026 while coverage is fresh. Recheck disclosures before release.

## Defensive lab and demonstration plan

**Safety rule:** Use a disposable VM or test workstation, a synthetic repository, placeholder credentials and **no third-party relay or attack script**. Never execute the observed one-liners or contact their domains.

1. Install an official Codex CLI in a controlled environment, or use a static mock configuration fixture for a fully offline demonstration.
2. Inspect the *effective* settings, user config (`~/.codex/config.toml`), trusted project config, environment overrides, and managed policy. Show how to identify unexpected model/provider URLs and risky `sandbox_mode` values. Do not display or print real keys.
3. Compare safe baseline `workspace-write` + `on-request` against a **text-only fixture** containing `danger-full-access`. Do not actually run the dangerous configuration.
4. Demonstrate read-only configuration auditing in Python that flags unauthorized hosts, full-access sandbox, user-scope key overrides, and telemetry changes; redact all secrets.
5. Add an administrator-managed `requirements.toml` restriction excluding full access, plus endpoint egress allowlisting and change monitoring. Validate policy behavior in a nonproduction environment with an up-to-date CLI.
6. Produce audit evidence: changed file hashes, sanitized endpoint names, expected vs. actual policy, a synthetic SIEM alert, and a remediation checklist. Confirm default policy and user permissions on Windows, macOS and Linux separately.

The CLI's sandbox and approval policy are different controls; OpenAI describes `workspace-write` and `on-request` as a lower-risk local default. The configuration reference documents admin-enforced `requirements.toml` and `allowed_sandbox_modes`; test version compatibility before relying on managed controls.

OpenAI references:
- [Sandbox and approvals](https://developers.openai.com/docs/sandboxing)
- [Configuration reference](https://developers.openai.com/docs/config-file/config-reference)
- [Basic configuration](https://developers.openai.com/docs/config-file/config-basic)
- [Agents API sandbox security](https://developers.openai.com/api/docs/guides/agents-api/environments/security)

## Editorial accuracy and release checklist

- Do **not** call this a zero-day, CVE, hacked Codex package, or confirmed exfiltration incident.
- Distinguish observed unauthorized configuration from possible downstream abuse.
- Do **not** assert that all Claude Code sandbox behavior matches Codex; observed `danger-full-access` setting is specifically Codex.
- Use only synthetic secrets and a controlled local audit; do not reproduce attacker installation scripts.
- Confirm current Codex CLI configuration keys and enterprise policy support before recording.
- Check direct YouTube results on publication day, record competing-video titles and timestamps if accessible, and revise the creator-supply claim.
- Show screenshots only from a disposable environment and redact tokens.

## Architecture

See [trust boundary diagram](../../../architecture/2026-10-30-codex-shadow-ai-relay-trust-boundaries.mmd) and [architecture decisions](../../../architecture/2026-10-30-codex-shadow-ai-relay-design-decisions.md).

## Source log

1. [Symantec Threat Hunter Team — October 29, 2026](https://www.security.com/blog-post/openai-claude-unauthorized-relays)
2. [Dark Reading — October 29, 2026](https://www.darkreading.com/threat-intelligence/ai-relay-service-openai-codex-claude-code-sandboxes)
3. [Cybersecurity News — October 30, 2026](https://cybersecuritynews.com/unauthorized-openai-codex/)
4. [OpenAI — Sandbox and approvals](https://developers.openai.com/docs/sandboxing)
5. [OpenAI — Configuration reference](https://developers.openai.com/docs/config-file/config-reference)
6. [OpenAI — Agents API sandbox security](https://developers.openai.com/api/docs/guides/agents-api/environments/security)

*No independent reproduction or live credential/network test was performed for this article.*
