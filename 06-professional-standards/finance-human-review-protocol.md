---
document_id: finance.contract.human-review-protocol
title: Finance Human Review Protocol
artifact_type: contract
version: 1.0.0
lifecycle_status: active
implementation_status: contract-only
owner: finance-harness
exposure_class: internal-controlled
distribution_boundary: private-resource
runtime_loadable: false
routable: false
last_updated: 2026-07-14
public_exposable: false
authority_level: 6
governed_artifacts:
  - artifact_type: report
  - artifact_type: handoff
  - artifact_type: reference
  - artifact_type: contract
breaking_change_policy: Professional Standards owner review plus affected artifact-owner review.
depends_on:
  - document_id: finance.policy.service
  - document_id: finance.policy.policyguard
  - document_id: finance.validator.output
  - document_id: finance.contract.evidence-uncertainty-standard
  - document_id: finance.contract.response-quality-rubric
---

# Finance Human Review Protocol

## 1. Purpose And Scope

This protocol defines how Finance Harness performs human and professional review
for documents, examples, findings, and release-facing sign-off.

It is normative for review procedure only. It does not replace PolicyGuard,
Output Validator, Routing Contract, Runtime behavior, routing fixtures,
red-team execution, regression contracts, market-data grounding, service-policy
scope, or legal approval.

The protocol turns the Evidence and Uncertainty Standard and Response Quality
Rubric into an operational review workflow.

## 2. Canonical Dependency Boundary

Reviewers must treat the following documents as owners of their respective
contracts:

- `00-core/0-a-safety-core/00-service-policy.md`
- `00-core/0-a-safety-core/finance-policyguard-rules.md`
- `00-core/0-a-safety-core/finance-output-validator.md`
- `00-core/0-a-safety-core/finance-market-data-grounding.md`
- `00-core/0-b-lens-infra/finance-lens-authoring-standard.md`
- `00-core/0-c-runtime-contract/finance-routing-contract.md`
- `00-core/0-c-runtime-contract/finance-red-team-cases.md`
- `00-core/0-c-runtime-contract/finance-lens-regression-coverage-matrix.md`

A human review finding may recommend that an owner inspect a rule. It must not
change the rule inside the review record.

## 3. Review Types

| review type | trigger | inputs | output | blocking |
|---|---|---|---|---|
| Document standards review | New or changed 06 standard or related governance doc | changed document, canonical dependencies | verdict and findings | Yes for the reviewed change |
| Response quality review | Golden response, example, or public-safe sample review | response sample, prompt/context summary, relevant canonical docs | rubric verdict | Yes for sample approval |
| Evidence review | Current-state, source, uncertainty, or claim-traceability concern | claim units and supporting sources | evidence finding | Depends on severity |
| New-Lens review | Lens candidate before registration or activation review | Lens document and canonical Lens standards | authoring/professional findings | Yes for that review track |
| Release-facing review | Document set or artifact set prepared for release | validation results, review sample, owner summary | joint sign-off or revision request | Yes |
| Finding intake review | Human or operational finding submitted for owner review | finding record and example fragment | owner-routing decision | No by itself |

## 4. Reviewer Roles

| level | role | may do | may not do |
|---|---|---|---|
| L1 | internal reviewer | apply standards, record findings, request revision | override canonical safety contracts |
| L2 | domain/professional reviewer | resolve high-severity professional disagreements, review hidden-advice concerns | create PolicyGuard or Runtime rules |
| L3 | external expert or legal reviewer | review only the explicitly shared scope | receive repo-only material without an approved sharing process |

L3 legal review is not created by this protocol. Legal approval remains outside
06 and must be handled by the Legal owner or external counsel.

## 5. Review Input Preparation

Before review, the artifact owner should provide:

- artifact path or identifier
- review scope
- relevant changed sections or response samples
- known canonical dependencies
- known unresolved owner decisions
- validation results already available

Do not include unnecessary user records, account details, logs, or operational
metadata in the review packet.

## 6. Sampling Model

Minimum coverage is qualitative axis coverage, not a fixed mandatory sample
count.

For each review, select samples or sections that cover the relevant axes:

- evidence and uncertainty
- hidden investment advice
- safety-policy boundary
- current-state or market-data claims
- user-input handling
- refusal and redirect quality
- Lens or document-authoring boundary, when applicable
- release-facing sign-off impact, when applicable

A fixed numeric sample threshold is intentionally not defined. It may be added
only after pilot review data shows that a specific threshold improves review
quality without creating false precision.

## 7. Review Workflow

1. Confirm review scope and affected owner documents.
2. Identify relevant review axes from §6.
3. Decompose material claims or response blocks where needed.
4. Apply `finance-evidence-and-uncertainty-standard.md` to evidence issues.
5. Apply `finance-response-quality-rubric.md` to response quality issues.
6. Record findings with the owning canonical document or artifact owner.
7. Determine whether a second reviewer is mandatory under §8.
8. Produce a verdict and required follow-up.
9. For release-facing review, collect joint sign-off under §14.

## 8. Second Reviewer Requirement

A second reviewer is mandatory only for:

- suspected hidden investment advice
- suspected safety-policy failure
- unresolved high-severity evidence or uncertainty conflict

