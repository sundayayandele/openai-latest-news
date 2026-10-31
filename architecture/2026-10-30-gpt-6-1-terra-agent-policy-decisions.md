# Architecture decisions — GPT-6.1 Terra agent authorization evaluation

**Date:** 2026-10-30  
**Related article:** [Research article](../articles/2026/10/2026-10-30-gpt-6-1-terra-agent-security-cost.md)  
**Diagram:** [Mermaid architecture](./2026-10-30-gpt-6-1-terra-agent-policy-evaluation.mmd)

## ADR-01 — Model proposes; application authorizes
**Context:** Model outputs can be influenced by untrusted documents or peer-agent messages.  
**Decision:** All tool proposals pass an independent principal/action/resource policy check. Never let a model set its own authorization.  
**Alternatives:** Prompt-only rules, unrestricted tool execution. **Rejected** as insufficient enforcement.  
**Validation:** Unauthorized synthetic ticket update is denied even if model requests it.

## ADR-02 — Use mock systems and synthetic fixtures
**Context:** We need a reproducible public demonstration without exposing customers or production systems.  
**Decision:** FastAPI and synthetic ticket/invoice data; no real credentials or enterprise SaaS writes.  
**Consequence:** Demonstrates architecture, not real vendor integration behavior.  
**Validation:** Fixtures contain no production secrets and the demo runs offline except for optional model API calls.

## ADR-03 — Compare accepted-task economics, not just token price
**Context:** A cheaper model can incur more retries and human review.  
**Decision:** Track task accuracy, approved outcomes, retries, model spend, tool costs and review effort.  
**Alternative:** Price per million tokens alone. **Rejected** as misleading for agentic workflows.  
**Validation:** Compute cost per accepted task with denominators and sample sizes visible.

## ADR-04 — Make write operations approval-gated
**Context:** Ticket updates and payments are consequential side effects.  
**Decision:** Require explicit user approval for write operations; default to read-only.  
**Consequence:** Added latency but reduced unapproved changes.  
**Validation:** Verify both approved and denied transitions are recorded.

## ADR-05 — Treat retrieved text and agent messages as untrusted
**Context:** External content may contain instructions that should not become system or developer commands.  
**Decision:** Preserve provenance and trust levels; pass retrieved content as data, not policy.  
**Consequence:** Some legitimate-looking suggestions will require explicit validation.  
**Validation:** Test benign out-of-scope instructions and confirm no policy bypass.

## ADR-06 — Keep model and application safety metrics distinct
**Context:** OpenAI's system-card 29.6% and 13.75% results use controlled test conditions.  
**Decision:** Do not label those figures as real-world incident rates; measure our own app-specific proposal and enforcement rates.  
**Consequence:** More nuanced but accurate publication.  
**Validation:** Every figure has a dataset, denominator, environment, and limitations.

## ADR-07 — Auditable and reversible execution
**Context:** Operators need incident review and rollback.  
**Decision:** Append-only audit events, idempotency keys, dry-run preview, and compensating actions for reversible mock writes.  
**Consequence:** Extra storage and workflow complexity.  
**Validation:** Replay a test run from audit events and demonstrate a rollback.

## ADR-08 — No claims of live testing without credentials and results
**Context:** The model API may be unavailable during recording.  
**Decision:** Label any offline demonstration as simulated; never invent benchmark outcomes.  
**Validation:** Publish the exact code, configuration, sample count and logs for any claimed real measurement.
