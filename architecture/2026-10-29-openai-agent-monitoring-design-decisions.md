# Architecture decisions — OpenAI agent misalignment monitoring

**Date:** October 29, 2026  
**Scope:** Synthetic demonstration and production reference pattern for tool-using agents.

## ADR-001 — Keep authorization outside the agent
**Decision:** A dedicated gateway enforces tool allow-lists, scopes and approval requirements.  
**Reason:** A model's self-assessment and asynchronous provider monitoring cannot be the sole control for irreversible actions.  
**Trade-off:** More application logic and latency, lower risk of unauthorized tool dispatch.

## ADR-002 — Treat monitoring as detection, not transaction control
**Decision:** A provider safety alert sets an application-owned pause state; it does not assume provider rollback.  
**Reason:** OpenAI documents asynchronous monitoring and possible completed actions before an alert.  
**Trade-off:** Requires reconciliation and compensation for prior actions.

## ADR-003 — Prefer state-preserving Responses API patterns where supported
**Decision:** For covered models, use documented persisted reasoning, WebSocket or compaction mechanisms when intervention matters.  
**Reason:** Automatic stopping depends on recognizing conversation continuation.  
**Trade-off:** Additional state management; still not a universal stop guarantee.

## ADR-004 — Use signed, idempotent webhook ingestion
**Decision:** Verify webhook signatures, acknowledge promptly, deduplicate event IDs and fetch alert details asynchronously with least-privilege project credentials.  
**Reason:** Webhook delivery may retry; payload contains an alert ID rather than full details.  
**Trade-off:** Adds queue and secrets management.

## ADR-005 — Fail closed on safety-block API errors
**Decision:** Handle HTTP 403 `misalignment_policy_violation` by halting tool dispatch, retaining audit context and escalating for review. Do not auto-retry.  
**Reason:** Replaying a consequential workflow can repeat harm.  
**Trade-off:** Some legitimate tasks require manual restart or remediation.

## ADR-006 — Keep privacy-aware audit trails
**Decision:** Log request IDs, response IDs, tool IDs, policy outcomes and redacted event metadata; avoid storing sensitive transcripts by default.  
**Reason:** Provider alerts may omit details (including ZDR contexts) and do not replace application evidence.  
**Trade-off:** Need careful retention, access control and incident forensics planning.

## ADR-007 — Demonstrate with synthetic alerts only
**Decision:** The public demo replays mock `safety.alert.created` events and mock 403 responses unless authorized live testing is confirmed.  
**Reason:** Avoid unsafe attempts to induce real monitoring or misrepresent capabilities.  
**Trade-off:** Demonstrates integration correctness, not the provider's actual detection recall.

## Production acceptance criteria

- No unauthorized write tool can execute without independent policy approval.
- Duplicate webhook events do not trigger duplicate side effects.
- Safety-block errors halt tool dispatch, including streamed errors.
- Alert-to-local-pause latency is measured and visible.
- A security reviewer can reconstruct which tool calls completed before the alert.
- Clear operator playbooks cover false positives, incident triage and compensating actions.

**References:** [OpenAI technical report](https://openai.com/index/how-we-detect-frontier-model-misalignment/) · [API guide](https://developers.openai.com/api/docs/guides/safety-checks/misalignment-monitoring).
