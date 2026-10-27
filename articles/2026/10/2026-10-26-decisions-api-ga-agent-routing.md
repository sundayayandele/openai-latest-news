---
title: "OpenAI Decisions API Is Now Generally Available: 400 Questions, GPT-Live Routing, Caching, and the Case for Faster AI Agents"
date: 2026-10-26
last_verified: 2026-10-26
status: draft
topics: [openai, devday-2026, decisions-api, agents, voice, platform-engineering]
sources:
  - https://openai.com/index/decisions-api/
  - https://developers.openai.com/api/docs/changelog
  - https://developers.openai.com/api/docs/guides/decisions
  - https://developers.openai.com/api/reference/resources/decisions/methods/create
---

# OpenAI Decisions API Is Now Generally Available: 400 Questions, GPT-Live Routing, Caching, and the Case for Faster AI Agents

**Research verified:** October 26, 2026. **Editorial status:** Draft for technical review. **Disclosure:** No live API benchmark was run for this article; reported vendor/customer figures are attributed and should not be treated as independent test results.

## Executive summary

On **October 22, 2026**, OpenAI moved the **Decisions API** to general availability (GA). The API was first presented at DevDay on September 29 and opened in public beta on October 6; the new event is **GA plus material capability changes**, not the initial unveiling. Powered by \`gpt-6-luna\`, \`POST /v1/decisions\` answers bounded questions using a predicate probability, selection from explicit options, or a rubric score, rather than producing arbitrary prose.

Since beta, the service has gained **up to 400 questions per request (previously 200)**, shared-input prompt caching, native JSON inputs, question-specific images, native GPT-Live integration, and supported US/EU inference residency. OpenAI says it can be **up to 10× faster** than GPT-6 Luna via the Responses API for these constrained decisions; this is a vendor comparison, not an independently replicated end-to-end application result.

**Practical thesis:** Use Decisions for inexpensive *judgments* inside an agent workflow; keep deterministic policy, permissions, and execution outside the model. Do not confuse a fast decision endpoint with a complete autonomous agent or a security enforcement boundary.

## Verified release timeline

| Date | Event | Significance |
| --- | --- | --- |
| Sep 29, 2026 | Decisions API introduced at OpenAI DevDay | Limited preview and concept introduced. |
| Oct 6, 2026 | Public beta | Developers could begin testing the endpoint. |
| Oct 22, 2026 | General availability | Doubled question limit, caching, JSON, question-specific images, GPT-Live integration, residency support. |
| Oct 26, 2026 | Research verification | No newer Decisions-specific GA replacement found in the official changelog. |

Primary references: [OpenAI DevDay recap](https://openai.com/index/devday-2026-recap/), [OpenAI GA announcement](https://openai.com/index/decisions-api/), [API changelog](https://developers.openai.com/api/docs/changelog).

## What developers can build

The endpoint accepts shared evidence and **1–400 independent questions**. It supports:

1. **Predicate**: probability that a condition is true, e.g. "Is this ticket about a lost account?"
2. **Choice**: select among declared values, e.g. billing, technical, account access, other.
3. **Score**: rate against 2–10 ordered rubric levels, e.g. incident severity.

The API can return a **refusal** for an individual question. Choice and score responses include probabilities and a confidence value; confidence is not a substitute for application-specific calibration. Dependent questions belong in separate calls. Tool-call history may be supplied as context, but **Decisions does not itself execute tools**.

Use cases include customer-support triage, invoice/document classification, agent handoff, voice-agent intent routing, and risk scoring of proposed commands. In high-risk environments, decisions should be advisory to deterministic authorization and approval.

## Pricing, caching, and caveats

OpenAI lists **$0.10 per million uncached input tokens**, **$0.01 per million cached input tokens** for GPT-6 Luna Decisions, and **no separate output-token charge**. Cache *writes* are billed under the applicable GPT-6 Luna caching rates. Regional processing premiums and other applicable modifiers may apply; verify the live pricing page before publishing cost calculations.

Caching reuses an identical eligible **shared input prefix of at least 1,024 tokens**. The \`questions\` content, including question-specific images, is **outside** that reusable prefix. Use \`prompt_cache_key\` to partition reuse where appropriate and inspect \`usage.input_tokens_details.cached_tokens\` and \`cache_write_tokens\`. The Responses API's 12-hour caching option must **not** be assumed to work for Decisions without checking its endpoint-specific reference.

**Cost per successful outcome**, including human review, retry cost, mistakes, and downstream tool execution, is the metric that matters. Low token price alone does not prove lower operational cost.

Official details: [Decisions guide](https://developers.openai.com/api/docs/guides/decisions), [Decisions endpoint reference](https://developers.openai.com/api/reference/resources/decisions/methods/create), [developer announcement](https://community.openai.com/t/decisions-api-is-now-generally-available-with-new-updates/1408934).

## What the speed claims actually mean

OpenAI reports "up to 10× faster" than **GPT-6 Luna using the Responses API**, not ten times faster than every model or full agent workflow. OpenAI's October 22 post includes customer-reported examples:

| Customer | Vendor-published example | Interpretation |
| --- | --- | --- |
| Box | Approximately 5× faster route selection on 30 insurance-certificate cases | Small, specific evaluation; not a universal benchmark. |
| Datadog | 40× faster p95 risk scoring versus available frontier models | Different comparison baseline; workload-specific. |
| Ramp | 4× faster and 30% cheaper cost attribution | Customer-specific workload. |
| Sierra | Under 280 ms p95, about 3× faster and 90% lower cost per call | Compares with its previous approach. |
| Speak | Time to next action 1.4 s → 0.13 s in GPT-Live testing | Product-specific interactive path. |

These are **selected customer statements in OpenAI's release**, not independently audited results. An independent production test should measure latency percentiles, accuracy, calibration, abstention, and total cost with the same labeled workload.

## Opportunity assessment for YouTube

**Primary keyword:** OpenAI Decisions API general availability / GPT-6 Luna Decisions API.

**Recommended angle:** An enterprise-grade routing benchmark with human approval and calibrated thresholds, contrasting \`/v1/decisions\` against \`/v1/responses\` on the *same* labeled dataset. Focus on whether it can safely replace a full model turn for routine routing.

**Evidence of interest:** OpenAI's own introductory video was indexed at roughly 309K views in a third-party video catalog snapshot; an October 8 creator video was indexed at roughly 30K views, and an October 12 practical video at roughly 14K. A Reddit beta discussion had approximately 91 upvotes in its indexed snapshot. October 22 GA generated fresh official and developer-press coverage. These are signals of **existing attention**, not proof of an increasing search trend.

**Creator supply:** Introductory videos already exist, including OpenAI's launch video and at least two independent explainers/demos. Targeted indexed searches did **not** reveal many *post-GA, production-grade, latency-and-calibration* walkthroughs. This is an **underserved subtopic hypothesis**, not a measured count of all YouTube uploads. Competition is **not zero**.

**Audience:** AI engineers, SaaS developers, contact-center/voice-agent builders, DevOps, platform engineers, SREs, and enterprise architects.

**Opportunity confidence:** Medium for audience demand, medium-low for specialist creator scarcity. **Urgency:** High for a differentiated tutorial within 2–4 days; avoid an undifferentiated launch recap.

**Recommended title:** **"OpenAI's Decisions API Is LIVE: 400 AI Decisions in One Call — Faster Than ChatGPT?"**

**Thumbnail:** **"400 DECISIONS / 1 CALL"** and **"10× FASTER?"**

**Opening hook:**

> "OpenAI just moved a very different kind of AI API into general availability. Instead of asking a model to write a paragraph, you ask it to make a bounded decision—pick a route, assign a risk score, or decide whether a human should step in. OpenAI says this can be up to ten times faster than running GPT-6 Luna through its usual Responses API. But does that speed hold up when you add security checks, human approval, and real-world mistakes? Today we're going to build an agent router and measure the result."

## Reproducible lab design (proposed; not executed)

Use a synthetic, labeled dataset of 300–1,000 support tickets with \`ticket_text\`, \`expected_route\`, and \`requires_human_review\`. Compare:

- **Baseline A:** deterministic keywords/regex.
- **Baseline B:** GPT-6 Luna via Responses API with a structured route output.
- **Candidate C:** GPT-6 Luna via Decisions API with the same routing taxonomy.

Keep dataset, prompt intent, region, concurrency, and retry policy fixed. Log p50/p95/p99 end-to-end latency, token usage, per-case cost, accuracy, confusion matrix, false-negative rate for urgent tickets, abstention rate, and percentage escalated to human review. Randomize call order, warm both paths, and report sample sizes and confidence intervals.

### Minimal API call

The following example follows the official Decisions guide; it is a **sample**, not a recorded test result:

\`\`\`bash
curl https://api.openai.com/v1/decisions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-6-luna",
    "input": "I was charged twice for my order.",
    "questions": [{
      "type": "choice",
      "name": "department",
      "instructions": "Which department should handle this complaint?",
      "choices": [
        {"value": "billing", "description": "Payments, invoices, refunds."},
        {"value": "technical", "description": "Problems using the product."},
        {"value": "account", "description": "Login and account access."},
        {"value": "other", "description": "Requests outside these categories."}
      ]
    }]
  }'
\`\`\`

Validate the returned \`answers\` schema, including refusals. Use a **separate policy service** to decide whether a route may trigger any action. Require human approval for refunds, account access changes, security remediations, and other consequential operations.

## Reference architecture

Source diagram: [2026-10-26-decisions-api-enterprise-routing.mmd](../../../architecture/2026-10-26-decisions-api-enterprise-routing.mmd). Design decisions: [2026-10-26-decisions-api-design-decisions.md](../../../architecture/2026-10-26-decisions-api-design-decisions.md).

\`\`\`mermaid
flowchart LR
  U["Ticket / voice input"] --> I["Ingestion and PII minimization"]
  I --> Q["Decision orchestrator"]
  Q --> D["OpenAI Decisions API / gpt-6-luna"]
  D --> V["Schema and threshold validation"]
  V --> P["Deterministic policy / RBAC"]
  P -->|low risk| A["Approved routing queue"]
  P -->|uncertain or high risk| H["Human review"]
  A --> T["Scoped downstream action"]
  H --> T
  Q --> O["Metrics / audit / evals"]
  V --> O
  P --> O
\`\`\`

### Security, privacy, and reliability

- Treat the decision result as **untrusted input**, not authorization.
- Use least-privilege service credentials, scoped actions, and explicit approval for irreversible changes.
- Minimize or mask sensitive data; choose supported residency and data controls only after verifying account eligibility and agreements.
- Handle refusals, malformed outputs, low confidence, provider failures, and timeouts with safe fallback queues.
- Keep an audit trail of model version, question taxonomy version, input hash, response metadata, policy outcome, reviewer decision, and final action; avoid logging raw sensitive content by default.
- Monitor drift and re-calibrate on fresh labeled cases. Validate performance per language, channel, and ticket class.

## Editorial recommendation

**Publish a working benchmark, not a speculative "10× faster" claim.** A 12–16 minute demonstration of Decisions + GPT-Live handoff or a Python/FastAPI routing gateway with realistic approval gates is more differentiated than another launch summary. Do not claim creator scarcity is proven; verify YouTube's current upload results before final publication.

## Sources and evidence ledger

**Primary, verified:**

1. OpenAI (Oct 22, 2026), [Build more responsive apps with the Decisions API](https://openai.com/index/decisions-api/).
2. OpenAI, [API changelog](https://developers.openai.com/api/docs/changelog), Oct 22 entry.
3. OpenAI, [Decisions developer guide](https://developers.openai.com/api/docs/guides/decisions).
4. OpenAI, [Create a decision API reference](https://developers.openai.com/api/reference/resources/decisions/methods/create).
5. OpenAI Developer Community (Oct 22), [GA announcement and pricing](https://community.openai.com/t/decisions-api-is-now-generally-available-with-new-updates/1408934).
6. OpenAI, [DevDay 2026 recap](https://openai.com/index/devday-2026-recap/).

**Secondary / opportunity observations (not exhaustive analytics):**

7. [TechCrunch (Sep 30), Decisions API and bounded-decision market](https://techcrunch.com/2026/09/30/openais-jev-clone-could-help-the-frontier-lab-stop-its-swarming-agents/).
8. [Gadgets360 (Oct 23), GA coverage](https://www.gadgets360.com/ai/news/openai-decisions-api-gpt-6-luna-model-public-release-price-capabilities-12225198).
9. [Reddit r/OpenAI (Oct 7), beta discussion](https://www.reddit.com/r/OpenAI/comments/1wzprt3/decisions_api_is_now_available_in_public_beta/).
10. [Glasp YouTube index, independent October 12 practical video](https://glasp.co/youtube/HMmcdOMlY_E).
11. [Creator-video index, October 8 independent explainer](https://postsingularityinstitute.org/fr/syntheses/UCY8ZKVxXj0FgIvSfeiSDclg_XYX8bTIwKkM/).
12. [OpenAI launch-video index and snapshot](https://lounge.ai/learn/v/7b393172-b89b-4070-948c-f95fd0a7928e).

**Update policy:** Recheck pricing, model availability, caching behavior, and competitor videos before recording or publishing. Correct this article if API behavior changes.
