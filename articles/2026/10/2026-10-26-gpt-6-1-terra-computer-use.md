---
title: "GPT-6.1 Terra: OpenAI's Cheaper Computer-Use Model—Architecture, Benchmarks, Security and a Practical Evaluation Plan"
date: 2026-10-26
last_verified: 2026-10-26
status: draft
topics: [openai, gpt-6-1-terra, agents, computer-use, architecture, model-evaluation]
---

# GPT-6.1 Terra: OpenAI's Cheaper Computer-Use Model—Architecture, Benchmarks, Security and a Practical Evaluation Plan

**Published for editorial review: 26 October 2026.** This report distinguishes OpenAI's own benchmark and safety claims from independent validation. Prices and availability may change.

## Executive summary

OpenAI announced **GPT-6.1 Terra** on **26 October 2026**, with the API identifier `gpt-6.1-terra`. It is designed for visual reasoning, computer interaction, coding and everyday professional work. It is available through the API and to eligible Plus, Pro, Enterprise and Edu users in ChatGPT Work and Codex. Standard API prices are **$1 per million input tokens**, **$0.05 per million cached input tokens** and **$5 per million output tokens**. OpenAI describes the standard input/output prices as **50% lower** than both GPT-5.6 Terra and GPT-6.1 Sol. [1][2]

This is a new model launch, not simply a refreshed UI for the older Terra. The important technical question is whether lower per-token cost also yields lower **cost per successful, supervised task** in real agent workflows. OpenAI reports significant improvements on computer-use, visual-reasoning, factuality and coding benchmarks, but these are vendor-reported results and should be tested against your own workloads.

## What changed, exactly?

| Dimension | Verified announcement |
|---|---|
| Release date | 26 October 2026 |
| Model ID | `gpt-6.1-terra` |
| API | Responses API and Chat Completions listed in the developer changelog |
| Focus | Visual reasoning, precise computer use, clear writing, coding |
| Price | $1/M input, $0.05/M cached input, $5/M output |
| Availability | API; eligible ChatGPT Work and Codex plans |
| Security | OpenAI says it meets the Critical cybersecurity capability threshold; the system card should be reviewed before deployment |

**Do not confuse the capability to reason about computer interfaces with unrestricted authority to operate arbitrary systems.** Computer-use applications require a tool/runtime integration, permissions, policy controls, and a well-defined approval model.

## Benchmark results: vendor claims, not an independent bake-off

OpenAI reports that at medium reasoning effort, Terra improves **15 percentage points** over GPT-5.6 Terra on *Agents' Last Exam* at approximately one-third of the cost. It reports near-GPT-6 Astra results on the same benchmark at roughly one-tenth the per-token price. OpenAI also reports improvements on MMMU-Pro visual reasoning and OSWorld 2.1 desktop-task evaluations. [1]

For coding, OpenAI reports **50.3%** on Terminal-Bench 4.0 at maximum reasoning effort, versus a published **21.5%** for GPT-5.6 Terra. On an internal error-inducing-prompt benchmark, the error rate at low effort reportedly falls from **53.6% to 16.7%**. These figures are not guarantees of comparable performance in production. Benchmark harnesses, reasoning budgets, tool environments and scoring rules matter. [1]

OpenAI says Terra meets the **Critical** cybersecurity capability threshold under its Preparedness Framework. Treat this as a reason to enforce strong boundaries, not as evidence that the model is inherently safe to grant broad execution privileges. [1]

## Economics: the metric that matters

At standard prices, a workload consuming **10 million uncached input tokens and 5 million output tokens** costs approximately **$35** on Terra (10 × $1 + 5 × $5), compared with **$70** at the stated GPT-6.1 Sol standard rates (10 × $2 + 5 × $10). This simple example excludes caching, tool costs, retries, long-context repricing, tier differences, taxes and failed tasks. [1]

**Measure cost per successful task** rather than token price alone:

`cost_per_success = total_model_and_tool_cost / number_of_correctly_completed_tasks`

A cheaper model that needs more retries or escalations may not be cheaper overall. Measure latency percentiles, failure rates, human intervention, safety exceptions and token consumption at a fixed quality threshold.

## Architecture: supervised visual-to-action agent

See the editable Mermaid source at [architecture/2026-10-26-gpt-6-1-terra-agent-gateway.mmd](../../../architecture/2026-10-26-gpt-6-1-terra-agent-gateway.mmd).

The recommended reference design uses a **policy-enforcing gateway** between business requests and model-driven actions:

1. A client submits a bounded task (for example, extracting an invoice and entering it into a test accounting application).
2. An identity and policy gateway authenticates the requester, authorizes allowed applications, and issues a short-lived task scope.
3. A task orchestrator sends context and screenshots to the model, records a proposed action, and validates it against an allowlist.
4. A **separate** computer-use driver executes only approved actions inside an isolated environment.
5. High-impact actions (payments, record deletion, privilege changes, outbound messages) require human approval.
6. A verifier independently checks application state and records a structured outcome.
7. Audit logs and metrics capture action traces with appropriate redaction and retention.

