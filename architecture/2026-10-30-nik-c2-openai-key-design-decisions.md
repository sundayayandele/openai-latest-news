# NIK C2 / OpenAI API key abuse — architecture decisions

**Date:** 2026-10-30  
**Status:** Proposed for defensive reference architecture.  
**Scope:** Developer workstations, AI applications, project API credentials, usage monitoring and incident response. This is not an incident response report about a specific customer.

## ADR-01 — Do not place production API credentials on ordinary developer workstations
- **Context:** Talos describes a compromised Windows process reading the user's OPENAI_API_KEY when an operator supplies no key.
- **Decision:** Prefer approved backend brokers or isolated runtime workloads to hold production credentials; use development-only credentials for local experiments.
- **Alternatives:** Shared long-lived workstation keys; direct desktop-to-provider calls.
- **Consequences:** Extra backend complexity and latency; substantially better credential separation.
- **Validation:** No production key in workstation environment, repository, terminal transcript or CI log.

## ADR-02 — Use project-scoped, least-privilege credentials and expiration
- **Context:** A broad, long-lived key increases blast radius.
- **Decision:** Separate keys/projects by app and environment, restrict API permissions, expire and rotate keys, use workload identity federation where supported.
- **Alternatives:** One shared organization-wide key.
- **Consequences:** More lifecycle automation; better attribution and containment.
- **Validation:** Permission-negative tests; documented rotation; inventory of key owners and expiration dates.

## ADR-03 — Apply supported provider and network restrictions
- **Context:** Unauthorized API requests may originate from the infected workstation.
- **Decision:** Where available, use OpenAI API IP allowlisting and enterprise egress policies so only approved backend infrastructure can call the provider with production credentials.
- **Alternatives:** Rely on secret obscurity or spend alerts alone.
- **Consequences:** VPN/NAT/egress management and operational exceptions; not a cure for an infected host.
- **Validation:** An unapproved test origin is rejected without using a real production key.

## ADR-04 — Combine usage anomaly detection with enforced spending controls
- **Context:** A compromised key may incur charges even when the API behaves correctly.
- **Decision:** Monitor usage per project/key and set supported hard spending limits and alerts; document that enforcement may not be instantaneous.
- **Alternatives:** Monthly manual billing review only.
- **Consequences:** False positives and legitimate service interruptions if limits are reached.
- **Validation:** Synthetic usage spikes trigger an alert and a tested incident workflow.

## ADR-05 — Treat endpoint compromise as a separate incident
- **Context:** Key rotation does not remove remote-access malware.
- **Decision:** Isolate suspected endpoints, preserve evidence, use endpoint detection/response and incident response procedures, and revoke/reissue exposed credentials.
- **Alternatives:** Rotate the key and resume work immediately.
- **Consequences:** Potential device downtime and forensic cost; necessary for confidence in containment.
- **Validation:** Documented IR playbook and tabletop exercise using only synthetic artifacts.

## ADR-06 — Never expose real secrets or malware in the public tutorial
- **Context:** A security video can accidentally leak a credential or teach dangerous malware operation.
- **Decision:** Use a local mock endpoint, fabricated usage data, synthetic keys and conceptual architecture; no malware execution, C2 interaction or unauthorized requests.
- **Alternatives:** Live attack recreation.
- **Consequences:** Less dramatic demo, but reproducible and safe.
- **Validation:** Recorded video, source files and Git history contain no real keys or malware payloads.

## ADR-07 — Separate evidence from inference in publication
- **Context:** Talos observed functional infrastructure with moderate confidence, not a verified global victim count or confirmed number of malicious OpenAI requests.
- **Decision:** Attribute the key fallback and malware capabilities to Talos; explicitly say the case does not establish an OpenAI API vulnerability, and that YouTube supply/demand signals are partial.
- **Alternatives:** Treat secondary headlines as verified production incidents.
- **Consequences:** More qualified language; greater editorial accuracy.
- **Validation:** Pre-publication fact-check against the primary Talos article, current OpenAI guidance and fresh YouTube search.

## References

- https://blog.talosintelligence.com/nik-c2-new-ai-enabled-framework/
- https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety
- https://help.openai.com/en/articles/8304786-keeping-your-openai-account-secure
- https://help.openai.com/en/articles/9186755-managing-projects-in-the-api-platform
