# Finance AI Agent — Product Vision and Agent MVP Behavior Proposal

> Status: PROPOSED / NOT_EFFECTIVE · Owner direction recorded 2026-10-29 · not an accepted policy, runtime implementation, deployment, or public-launch approval.
> Owner: Finance Product · Consumers: Finance Backend, Shared AI, Finance FE · Proposed version: 0.1
> Canonical precedence: existing Finance Product Policy > Safety Core/PolicyGuard/Output Validator > accepted Finance/Shared AI ADRs > this proposal. Any contradiction requires a separately reviewed Owner decision.

## F1 — What the product is intended to do

Finance Harness aims to be more than a general chatbot or a static investment checklist. Given a user's question, it should combine the user's **authorized and explicitly selected** investment records, trustworthy market/company evidence, domain-specific Expert Lens knowledge, and the permitted conversation context to support rigorous financial analysis. Outputs distinguish verified facts, user assertions, hypotheses, uncertainty, alternative scenarios and follow-up questions. The system must not make unauthorized trading decisions or claim guaranteed returns.

Four long-term product experiences:
1. **My investments:** discuss and review holdings or investment theses the user has explicitly recorded and authorized; true portfolio tracking is a separate later policy/data decision.
2. **Market questions:** explain industries, earnings, rates, FX, valuation and market mechanisms using appropriately sourced and time-stamped data.
3. **Future possibilities:** compare conditional scenarios, causal assumptions, counterarguments and disconfirming indicators without asserting a single certain forecast.
4. **Conversation continuity:** connect selected past discussions and Journal/Analysis results to new questions while keeping user opinion separate from verified evidence.

These are TARGET experiences, not evidence of currently running features.

## Agent MVP: minimally real, not a prompt-only demonstration

For the first end-to-end milestone, a single bounded Finance-controlled orchestration flow should:
1. Authenticate the principal and check ownership of the selected Finance resource.
2. Classify the question and run existing input PolicyGuard **before** selecting Lens or allowing Tools.
3. Choose up to a small approved set of existing, eligible Finance Expert Lenses (initial target 1–3), without activating currently draft/inactive Lenses by implication.
4. Execute at least one actual allowlisted, read-only Tool through explicit Product-owned authorization, with validated arguments and tenant scope. A second market/evidence Tool becomes an MVP requirement only once its data license, freshness contract and source are approved.
5. Keep the result's source, effective/as-of time and permission scope separate from a model interpretation. Calculations and hard rules are deterministic code, not model guesses.
6. Produce a structured, evidence-linked interpretation with assumptions, risks, counter-cases and missing information. Finance Output Validator checks the result before exposure.
7. Answer a same-session follow-up using explicitly permitted recent context and optionally a user-selected Journal. Persistent conversation storage and long-term personalized Memory are **not approved** by this proposal.
8. Preserve explicit Journal-save semantics: analysis completion does not automatically write a Journal or modify an investment record.

An MVP passes only with a reproducible real workflow at its declared integration level. A matching test fixture or simulated Tool is not an actual data-source or authenticated HTTP E2E.

## Target architecture and responsibilities

**Finance Product:** business intent, domain-specific Expert Lens selection, PolicyGuard, CurrentUserPort and Product authorization, Question/Checklist/Journal/Analysis/Review/Portfolio data ownership, user consent, final output validation, policy compliance.

**Shared AI (existing Kotlin/JVM foundation with Spring AI adapter):** declared Skill execution, generic Tool/Retriever/Model port invocation, correlation/execution IDs, typed evidence/scope validation, limits, telemetry and technical failure handling. It does not own Finance investment meaning or access Finance's DB directly.

**Explicit boundary:** Finance -> versioned, approved service contract -> Shared AI submit. Direct internal HTTP/JSON is the accepted target communication style, but exact endpoint, wire DTO and service-auth protocol remain under separate technical decision/gates.

### Concepts that must remain distinct

- Orchestrator: coordinates bounded execution; does not imply unconstrained autonomous delegation.
- Lens/Skill: professional analytic methodology and allowed semantic selection, not automatically a callable data Tool.
- Tool: actual implementation that retrieves or deterministically computes, with pre-call allowlist/authorization and query-time scope verification.
- Memory: user-authored Journal truth, conversation text, optional derived memory, and immutable analysis run context are separately classified and owned.
- Guardrail/Hook: enforced application boundary before input/Tool calls and after Evidence/output, not a prompt-only instruction.

