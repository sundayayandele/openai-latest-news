# Architecture Decision Records — OpenAI API Credits, Agent Demo and FinOps

**Date:** 2026-10-29  
**Status:** Proposed; editorial and engineering review required  
**Companion research:** [October 29 API credits article](../articles/2026/10/2026-10-29-openai-pro-business-api-credits.md)  
**Diagram:** [Editable Mermaid source](./2026-10-29-chatgpt-api-credits-agent-finops.mmd)

> This is a proposed application architecture for demonstrating the October 28, 2026 subscription API-credit benefit. It is not a description of OpenAI's internal billing implementation. No live account or API grant was accessed.

## ADR-001 — Treat credits as a time-bound promotional billing grant, not cash or a zero-cost workload

**Context:** The published benefit is an API organization-specific promotional grant, prorated initially and expiring with the relevant billing cycle. Underlying usage still incurs normal API billing calculations.

**Decision:** Maintain separate fields for `estimated_api_cost_usd`, `promotional_grant_issued_usd`, `promotional_grant_remaining_usd`, `grant_expires_at`, `purchased_balance_usd` and `paid_spend_usd`. Do not call usage "free" in operational dashboards; show **cost offset by credits**.

**Alternatives:** Single "free balance" number; treating grant as cash rebate. Rejected as inaccurate.

**Consequences:** Better cost forecasting after the promotion, at the expense of a slightly more complex dashboard.

**Validation:** Reconcile usage and credit-grant records; demonstrate a grant expiry without modifying the underlying unit-cost calculations.

## ADR-002 — Link one deliberately chosen API organization; enforce ownership separation

**Context:** The claim flow requires an eligible subscription/workspace owner, an API organization owner and the same email address. One subscription/workspace links to one API organization.

**Decision:** Require billing-owner review of the receiving organization before confirmation. Keep the demo workload in a dedicated project of that organization. Do not claim that workspace membership grants API permissions.

**Alternatives:** Create multiple organizations and rotate grants; use an unrelated project. Rejected because the published linking and ownership rules do not support the assumption.

**Consequences:** Stronger governance and a reproducible billing audit. Organization reassignment may require OpenAI support.

**Validation:** Verify organization ID in the claim confirmation and billing view; verify project key belongs to the intended organization without displaying secrets.

## ADR-003 — Use project-scoped server-side credentials

**Context:** The tutorial needs an API key but must not expose it to browsers, source control, video recordings or log sinks.

**Decision:** Use a dedicated project key stored in a server-side secret manager or local environment variable outside source control. Never call the API directly from an untrusted browser with a long-lived key. Rotate after filming if there is any exposure.

**Alternatives:** Hard-code key in FastAPI; embed key in front-end JavaScript. Rejected.

**Consequences:** Adds server deployment complexity but limits blast radius.

**Validation:** Run a secret scanner against repo and recorded materials; assert no `OPENAI_API_KEY` value appears in logs or HTTP responses.

## ADR-004 — Disable unapproved paid top-ups and distinguish exhaustion from rate limiting

**Context:** Free credits are spent first; purchased credits can be consumed afterward. If no credit remains, API calls can fail with HTTP 429 and `credit_balance_exhausted`. Other rate-limit 429s may have different causes.

**Decision:** Do not enable Auto Recharge for the demonstration. Require explicit billing-owner authorization before any purchase. Implement separate error handlers for credit exhaustion, rate limits and transient service failures. Backoff is appropriate for rate limits; it is not a remedy for an exhausted balance.

**Alternatives:** Automatically recharge; retry every 429 indefinitely. Rejected as unsafe and ineffective.

**Consequences:** Demo may stop when grant is exhausted; no surprise purchases.

**Validation:** Mock both 429 classes and verify correct messaging, retry policy and alerting. Do not intentionally spend real credit to test depletion.

## ADR-005 — Use synthetic data, human review and no production write permissions

**Context:** An agent demo can accidentally introduce customer-data exposure or prompt injection if live tickets and unrestricted tools are used.

**Decision:** Feed only synthetic tickets; apply input validation; treat ticket descriptions as untrusted data; return classification suggestions to a human-review queue. No live CRM/ITSM write tool is attached.

**Alternatives:** Demonstrate with real production tickets and automatic edits. Rejected due to privacy, authorization and correctness risks.

**Consequences:** Slightly less dramatic video, much safer and easier to reproduce.

**Validation:** Run injection test strings embedded in tickets; assert no policy override, no unauthorized tool call and no external write action.

## ADR-006 — Measure cost per successful task and show the true benefit separately

**Context:** A headline $500 allowance does not prove that a specific agent is economical or reliable.

**Decision:** Benchmark identical synthetic tickets across models, recording token usage, request counts, latency, output validity, classification correctness and cost per successfully completed task. Present actual usage charges and promotional offsets in separate charts.

**Alternatives:** Compare only per-token price or report zero spend after credit offsets. Rejected.

**Consequences:** A defensible engineering conclusion and a useful viewer takeaway.

**Validation:** Re-run a fixed labeled dataset; compare independent cost calculations against API usage records; label all estimates and sample sizes.

## ADR-007 — Alert on grant expiry, allowance changes and rollout restrictions

**Context:** Credits do not roll over, the initial grant can be prorated, and OpenAI may change or remove the benefit.

**Decision:** Maintain a review date, grant expiry alert and a current-terms link. Before publication or implementation, confirm the benefit appears in the actual account and that current country/cohort eligibility is satisfied.

**Alternatives:** Treat the offer as permanent and globally universal. Rejected.

**Consequences:** Requires periodic verification but reduces misleading claims and failed onboarding.

**Validation:** Confirm grant expiry in API billing; test expiry notification with simulated data; review primary docs before the video goes live.

## Deployment and validation backlog

| Phase | Implementation | Acceptance evidence |
| --- | --- | --- |
| 0 | Confirm eligible subscription, owner and same-email requirements | Account owner sees claim option and grant after activation |
| 1 | Create API project, server-side secret and safe limits | Key scoped to correct organization; no secret leak |
| 2 | Build FastAPI classification service with synthetic fixtures | Deterministic contract tests |
| 3 | Add cost and usage ledger | API usage and promotional grant shown separately |
| 4 | Add mocked exhaustion, rate-limit and prompt-injection tests | Graceful handling and no unauthorized action |
| 5 | Capture video and edit screenshots | No PII, credentials, or account identifiers |
| 6 | Recheck offer terms immediately before publishing | Primary-source links and current date recorded |

## Source of truth

- [OpenAI API changelog](https://developers.openai.com/api/docs/changelog)
- [Official developer guide](https://developers.openai.com/api/docs/guides/chatgpt-api-credits)
- [OpenAI Help Center eligibility and billing details](https://help.openai.com/en/articles/20001626-api-credits-benefit-for-eligible-chatgpt-subscriptions)
- [OpenAI ChatGPT release notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes)
