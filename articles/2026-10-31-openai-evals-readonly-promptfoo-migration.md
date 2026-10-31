# OpenAI Evals Becomes Read-Only Today: How to Rescue Your AI Tests Before November 30

**Research date:** October 31, 2026  
**Status:** Editorial draft, not a tested customer migration  
**Opportunity:** HIGH (8.6/10); publish October 31–November 2  
**Primary keywords:** OpenAI Evals read-only October 31 2026; OpenAI Evals to Promptfoo migration  
**Secondary keywords:** Agent Builder shutdown, AI eval CI/CD, OpenAI graders, prompt regression testing, DevDay agent migration

## Executive summary

OpenAI's **June 3, 2026 deprecation notice reaches its first operational deadline today, October 31, 2026**: existing hosted Evals are scheduled to become **read-only**. The hosted Evals dashboard and API are scheduled to shut down on **November 30, 2026**. The same November 30 deadline applies to Agent Builder and reusable prompt objects via `/v1/prompts`. ChatKit remains available. This is a scheduled lifecycle milestone, **not a newly announced October 31 product**, and we have not accessed a customer's account to verify read-only enforcement.

OpenAI's official migration cookbook recommends **Promptfoo** for replacing Evals. This is a **manual recreation of prompts, datasets, provider settings and assertions**, not a guaranteed one-click import. Developers with production test suites should preserve their definitions, test data, run outputs, grading criteria and access controls before the November 30 shutdown.

**Do not conflate** the hosted Evals platform with the separate open-source `openai/evals` GitHub repository. Likewise, ChatKit continuing does not mean an Agent Builder-hosted backend will continue after its sunset.

## What changed and when

| Date | Product | Verified vendor schedule | Developer implication |
|---|---|---|---|
| June 3, 2026 | Hosted Evals | Deprecation announced | Start migration planning |
| **October 31, 2026** | **Hosted Evals** | **Existing evals become read-only** | Stop relying on new/modified hosted evals |
| **November 30, 2026** | **Evals dashboard/API** | **Scheduled shutdown** | Save definitions, run results and datasets before loss of access |
| November 30, 2026 | Agent Builder | Scheduled shutdown | Rebuild in Agents SDK or ChatGPT Workspace Agents |
| November 30, 2026 | Reusable prompts (`/v1/prompts`) | Scheduled shutdown | Move prompts into application-managed source/version control |
| After November 30 | ChatKit | Remains available | Review whether your current integration depends on retired hosted workflows |

