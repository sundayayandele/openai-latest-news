# Architecture design decisions — ChatGPT dot texting (October 29, 2026)

**Status:** Draft editorial design analysis. The diagram is a **conceptual reference architecture**, not a statement that OpenAI's private implementation uses the exact depicted components. Facts about the provider, eligibility, memory, and approval are drawn from OpenAI documentation.

## ADR-001 — Model messaging as a distinct trust boundary

**Context:** Texts are routed through a third-party delivery provider (Linq). The iMessage/RCS transport is not a basis for claiming that message content is end-to-end encrypted to the dot.

**Decision:** Show a separate third-party relay boundary and avoid transmitting secrets or regulated records in the demonstration.

**Alternatives:** Depict a direct phone-to-OpenAI channel (misleading); omit delivery infrastructure (hides material privacy concern).

**Validation:** Compare official release notes with the current dot help center; test synthetic-only messages.

## ADR-002 — Bind messages to an authenticated identity

**Context:** The product claims one-to-one dot texting. Phone ownership and account identity are separate concepts.

**Decision:** A proposed implementation must validate phone/account enrollment, protect against cross-account access, and avoid treating caller-provided identifiers as proof of authorization.

**Consequences:** More enrollment friction and recovery procedures; stronger isolation.

**Validation:** Attempt synthetic cross-account and stale-identity cases in a mock gateway. This is a recommended test, not a claim about OpenAI's tested internals.

## ADR-003 — Keep consequential approvals inside an authenticated app

**Context:** Dots can invoke connected apps; some actions need explicit approval.

**Decision:** Treat text as an instruction channel, not a general authorization channel. Route high-impact approvals through an authenticated ChatGPT approval flow.

**Consequences:** Additional user step, clearer non-repudiation and scope.

**Validation:** Simulate an update to a synthetic ticket and verify no write occurs before approval.

## ADR-004 — Apply least privilege to shared plugin context

**Context:** Texting shares memory and plugins with the dot in ChatGPT. Broad plugin grants expand the impact of compromised or mistaken instructions.

**Decision:** Limit connectors to task-specific scopes, use separate synthetic accounts for demos, and redact sensitive fields in logs.

**Consequences:** Less convenience, lower blast radius.

**Validation:** Negative tests for forbidden app operations and prompt injection in retrieved content.

## ADR-005 — Distinguish notifications, disconnection, and deletion

**Context:** STOP can halt outgoing texts, but that is not the same as deleting stored dot context.

**Decision:** Document three separate user actions: stop messages, unlink texting, and reset/delete retained dot context according to OpenAI's current controls.

**Consequences:** More precise privacy guidance; avoids false promises.

**Validation:** Review official support documentation before recording.

## ADR-006 — Require evidence labels for live versus simulated demos

**Context:** Texting beta is limited to selected U.S. Pro accounts and not available in Business/Enterprise workspaces.

**Decision:** If eligible access is unavailable, use a mock messaging gateway and prominently label all screenshots, traces, and architecture diagrams as illustrative.

**Consequences:** No false claim of access; narrower performance conclusions.

**Validation:** Preserve source links, record the account/region eligibility if testing, and publish a correction if rollout terms change.

## Evidence sources

- https://help.openai.com/en/articles/6825453-chatgpt-release-notes
- https://help.openai.com/en/articles/20001530-getting-started-with-your-dot
- https://help.openai.com/en/articles/20001529-dots-privacy-security-and-safety-faqs
- https://learn.chatgpt.com/docs/whats-new/dots-october-28-2026
- https://windowsreport.com/openai-now-allows-texting-your-chatgpt-dots-but-messages-arent-end-to-end-encrypted/
