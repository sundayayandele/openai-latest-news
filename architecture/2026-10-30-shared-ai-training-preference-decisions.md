# Architecture decisions: cross-vendor AI training usage preferences

**Date:** 2026-10-30  
**Status:** Proposed implementation plan; review when IETF draft and vendor rollout details change.  
**Companion:** [Research article](../articles/2026-10-30-openai-google-microsoft-shared-ai-training-robots-policy.md) · [Architecture diagram](./2026-10-30-shared-ai-training-preference.mmd)

## Context

OpenAI, Google and Microsoft announced support plans on October 30, 2026 for a shared AI-training preference token in robots.txt. The IETF document is an **individual Internet-Draft**, not a finalized standard. The planned vendor rollouts are staggered. Microsoft additionally distinguishes training from grounding and is replacing older NoArchive/NoCache usage controls.

## ADR-001: Separate crawler access, training preference, and AI grounding

**Decision:** Model three independent policy dimensions in configuration and documentation.  
**Rationale:** The shared token expresses a preference about using retrieved content for training. It does not itself block HTTP access or govern all generated-answer grounding.  
**Tradeoff:** More complex governance but fewer accidental SEO regressions and misleading assurances.  
**Validation:** Inventory existing crawler rules, provider-specific training controls, and Microsoft grounding settings independently.

## ADR-002: Treat the shared policy token as additive during rollout

**Decision:** Keep existing provider-specific controls while testing the new `User-Agent: __usage_ai-training` group on staging.  
**Rationale:** OpenAI has not announced immediate universal enforcement; Google targets 2027; Microsoft begins on crawl in February 2027.  
**Tradeoff:** Temporary duplication of policy configuration.  
**Validation:** Review first-party support documentation and re-run configuration tests before removing old controls.

## ADR-003: Version-control robots.txt and enforce change review

**Decision:** Manage robots.txt through Git-backed infrastructure or CMS deployment pipelines, with designated policy owners.  
**Rationale:** A one-line change can affect crawler access, training preferences, and downstream content distribution.  
**Tradeoff:** Slower ad hoc edits.  
**Validation:** Require PR approval, diff review, syntax check, staging verification, and rollback artifact.

## ADR-004: Preserve provider-specific precedence and exceptions

**Decision:** Document and test Google-Extended and other explicit vendor settings instead of assuming a shared token overrides them.  
**Rationale:** Google says it will interpret the shared opt-out as Google-Extended disallow only when Google-Extended is not explicitly specified. The draft permits provider-specific interpretation.  
**Tradeoff:** More test cases per provider.  
**Validation:** Maintain a policy matrix with explicit exceptions, expected behavior and verified vendor documentation.

## ADR-005: Keep search discoverability and training consent separate

**Decision:** Avoid changing Googlebot/Bingbot access or indexation directives merely to opt out of AI training.  
**Rationale:** The draft says usage preferences are orthogonal to crawler rules. Microsoft says new training/grounding preferences do not themselves alter eligibility for traditional Bing results.  
**Tradeoff:** Usage preferences do not technically prevent an uncooperative crawler from fetching public content.  
**Validation:** Monitor sitemap accessibility, robots rules, Search Console/Bing Webmaster Tools and search coverage.

## ADR-006: Implement Microsoft migration as a dated change

**Decision:** Track a separate Microsoft work item for sites relying on `NoArchive`/`NoCache`.  
**Rationale:** Microsoft says new preferences take effect on crawl from **2027-02-01**, and old usage restrictions need migration by **2027-03-01**. Its temporary Bing Webmaster Tools grounding transition opens **2026-12-15** for eligible legacy users.  
**Tradeoff:** Vendor-specific implementation and operational calendar.  
**Validation:** Record the legacy inventory, deploy replacement meta/header preferences, verify crawled state and transition-setting eligibility.

## ADR-007: Do not treat robots.txt as security or a legal enforcement guarantee

**Decision:** Keep sensitive content behind actual authentication/authorization and characterize the token as a voluntary, machine-readable usage preference.  
**Rationale:** Robots.txt does not enforce access control; not all data users necessarily comply; and the Internet-Draft has no formal standard status.  
**Tradeoff:** Security and legal/compliance work remain separate.  
**Validation:** Review authentication boundaries and data-publication policy; avoid claims that robots.txt can retract content already collected or trained on.

## ADR-008: Monitor specification drift and rollout state

**Decision:** Recheck the IETF draft and first-party vendor documents before recording a tutorial or changing production defaults.  
**Rationale:** Internet-Drafts can change, and vendor implementation dates/precedence may evolve.  
**Tradeoff:** Recurring verification overhead.  
**Validation:** Capture the date and version of each primary reference in the publication notes.

## Implementation acceptance criteria

1. Existing robots.txt is backed up and diffed against the proposed change.
2. Shared policy-token group is syntactically valid and reachable over HTTPS.
3. Provider-specific rules and sitemap/indexing behavior are unchanged unless explicitly approved.
4. WordPress/CDN/Nginx ownership of robots.txt is documented, avoiding competing writers.
5. Microsoft legacy directive users have a dated migration ticket.
6. Article/video states **announced/planned**, not **already enforced everywhere**.
7. Compliance monitoring and crawler access logs are not misrepresented as proof that no model training occurs.

## Sources

- https://openai.com/index/helping-websites-express-ai-training-preferences/
- https://developers.google.com/search/blog/2026/10/ai-training-control
- https://blogs.bing.com/search/2026/10/your-content-your-choice-clearer-preferences-for-training-and-grounding/
- https://datatracker.ietf.org/doc/draft-kelley-aipref-training-preference-signal/