The initial topology is **one bounded Orchestrator + selected Expert Lenses + allowlisted read-only Tools**, with optional conditional specialist review only after an evaluation shows a measurable benefit. Permanent multi-agent spawning is not a first-MVP prerequisite.

## Evidence, memory and evaluation gates

- Verified facts require source IDs and applicable publication/effective times. User-provided price/position is labeled unverified user input, not current market truth.
- No real-time quote, market movement or consensus assertion without an authorized source and as-of timestamp; lack of evidence yields UNKNOWN or a transparent limited answer.
- Protected Tools and Retrievers must fail closed on missing or stale authorization; identity_user_id is not automatically tenant_reference.
- Memory reuse is limited by explicit consent, ownership and source provenance. Conversation retention, delete propagation to summaries/embeddings/job snapshots and legal review are separate decisions.
- Golden tests must cover authorized and unauthorized Journal retrieval, invalid Tool arguments, prompt injection from external evidence, stale market facts, mismatched Evidence IDs, numerical calculations, timeout/retry, and same-session follow-up.
- Evaluation captures task completion, grounding and unsupported claim rate, tool selection/argument accuracy, p50/p95 latency and verified cost; missing measurements stay UNKNOWN.

## Stage gates

| Stage | Goal | Evidence required |
|---|---|---|
| Existing integration | Finance RPL-127 + Shared AI RPL-126 first General Analysis slice | L1 contracts; L2 compiled Bridge; L3 actual cross-service HTTP; L4 trusted user/Tenant E2E; distinguish each |
| Agent MVP | One real approved read-only Tool + selected eligible Lens + grounded structured response + same-session follow-up | bounded Tool calling, authorization and output gates, replayable Golden Set and actual end-to-end trace |
| Personalized research | Explicit user-approved Portfolio/long-term Memory and additional market sources | data policy, legal/consent, deletion, licensing and scope gates |
| Advanced agent | Conditional specialist/reviewer, multiple evidence sources, richer scenarios | incremental safety, quality, latency and cost evidence |
| Production | reviewed release/security/data-provider gates | operational telemetry and Owner approval, not implied by unit tests |

## Open decisions — not approved by this document

1. First genuinely permitted market/Evidence Source, licensing, as-of freshness and re-display rights.
2. Real tenant_reference source and query-time Product authorization for Tool/Retriever.
3. Exact versioned service wire DTO/API, protocol security, timeouts and error mapping.
4. Permanent conversation storage and user-controlled long-term Memory retention/deletion.
5. Broader portfolio tracking or suitability-based personalized investment advice, subject to policy and legal gate.
6. Whether any inactive Lens should be separately reviewed/activated.

## Documentation ownership and non-goals

- Confluence Finance Harness and Backend·AI Roadmap: business vision, stages, evidence/status and links; keep the historical FastAPI conceptual sketch explicitly distinguished from current Kotlin/JVM accepted Phase 1.
- This document: proposed user-facing Agent behavior and MVP acceptance criteria; not a substitute for Routing Contract, Lens Catalog, Finance User Data Policy, or Safety Core.
- finance-harness-architecture: accepted product architecture and cross-repository decisions, only after separate design acceptance.
- shared-ai-architecture: generic runtime contracts, not Finance-specific advisory policy.
- Confluence '06. External AI Reference Hub': Harvard evaluation, Google EGM review, Upstage deterministic Tool rules remain research/adoption candidates, not implementation proof.
- Existing Jira RPL-126/127 implementation scope must not silently expand to the full Agent MVP without a scoped Owner approval/Primary Jira.

References:
- [Finance Backend·AI Roadmap](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/18153473/Finance+Harness+Backend+AI)
- [Finance Product Policy](../../service-policy/finance-product-policy.md)
- [Finance Routing Contract](finance-routing-contract.md)
- [Finance Lens Catalog](../0-b-lens-infra/finance-lens-catalog.md)
- [Finance User Data Policy](../../05-product-legal-ops/finance-user-data-policy.md)
- [Shared AI ADR-0001](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0001-skill-lens-workflow-execution-ownership.md)
- [Shared AI ADR-0004](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0004-execution-context-api-tenant-isolation.md)
