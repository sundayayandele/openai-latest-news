# Design decisions — OpenAI Decisions API enterprise routing

**Date:** 2026-10-26 · **Status:** Proposed for lab evaluation, not production-certified.

## ADR-001: Use Decisions for bounded judgments, not tool execution

**Context:** A contact-center agent needs fast classification and routing, not an open-ended text response at every step.

**Decision:** Use `/v1/decisions` for `predicate`, `choice`, and `score` tasks. Retain Responses API for generated explanations, extraction requiring complex schemas, and tool calls.

**Alternatives:** Full Responses API for every route; local deterministic classifiers; specialized external decision models.

**Consequences:** Potential latency/cost improvement, but narrower semantics and a second endpoint to maintain. Evaluate both routes against the same labeled dataset.

**Validation:** Measure end-to-end p50/p95/p99, macro-F1, calibration, per-task cost, and escalation rates.

## ADR-002: Separate model recommendations from authority

**Context:** A high-confidence classification can still be wrong or adversarially influenced.

**Decision:** Validate schema and confidence; enforce business policy and RBAC in a separate deterministic service. Require human approval for consequential changes (refunds, account resets, production actions).

**Alternatives:** Execute directly on model output; ask a second model to authorize.

**Consequences:** Additional latency but stronger auditability and blast-radius control.

**Validation:** Test prompt injection, ambiguous inputs, false-positive routing, policy-denied actions, and safe fallbacks.

## ADR-003: Batch only independent questions and cache shared evidence

**Context:** GA allows up to 400 questions/request and caching of eligible shared input prefixes.

**Decision:** Batch independent questions against the same evidence; use separate calls for dependencies. Cache identical shared prefixes at or above 1,024 tokens when economic; avoid assuming question text is cached. Partition with `prompt_cache_key` to reduce accidental cross-workload reuse.

**Alternatives:** One call per question; cache all request content; unbounded batching.

**Consequences:** May reduce request overhead, but batching increases coupling and may amplify single-request failures. Cache writes are charged; savings require measured reuse.

**Validation:** Test question-count boundaries, refusal handling, cache read/write usage, TTL support, and cost break-even.

## ADR-004: Keep data-minimization and residency controls explicit

**Context:** Support tickets and voice conversations may contain personal and sensitive information.

**Decision:** Minimize payloads before API calls, avoid raw PII in logs, use scoped API keys, and confirm ZDR/HIPAA/residency eligibility contractually before use. Use US/EU inference processing only where configured and supported.

**Alternatives:** Forward full records and rely only on model/provider controls.

**Consequences:** More preprocessing and potential loss of useful context; improved privacy posture.

**Validation:** Data-flow review, redaction tests, key-scoping tests, retention review.

## ADR-005: Instrument accuracy and cost per successful workflow

**Context:** Vendor-reported 10× speed and customer outcomes are not guaranteed for another workload.

**Decision:** Run a repeatable evaluation across keyword baseline, Responses API and Decisions API with the same synthetic labeled tickets. Record p95, F1, confusion matrices, calibration, human escalation, retry rate, and cost per correct outcome.

**Alternatives:** Compare only vendor headline numbers or per-token prices.

**Consequences:** Slower adoption decision but more reliable economic and safety assessment.

**Validation:** Repeat tests over multiple runs and workloads; publish uncertainty intervals and dataset description.

## Architecture source

[Editable Mermaid diagram](2026-10-26-decisions-api-enterprise-routing.mmd)

## References

- https://openai.com/index/decisions-api/
- https://developers.openai.com/api/docs/guides/decisions
- https://developers.openai.com/api/reference/resources/decisions/methods/create
- https://developers.openai.com/api/docs/changelog
