# Design decisions: GPT-6.1 Terra supervised computer-use gateway

**Date:** 2026-10-26 · **Status:** Proposed, not production-certified

## ADR-01: Enforce policy outside the model
**Context:** A model can be manipulated by instructions in screenshots or documents.
**Decision:** Use a deterministic gateway with identity, scope, application allowlists, action validation and rate limits.
**Alternatives:** Model-only instructions; direct UI tool access.
**Consequences:** Additional engineering and latency, but denial does not depend on model compliance.
**Validation:** Simulated prohibited-action tests must be blocked.

## ADR-02: Isolate the computer-use runtime
**Decision:** Run UI automation in a short-lived sandbox with no ambient production credentials, minimal filesystem access, restricted egress and isolated synthetic data.
**Alternatives:** Shared desktop session; direct production browser.
**Consequences:** Higher infrastructure overhead and a need to provision fixtures.
**Validation:** Verify sandbox reset, network denials and no cross-task state leakage.

## ADR-03: Human approval for irreversible actions
**Decision:** Gate payments, deletion, privilege changes, and external communications on explicit approval and independently verified task scope.
**Alternatives:** Unsupervised execution; post-hoc review only.
**Consequences:** Slower high-impact workflows, but more defensible risk posture.
**Validation:** Test rejection and timeout behavior.

## ADR-04: Optimize cost per successful task, not price per token
**Decision:** Compare Terra, the preceding Terra and Sol using fixed synthetic tasks, equalized evaluation budgets, p95 latency, accuracy, retries, total model/tool cost and human interventions.
**Alternatives:** Marketing benchmark comparison alone.
**Consequences:** More measurement work; results reflect local workloads.
**Validation:** Repeat runs with a fixed dataset and publish methodology.

## ADR-05: Route uncertain cases to a stronger model or human
**Decision:** Escalate low-confidence or failed verification results rather than retry indefinitely.
**Alternatives:** Unlimited self-correction.
**Consequences:** Higher cost on hard cases; bounded error propagation.
**Validation:** Evaluate escalation thresholds against held-out tasks.

## References
- https://openai.com/index/introducing-gpt-6-1-terra/
- https://developers.openai.com/api/docs/changelog
