# Architecture decisions: Codex and Work usage governance

**Date:** 2026-10-31  
**Status:** Proposed / editorial reference only  
**Scope:** Internal capacity planning for OpenAI Codex and ChatGPT Work subscriptions. This does **not** claim to document OpenAI's internal metering.

## ADR-01 — Verify entitlements before capacity planning

**Context:** On October 29, 2026, OpenAI reversed the announced Pro 200 cut and doubled Work/Codex allowances for Pro 100 and Pro 500. Some Pro 200 subscribers retain the existing higher allowance; others are raised to it.

**Decision:** Record plan tier, current displayed usage and reset window from an authorized account. Never infer absolute quotas from plan price or news headlines.

**Consequences:** Avoids misleading capacity forecasts; requires periodic checks because allowances can change.

**Validation:** Compare the account's usage screen with dated official release notes. No account-level verification has been conducted for this article.

## ADR-02 — Separate subscription usage from API billing

**Context:** Work/Codex included allowances and API token/credit billing are distinct concepts.

**Decision:** Track API projects and Work/Codex consumption separately. Do not treat a subscription allowance increase as an API pricing change.

**Consequences:** Prevents inaccurate budgets and accidental over-spend.

**Validation:** Confirm separate usage/billing views and applicable product rate cards.

## ADR-03 — Optimize cost per accepted outcome

**Context:** A doubled allowance is not necessarily double throughput; tasks differ in reasoning, tool use, context and mode.

**Decision:** Measure accepted task completion, human review time, retries, and consumption by model/mode.

**Consequences:** More meaningful ROI comparisons; requires a consistent synthetic benchmark.

**Validation:** Run identical approved tasks with fixed acceptance tests, document settings and variance.

## ADR-04 — Require approval before spending beyond included usage

**Context:** Eligible users can purchase additional credits, which may be consumed after included usage.

**Decision:** Establish team thresholds and require explicit approval before additional paid consumption.

**Consequences:** Reduces bill surprises, at the cost of potentially pausing long-running tasks.

**Validation:** Exercise a simulated threshold crossing and confirm that an approval is required.

## ADR-05 — Keep sensitive workspace information out of recordings

**Context:** YouTube demonstrations may expose user names, repository paths, internal usage, or payment information.

**Decision:** Use synthetic repositories and redacted account screenshots. If a qualifying account is unavailable, clearly label UI mockups.

**Consequences:** Limits accidental disclosure but reduces the authenticity of demonstrations.

**Validation:** Review final footage frame-by-frame for secrets and identifiers.

## ADR-06 — Preserve editorial uncertainty

**Context:** The official October 29 note does not publish absolute allowance numbers or quantify Business Premium's increase.

**Decision:** Publish only the relative, officially stated changes; disclose missing details and distinguish indexed YouTube search from comprehensive creator-supply measurement.

**Consequences:** Lower sensationalism, stronger credibility.

**Validation:** Recheck official release notes before publication and update the article if a more specific statement appears.

## References

- https://help.openai.com/en/articles/6825453-chatgpt-release-notes
- https://help.openai.com/en/articles/11391654-chatgpt-business-release-notes
- https://help.openai.com/en/articles/9793128-about-chatgpt-pro-tiers
