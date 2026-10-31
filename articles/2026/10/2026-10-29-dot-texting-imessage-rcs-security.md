---
title: "OpenAI Dots Can Now Text You: iMessage, RCS, Agent Approvals and the Security Boundary"
date: 2026-10-29
last_verified: 2026-10-29
status: draft
topics: [openai, devday-2026, dots, agents, imessage, rcs, security, privacy, youtube]
sources:
  - https://help.openai.com/en/articles/6825453-chatgpt-release-notes
  - https://help.openai.com/en/articles/20001530-getting-started-with-your-dot
  - https://help.openai.com/en/articles/20001529-dots-privacy-security-and-safety-faqs
  - https://learn.chatgpt.com/docs/whats-new/dots-october-28-2026
---

# OpenAI Dots Can Now Text You: iMessage, RCS, Agent Approvals and the Security Boundary

**Research verified:** October 29, 2026. **Announcement:** October 28, 2026. **Editorial recommendation:** publish an explanatory security-and-product video within 24–48 hours; do not claim a hands-on test without eligible account access.

## Executive summary

OpenAI has started rolling out **texting for its persistent ChatGPT dot agents**. Eligible **U.S. ChatGPT Pro** users can reach their dot using **iMessage on iPhone or RCS on Android**. They can send tasks, photos, and voice notes, while the dot can proactively text relevant updates. The dot retains the same memory and plugin context across the ChatGPT app and texting. The message path uses a third-party provider, **Linq**, which is an important trust boundary.

This is **not** the same as ChatGPT reading a user's existing message threads. OpenAI states that only the user can text the dot, only the dot can text that user, and the feature does not grant access to other conversations. The beta is not available in Business or Enterprise workspaces, and OpenAI's help documentation says even U.S. Pro access is limited. There is no announced broader rollout date.

**Editorial distinction:** This is a new distribution channel for an existing DevDay agent product, not a new OpenAI API, new foundation model, or generally available enterprise feature.

## Verified release chronology

| Date | Development | Source |
| --- | --- | --- |
| September 29, 2026 | OpenAI introduces persistent dots at DevDay 2026. | OpenAI release notes and DevDay recap |
| October 28, 2026 | OpenAI release notes add the texting beta for U.S. Pro users; shared memory/plugins, proactive updates, and Linq provider disclosed. | OpenAI release notes |
| October 28, 2026 | 9to5Mac, Engadget, and Digital Trends publish launch coverage. | Independent reports |
| October 29, 2026 | Additional follow-on reporting appears; no independent YouTube search-volume figures verified. | Gadgets360 and indexed search |

## What changed technically

1. **New channel:** An eligible dot can receive text, photo, and voice-message tasks through a phone's messaging app using iMessage or RCS, rather than requiring the ChatGPT app.
2. **Proactive notifications:** The dot can send task updates without waiting for a new inbound message.
3. **Shared state:** The texting conversation draws on the dot's existing context, memory, and connected plugins.
4. **Approval continuity:** When an action requires approval, OpenAI's documentation says the dot sends a link to review the request in ChatGPT. A text message is not itself a blanket authorization to perform sensitive actions.
5. **External messaging hop:** Linq sends and receives the dot's messages on behalf of OpenAI. The provider is part of the data path.
6. **Eligibility:** Limited beta for U.S. Pro users; not available to Business or Enterprise workspaces. Availability is gradual and not guaranteed for every U.S. Pro account.

**Do not confuse** the dot texting beta with the separate Apple Messages plugin, which has different device and permission requirements, or with a generic SMS bot. The launch specifically identifies iMessage and RCS; do not promise conventional SMS support.

## Security and privacy analysis

### Boundary 1: Phone number and message-channel identity

OpenAI states that only the linked user can message their dot and the dot can message only that user. This is a vendor-stated product property, not a public audit of all identity-verification failure modes. A defensive evaluation should verify phone-number enrollment, lost-phone scenarios, number recycling, and whether approvals remain protected by authenticated ChatGPT sessions.

### Boundary 2: Third-party message relay

OpenAI identifies Linq as the third-party provider for delivery. An iMessage or RCS transport label must not be used to imply end-to-end encryption **all the way to the AI agent**: an intermediary processes the message for the service. A separate report by Windows Report explicitly discusses this distinction. Treat message content as potentially sensitive and avoid including passwords, one-time codes, regulated records, or confidential company data in a demonstration.