The model must not hold raw production credentials or be the final authority for irreversible actions. Separate read-only exploration from write-capable execution and set strict task budgets.

## Reproducible evaluation: first validate the API, then the agent

### Step 1: API smoke test

This minimal Responses API request checks model availability and basic text response behavior. It **does not** test computer-use capabilities:

```bash
export OPENAI_API_KEY="your-key-from-a-secret-manager"
curl -sS https://api.openai.com/v1/responses \
  -H "Authorization: Bearer ${OPENAI_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-6.1-terra",
    "input": "Return a short JSON checklist for validating a scanned invoice."
  }'
```

Do not place real secrets in source control, terminal recordings or published screenshots.

### Step 2: Build a safe benchmark

Use a sandboxed, non-production web application with synthetic invoices, invoices with deliberately confusing layouts, and controlled workflows. Include both text-only and screenshot-based tasks. Fix the same tasks and environment for every model under comparison.

Record: task success, precision of extracted fields, number of UI actions, unauthorized action attempts, latency p50/p95, retries, token use, dollar cost, and intervention rate. Compare GPT-6.1 Terra against GPT-5.6 Terra and GPT-6.1 Sol at documented reasoning settings. Independently review a sample of outputs.

### Step 3: Exercise security controls

Run benign tests for prompt-injection resistance in invoice text; reject attempts to change the payment recipient, export data or access unrelated records. Verify that the policy layer blocks prohibited actions **even if the model requests them**. Keep all tests authorized and isolated.

### Step 4: Publish measurements responsibly

Report model versions, run dates, dataset size, hardware and browser conditions, temperature/reasoning settings if applicable, sample counts and confidence intervals. Clearly label OpenAI-reported benchmarks separately from measurements from your own test environment.

## Design decisions

See [architecture/2026-10-26-gpt-6-1-terra-decisions.md](../../../architecture/2026-10-26-gpt-6-1-terra-decisions.md) for alternatives and trade-offs. In short: use an API gateway; isolate the computer-use runtime; require human approval for sensitive actions; measure end-to-end task cost; maintain a fallback to a more capable model for low-confidence outcomes.

## YouTube opportunity assessment (26 October 2026)

**Primary keyword:** GPT-6.1 Terra computer use.

**What's new:** Official model release on 26 October with reduced prices, reported visual/computer-use improvements, and API/Codex availability. [1][2]

**Evidence of demand:** Same-day coverage by 9to5Mac and The Next Web; this establishes fresh editorial interest, not measured search-volume growth. [3][4]

**Creator supply:** Targeted indexed YouTube searches for exact-model hands-on computer-use comparisons yielded few clearly identifiable *post-release technical walkthroughs*. Earlier speculative videos about Terra already exist. Search indexing is incomplete; this is an **early-window hypothesis**, not a measured upload-count or keyword-difficulty score. [5]

**Audience:** AI engineers, platform/DevOps teams, SaaS builders, workflow automation practitioners, and enterprise architects.

**Recommended title:** **GPT-6.1 Terra Just Launched: Can a Cheaper AI Agent Actually Use Your Computer?**

**Thumbnail:** **50% CHEAPER** / **REAL COMPUTER TEST**

**Opening hook:** “OpenAI just released GPT-6.1 Terra, promising stronger visual reasoning and computer-use capabilities at half the standard token price of its previous Terra model. But cheaper tokens don't automatically mean cheaper automation. Today we'll test it on a controlled invoice workflow, compare task success and total cost, and show the security controls you need before giving any AI agent access to business software.”

**Urgency:** **High — publish within 24–48 hours** if a working demonstration can be completed. If no real benchmark is ready, publish a clearly labeled first-look analysis instead of claiming hands-on results.

## References

1. OpenAI, *Introducing GPT-6.1 Terra*, 26 October 2026: https://openai.com/index/introducing-gpt-6-1-terra/
2. OpenAI developer changelog, 26 October 2026: https://developers.openai.com/api/docs/changelog
3. 9to5Mac, *OpenAI upgrades ChatGPT and Codex with new GPT-6.1 Terra model*, 26 October 2026: https://9to5mac.com/2026/10/26/openai-upgrades-chatgpt-and-codex-with-new-gpt-6-1-terra-model/
4. The Next Web, *OpenAI launches GPT-6.1 Terra, a cheaper model for everyday computer work*, 26 October 2026: https://thenextweb.com/news/openai-gpt-6-1-terra-computer-use
5. ByteForward, pre-release Terra discussion, 20 October 2026 (evidence that coverage existed *before* launch, not proof of post-launch competition): https://chooseto.ai/watch/9OaX1uBk_8o

*Editorial note:* All benchmark figures are attributed to OpenAI. The article intentionally does not assert that a production computer-use demo was executed or that YouTube search volume has been measured.