A second reviewer is not mandatory for every sample. The reviewer may still
request another reviewer for difficult or ambiguous cases, but routine samples
can be reviewed by one qualified reviewer.

## 9. Verdict Model

Use the same qualitative verdict vocabulary as the rubric:

```text
PASS
REVISION-REQUIRED
FAIL
```

- `PASS`: review scope satisfies applicable standards.
- `REVISION-REQUIRED`: the artifact can proceed only after bounded correction
  and, when needed, reviewer confirmation.
- `FAIL`: the artifact conflicts with a safety boundary, evidence standard,
  professional review requirement, or unresolved owner decision.

No numeric score, average, weighted total, or automatic release decision is
used.

## 10. Review Record

Recommended record fields:

```text
review_id
review_type
reviewed_artifact
review_scope
reviewer
second_reviewer
review_axes
verdict
findings
required_owner_follow_up
sign_off_status
```

The record should identify claim units or section references without copying
unnecessary private user data.

## 11. Disagreement Handling

When reviewers disagree:

1. Identify the exact claim, response block, or section at issue.
2. Classify the disagreement as evidence, hidden advice, safety policy,
   clarity, ownership, or legal uncertainty.
3. Use a second reviewer if §8 requires one.
4. If the disagreement concerns a canonical 00-core rule, route the question to
   that owner instead of resolving it locally.
5. Record the final disposition and owner follow-up.

## 12. Safety Failure Versus Quality Failure

| failure class | definition | immediate action | owner destination |
|---|---|---|---|
| Safety-policy failure | output or document appears to violate service policy, PolicyGuard, validator, or routing boundary | block approval for the reviewed artifact | owning `00-core` document owner |
| Evidence failure | material claim lacks support, overstates certainty, or mishandles user input | revision or owner review | 06 owner plus source owner if needed |
| Quality failure | unclear, misleading, or poorly structured but not a safety-policy failure | revision | artifact owner plus 06 owner |
| Legal uncertainty | review depends on legal approval or legal interpretation | do not decide in 06 | Legal owner / external counsel |

## 13. Finding Intake

A finding intake record may propose that an owner inspect a fixture, red-team,
validator, template, Lens, or policy gap. It must not assign final fixture IDs,
expected behavior, intents, actions, Runtime enums, or routing outcomes.

Recommended fields:

```text
finding_summary
example_reference
suspected_boundary
why_current_contract_may_be_insufficient
recommended_owner
urgency
```

The owning canonical document decides whether a change is needed.

## 14. Release-Facing Sign-Off

Release-facing sign-off requires joint approval by:

- the Professional Standards owner
- the owner of the reviewed artifact or document set

Joint approval confirms that:

- applicable review axes were covered qualitatively
- required second-reviewer cases were handled
- unresolved owner decisions are recorded and not silently assumed
- any safety, Runtime, PolicyGuard, Validator, or regression change request is
  routed to the proper owner
- legal approval is not claimed unless the Legal owner explicitly provides it

A Professional Standards sign-off cannot override failed machine validation,
failed regression, failed metadata validation, or an unresolved canonical-owner
blocker.

## 15. New-Lens Review

For Lens candidates, the reviewer checks professional quality and authoring
fitness only. Lens registration, Catalog updates, routing activation,
`runtime_loadable`, `routable`, `fixture_refs`, and Runtime behavior remain with
0-B and 0-C owner processes.

Review axes may include:

- checklist wording does not imply action approval
- data requirements are not current-state assertions unless grounded
- examples do not become recommendations
- uncertainty is visible where evidence is incomplete
- user input is not treated as verified external fact

## 16. Review Cadence

Review cadence is event-driven by default:

- new or changed 06 standard
- changed canonical dependency affecting evidence, quality, or review workflow
- release-facing document set prepared for sign-off
- repeated finding that suggests a systemic quality issue

A fixed calendar cadence may be introduced only after pilot review data supports
it.

## 17. Acceptance Criteria

This protocol is correctly applied when:

- review scope and axes are explicit
- minimum coverage is qualitative, not a fixed arbitrary count
- mandatory second-reviewer cases are limited to §8
- release-facing sign-off is joint under §14
- 06 findings do not override 00-core contracts
- legal approval is not claimed by 06
- Runtime, PolicyGuard, Validator, Routing, fixture, and regression semantics
  remain with their canonical owners

## 18. Deferred Maintenance Items

Deferred until pilot review or later owner decision:

- numeric sample thresholds
- measured reviewer agreement target
- calendar-based review cadence
- external expert review charter
- separate release-quality document if release procedure grows beyond this
  protocol

These items are deferred explicitly and are not prerequisites for the current
human/professional review protocol.

## 부록. 본 문서가 하지 않는 것

| 하지 않는 것 | 담당 |
|---|---|
| machine gate, validator, or regression override | `00-core` owning document |
| intent/action/Runtime enum creation | `finance-policyguard-rules.md`, `finance-routing-contract.md` |
| fixture ID or expected behavior creation | `finance-routing-fixtures.md`, `finance-red-team-cases.md` |
| Lens registration or activation | `finance-lens-catalog.md`, `finance-routing-contract.md` |
| exposure classification changes | `finance-document-exposure-policy.md` |
| legal approval or legal conclusion | Legal owner / external counsel |