OpenAI reports extensive penetration testing by frontier models and third-party security firms. That is an OpenAI claim, not a substitute for a public security assessment of this integration.

### Boundary 3: Memory and plugin actions

The same context and plugins can be used from the app and from texting. This improves usability but also increases the consequences of mistaken instructions, prompt injection from connected data, and over-broad plugin grants. Review plugin permissions, dot custom rules, approval settings, and audit history before enabling high-impact workflows.

OpenAI documents restrictions on proactive research and checks before sensitive actions. These controls reduce risk but do not make agents infallible.

### Boundary 4: Approval and auditability

For actions requiring approval, redirect users to an authenticated ChatGPT approval screen. Do not design a simulated demo that treats a plain text "yes" as universal authorization for money movement, password changes, or deletion. Log request identifiers, allowed scopes, approval events, and outcome status without logging message bodies unnecessarily.

### Boundary 5: Data retention and control

Dot conversations and memories follow the product's documented retention and memory controls; turning off a connection does not necessarily erase previously incorporated context. OpenAI's help documentation says replying STOP halts outgoing texts; resetting the dot is a different operation. Distinguish stopping notifications, unlinking a channel, and deleting retained context.

## YouTube opportunity assessment

**Primary keyword:** ChatGPT dot texting iMessage RCS

**Secondary keywords:** OpenAI dots texting setup; ChatGPT dot privacy; AI agent iMessage; RCS AI agent; Linq OpenAI security; ChatGPT proactive agent; DevDay dots.

**Demand signals:** The October 28 announcement was described by OpenAI as "by popular request." 9to5Mac, Engadget, and Digital Trends covered it on launch day, with Gadgets360 following on October 29. That supports fresh public attention and a practical user question: how to set it up safely. It does **not** establish rising YouTube keyword search volume; no trustworthy trend series was available during this scan. **Demand confidence: moderate.**

**Supply signals:** Searches on October 29 for exact YouTube terms combining "text your dot", "ChatGPT dots" + "RCS", and "iMessage" + "dot" surfaced general dot setup videos and unrelated messaging-automation tutorials, but did not establish a substantial set of dedicated hands-on security walkthroughs for this new beta. The results are indexed-search observations, **not** an exhaustive YouTube count. **Low-supply confidence: low–moderate.**

**Differentiation:** A useful video can combine setup eligibility, a realistic end-to-end architecture, memory/permission boundaries, and an honest discussion of third-party message processing. Avoid another generic "AI agents are here" recap.

**Audience:** ChatGPT Pro users in the U.S., AI-agent builders, privacy-conscious professionals, mobile automation enthusiasts, and DevSecOps/enterprise architects studying future agent channels. Business and Enterprise users should view this as architecture analysis, not a currently enabled feature.

**Recommended title:** "ChatGPT's AI Agent Can TEXT You First — But Is It Private? (iMessage + RCS)"

**Alternative title:** "OpenAI Dots Now Work in iMessage & RCS — Setup, Limits, and Security Explained"

**Thumbnail:** "AI TEXTS YOU FIRST!" / "PRIVATE OR NOT?"

**Opening hook:**
> "OpenAI just moved its always-on AI agent into your phone's regular messages. You can send a task by text, photo or voice note—and your agent can text you first when something changes. But there is a detail most quick demos will miss: those messages pass through a third-party delivery provider. Today, we'll explain who can use the new feature, how its memory and approvals work, and what security questions you should ask before connecting an agent to your inbox and calendar."

**Urgency:** High for an explainer, 24–48 hours from October 29. **Opportunity score: 8.0/10**, qualitative editorial estimate, not a measured analytics score.

## Proposed video structure (8–12 minutes)

| Segment | Content |
| --- | --- |
| 0:00–0:35 | Demonstrate the concept with clearly labeled official screenshots or a simulated text thread. |
| 0:35–1:45 | Explain the October 28 announcement and U.S. Pro beta eligibility. |
| 1:45–3:00 | Walk through the documented profile > Texting setup path; show actual UI only if eligible. |
| 3:00–4:30 | Explain shared memory, plugins, proactive updates, and approval links. |
| 4:30–7:00 | Show conceptual trust-boundary diagram, Linq relay, and privacy caveats. |
| 7:00–9:30 | Simulated defensive scenario: monitor a non-sensitive synthetic ticket, request approval for an update, and record audit events. |
| 9:30–end | Who should wait, how to limit permissions, and what would warrant revisiting the story. |