Sources: [OpenAI deprecations](https://developers.openai.com/api/docs/deprecations), [OpenAI Evals guide](https://developers.openai.com/api/docs/guides/evals), [OpenAI's migration cookbook](https://developers.openai.com/cookbook/examples/evaluation/moving-from-openai-evals-to-promptfoo).

## Why this is a YouTube opportunity

**Demand signals (observed):** OpenAI's official docs state an imminent production-impacting deadline. A dedicated [OpenAI Developer Community checklist posted October 30](https://community.openai.com/t/nov-30-shutdown-checklist-agent-builder-evals-prompt-objects-evals-go-read-only-oct-31/1411075) asks about migration experiences, while an [October 27 DEV Community explainer](https://dev.to/evalsreview/openais-hosted-evals-goes-read-only-on-31-october-what-to-export-and-where-to-move-3mni) and earlier third-party migration articles show timely educational interest. These are **qualitative signals**, not measured YouTube keyword-volume growth.

**Supply signals (observed):** Targeted indexed searches on October 31 for YouTube videos containing the exact phrases "OpenAI Evals read-only October 31", "OpenAI Evals to Promptfoo 2026", and "OpenAI Evals shutdown migration" did not surface a dedicated end-to-end current migration walkthrough. Existing general LLM evaluation tutorials and written migration guides compete indirectly. Indexed search is incomplete; do **not** claim zero YouTube videos exist.

**Demand confidence:** Moderate (hard deadline and community discussion). **Creator-supply confidence:** Low–moderate (indexed-search signal only). **Audience:** AI engineers, API developers, DevOps/MLOps engineers, platform teams, SaaS founders and technical educators.

## YouTube production package

**High-CTR title:** "OpenAI Evals Goes READ-ONLY Today — Save Your AI Tests Before November 30!"

**Alternate title:** "OpenAI Is Shutting Down Evals & Agent Builder: Migrate to Promptfoo + Agents SDK"

**Thumbnail:** `EVALS: READ-ONLY!` / `NOV 30 SHUTDOWN`

**Opening hook:**

> "If your team uses OpenAI's hosted Evals dashboard, today is a date you shouldn't ignore. OpenAI's own deprecation schedule says existing evals become read-only on October 31, and the Evals dashboard and API are scheduled to disappear on November 30. Agent Builder and reusable prompts also face the November deadline. I'll show you what to preserve, how to rebuild a real evaluation with Promptfoo, and how to run it in CI so your AI quality checks don't vanish with the hosted platform."

**Suggested video flow (10–14 minutes):** 0:00 deadline and scope; 0:45 official timeline; 2:00 what survives and what does not; 3:30 inspect sample Evals definitions; 5:00 rebuild a simple test in Promptfoo; 8:00 run and inspect assertions; 10:00 GitHub Actions integration concept; 12:00 migration checklist and caveats.

**Urgency:** HIGH, publish October 31–November 2. The underlying announcement is older, but the read-only milestone **arrives today** and the full sunset is one month away.

## Practical migration guide (safe, reproducible)

### Step 1: Inventory the hosted evaluation estate

For each project, record eval IDs, definitions, data-source configuration, test-case fixtures, graders, run IDs, results, prompt versions, model names and owners. Preserve all relevant metadata for traceability. Review the [Evals API reference](https://developers.openai.com/api/reference/python/resources/evals) and [list endpoint](https://developers.openai.com/api/reference/resources/evals/methods/list). API pagination matters: collect every page, not just the default 20 items.

Illustrative **read-only** REST calls (requires a valid project key and authorized access):

```bash
# Never print or commit your real API key.
curl -sS -H "Authorization: Bearer $OPENAI_API_KEY" \
  https://api.openai.com/v1/evals

# For each known eval_id, collect its definition and runs:
curl -sS -H "Authorization: Bearer $OPENAI_API_KEY" \
  "https://api.openai.com/v1/evals/$EVAL_ID"
curl -sS -H "Authorization: Bearer $OPENAI_API_KEY" \
  "https://api.openai.com/v1/evals/$EVAL_ID/runs"
# For each run, GET /v1/evals/{eval_id}/runs/{run_id}/output_items
```

These examples are **not** a full exporter: pagination, error handling, API permissions, rate limits and private dataset/file exports require additional handling. Confirm read access still works in your organization after the scheduled transition.

### Step 2: Rebuild a representative eval in Promptfoo

Create a minimal synthetic customer-support classification test in `promptfooconfig.yaml`:

```yaml
description: "Synthetic support triage regression checks"
prompts:
  - "Classify this support message as billing or technical. Return only one lowercase word: {{message}}"
providers:
  - openai:gpt-6-luna
tests:
  - vars:
      message: "My invoice was charged twice"
    assert:
      - type: equals
        value: billing
  - vars:
      message: "The application crashes on login"
    assert:
      - type: equals
        value: technical
```

Then:

```bash
npx promptfoo@latest validate config -c promptfooconfig.yaml
npx promptfoo@latest eval -c promptfooconfig.yaml --no-cache
npx promptfoo@latest view
```

The configuration follows [Promptfoo's configuration guide](https://www.promptfoo.dev/docs/configuration/guide/) and [OpenAI's migration cookbook](https://developers.openai.com/cookbook/examples/evaluation/moving-from-openai-evals-to-promptfoo). **Not executed during this research.** Run it with a suitable API key and confirm that the selected model is available to your organization. Expect billable API usage.

### Step 3: Validate parity before relying on CI

Map old Evals grading criteria to Promptfoo assertions. For subjective or LLM-as-judge graders, validate against a manually reviewed gold set; scores may not match numerically. Check edge cases, safety prompts, cost per run, model drift, retry behavior and threshold decisions. Do not silently change the test population while migrating.

### Step 4: Move to controlled CI/CD

Version `promptfooconfig.yaml` and fixtures in Git, restrict secrets to CI secret stores, run the suite on PRs or a controlled schedule, and retain machine-readable results as build artifacts. Set cost ceilings and use approval gates before promotion. For high-risk changes, maintain a human review path for failures.

### Step 5: Separate other November 30 migrations

**Agent Builder:** inspect workflow nodes, tool permissions, hosted workflow IDs, ChatKit integration and state. Use the [official Agent Builder migration guide](https://developers.openai.com/api/docs/guides/agent-builder/migrate-from-agent-builder) if available for your environment; the authoritative entry point is linked from [OpenAI deprecations](https://developers.openai.com/api/docs/deprecations). An SDK code export may not preserve all visual workflow semantics, so regression-test.

**Reusable prompt objects:** move the prompt text, variables and version history into application-managed code/configuration. Verify no production path still calls the retiring `/v1/prompts` API.

**What remains:** Responses API, Agents SDK and ChatKit are not declared retired by these notices. Do not imply that all OpenAI evaluation techniques or all datasets are disappearing.

## Architecture summary

See [editable Mermaid architecture](../diagrams/2026-10-31-openai-evals-to-promptfoo.mmd) and [architecture decisions](../architecture/2026-10-31-openai-evals-migration-decisions.md). The recommended pattern is: preserve legacy evidence; define portable eval fixtures in Git; run Promptfoo in an isolated CI job; call the provider with least-privilege credentials; archive reports; require quality-gate approval for deployment.

## Accuracy and editorial checklist

- [ ] Read-only date **scheduled for Oct 31**, not independently verified against a customer tenant.
- [ ] Nov 30 shutdown **scheduled**, not yet occurred.
- [ ] Evals migration is a **manual rebuild**, not a guaranteed automated export.
- [ ] No real API keys, customer datasets or production projects used.
- [ ] Verify CLI/config compatibility and model availability before screen recording.
- [ ] Creator supply is an indexed-search observation, **not** an upload count.
- [ ] No claim that ChatKit itself is shutting down.

## References

1. [OpenAI: API deprecations](https://developers.openai.com/api/docs/deprecations) — authoritative dates and product scope.
2. [OpenAI: Moving from OpenAI Evals to Promptfoo](https://developers.openai.com/cookbook/examples/evaluation/moving-from-openai-evals-to-promptfoo) — official migration recipe.
3. [OpenAI: Working with Evals](https://developers.openai.com/api/docs/guides/evals) — hosted Evals behavior and transition banner.
4. [OpenAI: Evals API reference](https://developers.openai.com/api/reference/python/resources/evals) — read-only endpoints and runs.
5. [Promptfoo: configuration guide](https://www.promptfoo.dev/docs/configuration/guide/) — supported YAML/test/assertion syntax.
6. [OpenAI Developer Community: Oct 30 shutdown checklist](https://community.openai.com/t/nov-30-shutdown-checklist-agent-builder-evals-prompt-objects-evals-go-read-only-oct-31/1411075) — qualitative demand signal, not official policy.
7. [DEV Community: Oct 27 migration explainer](https://dev.to/evalsreview/openais-hosted-evals-goes-read-only-on-31-october-what-to-export-and-where-to-move-3mni) — independent community interest.
