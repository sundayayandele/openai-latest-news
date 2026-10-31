# Architecture decisions — OpenAI Evals sunset (2026-10-31)

**Status:** Proposed, for review. Applies to hosted Evals migration; does not imply that the Responses API or ChatKit is retiring.

## ADR-01: Keep a read-only legacy evidence archive
**Decision:** Export/inventory every eval definition, grader, test dataset reference, run and output item before Nov 30. **Why:** the hosted dashboard and API are scheduled to disappear. **Trade-off:** storage and retention costs. **Acceptance:** inventory counts reconciled with paginated API responses; access restricted; restoration sampled.

## ADR-02: Use portable version-controlled evaluation definitions
**Decision:** Maintain Promptfoo configuration, fixtures and expected assertions in Git. **Why:** tests become reviewable and CI-friendly. **Trade-off:** code-centric ownership instead of a hosted UI. **Acceptance:** a clean checkout can reproduce the synthetic regression suite.

## ADR-03: Require grading-parity validation
**Decision:** Rebuild grader logic and compare against manually labeled examples. **Why:** similarity and judge-based scores may differ between platforms. **Trade-off:** engineering effort. **Acceptance:** critical test cases match expected outcomes and discrepancies are documented.

## ADR-04: Separate data migration from new production testing
**Decision:** Keep historical results immutable; new Promptfoo runs receive their own provenance and timestamp. **Why:** prevents accidental claims of equivalent measurement. **Acceptance:** report metadata identifies platform, model, prompt revision and grader revision.

## ADR-05: Scope credentials and budget in CI
**Decision:** CI uses dedicated restricted provider credentials, a secrets manager and spend alerts. **Why:** automated evals can leak credentials or generate unexpected bills. **Trade-off:** extra operational controls. **Acceptance:** no secrets in source or logs; budget alerts tested with a dry run.

## ADR-06: Gate production promotion on evaluations and human review
**Decision:** Evaluate representative prompts before release; sensitive failures require manual approval. **Why:** protects quality and safety. **Trade-off:** slower deploys. **Acceptance:** failing eval blocks promotion and records reviewer decision.

## ADR-07: Treat Agent Builder and reusable prompts as separate workstreams
**Decision:** Inventory hosted workflow IDs and prompt object dependencies independently. **Why:** both have the same Nov 30 date but different migration targets. **Trade-off:** multiple migration projects. **Acceptance:** no remaining runtime dependency on deprecated hosted endpoints.

## ADR-08: Confirm runtime status rather than assuming scheduled enforcement
**Decision:** Label Oct 31 as vendor-scheduled read-only until tested in an authorized account. **Why:** documentation and live rollout can differ. **Acceptance:** record actual tenant behavior and any official revised date before video release.

**Sources:** https://developers.openai.com/api/docs/deprecations ; https://developers.openai.com/cookbook/examples/evaluation/moving-from-openai-evals-to-promptfoo ; https://www.promptfoo.dev/docs/configuration/guide/