## Demonstration plan: verified versus simulated

**Verified from official docs:** profile > Texting entry point, iMessage/RCS channel, shared memory/plugins, U.S. Pro beta, third-party Linq provider, proactive notifications, approval links.

**Not independently tested in this research:** end-to-end live setup, delivery latency, actual phone-number verification UI, availability on any particular account, Linq retention configuration, and security properties under adversarial tests.

If an eligible U.S. Pro test account is unavailable, build a **clearly labeled simulation**:

- Inbound event: synthetic text/photo/voice metadata, no real personal data.
- Channel gateway: mock identity mapping and delivery adapter; never impersonate OpenAI's proprietary service.
- Agent orchestrator: synthetic ticket lookup and non-sensitive status change proposal.
- Policy engine: reject cross-user access and require authenticated approval for a write.
- Audit: correlation IDs, policy outcomes, and redacted metadata.
- Test cases: wrong phone identity, prompt injection in ticket text, stale approval, duplicated delivery, sensitive-data redaction, and STOP/unsubscribe handling.

The architecture diagram is at [architecture/2026-10-29-dot-texting-trust-boundaries.mmd](../../../architecture/2026-10-29-dot-texting-trust-boundaries.mmd), and design decisions are at [architecture/2026-10-29-dot-texting-design-decisions.md](../../../architecture/2026-10-29-dot-texting-design-decisions.md).

## What to verify before recording

- Confirm whether the creator has an eligible U.S. Pro account; do not suggest the beta is globally available or enabled for Plus, Business, or Enterprise.
- Check the current OpenAI release notes and dot help center for any changed eligibility, controls, or pricing.
- Confirm any privacy claim against primary documentation. Do not equate the iMessage/RCS transport with end-to-end encryption to OpenAI's agent.
- Avoid displaying real phone numbers, private messages, credentials, or customer records.
- Distinguish vendor security claims from independently verified outcomes.
- Do not claim a live hands-on demo if only an architectural simulation was produced.

## References

1. OpenAI, [ChatGPT release notes, October 28: Texting your dot](https://help.openai.com/en/articles/6825453-chatgpt-release-notes), accessed October 29, 2026.
2. OpenAI, [Getting started with your dot](https://help.openai.com/en/articles/20001530-getting-started-with-your-dot), beta eligibility, setup, STOP, and data cautions.
3. OpenAI, [Dots privacy, security, and safety FAQs](https://help.openai.com/en/articles/20001529-dots-privacy-security-and-safety-faqs), plugin boundaries, approvals, memory and data use.
4. OpenAI, [Text your dot: October 26–30 update](https://learn.chatgpt.com/docs/whats-new/dots-october-28-2026), channel and approval behavior.
5. 9to5Mac, [OpenAI expanding ChatGPT dot access to iMessage and RCS](https://9to5mac.com/2026/10/28/openai-expanding-chatgpt-dot-access-to-imessage-and-rcs/), October 28, 2026.
6. Engadget, [OpenAI now lets you text your Dot agent](https://www.engadget.com/2300235/openai-now-lets-you-text-your-dot-agent/), October 28, 2026.
7. Digital Trends, [OpenAI now lets you text your ChatGPT Dot agent](https://www.digitaltrends.com/computing/openai-now-lets-you-text-your-chatgpt-dot-agent-to-get-things-done/), October 28, 2026.
8. Gadgets360, [OpenAI Dots can now send texts, photos and voice messages](https://www.gadgets360.com/ai/news/openai-dots-agents-text-voice-imessage-chatgpt-rcs-rollout-features-12252879), October 29, 2026.
9. Windows Report, [Messaging privacy and the end-to-end encryption caveat](https://windowsreport.com/openai-now-allows-texting-your-chatgpt-dots-but-messages-arent-end-to-end-encrypted/), October 28, 2026.

**Corrections policy:** Update this article when OpenAI changes rollout geography, subscription eligibility, message security disclosures, or the documented setup process.
