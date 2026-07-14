---
document_id: finance.contract.evidence-uncertainty-standard
title: Finance Evidence and Uncertainty Standard
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
  - document_id: finance.policy.market-data-grounding
  - document_id: finance.template.prompt
---

# Finance Evidence and Uncertainty Standard

## 1. Purpose And Scope

This standard defines how human reviewers evaluate evidence quality,
evidence-to-claim traceability, and uncertainty expression in Finance Harness
outputs and documents.

It is normative for human and professional review. It is not Runtime logic and
it does not introduce machine-enforced categories, PolicyGuard intents,
PolicyGuard actions, fixture IDs, routing behavior, or legal conclusions.

This document applies to:

- golden or reviewed response examples
- Lens and standard-document review
- human review findings
- professional sign-off records
- proposed changes that affect evidence or uncertainty quality

Out of scope:

- market-data freshness contracts, owned by
  `00-core/0-a-safety-core/finance-market-data-grounding.md`
- machine output blocking, owned by
  `00-core/0-a-safety-core/finance-output-validator.md`
- PolicyGuard intent/action behavior, owned by
  `00-core/0-a-safety-core/finance-policyguard-rules.md`
- Routing and Runtime selection, owned by
  `00-core/0-c-runtime-contract/finance-routing-contract.md`
- legal approval or legal advice

## 2. Evidence Unit

The minimum review unit is the smallest independently reviewable claim.

A reviewer may classify a whole sentence when the sentence contains one claim.
When one sentence contains multiple independently verifiable claims, the
reviewer may classify clauses separately. Clause-level classification is
preferred when it prevents a verified statement from masking an unsupported
inference in the same sentence.

The review record should quote or summarize only the claim fragment needed to
identify the issue. It should not store unnecessary user data or operational
logs.

## 3. Evidence Type Model

### 3.1 VERIFIED_FACT

A claim is a `VERIFIED_FACT` only when the reviewer can identify a reliable
source or canonical document that directly supports it.

Requirements:

- cite the canonical document, source type, or review artifact used
- preserve source date or 기준시각 when temporal correctness matters
- avoid upgrading user-provided text into verified fact

### 3.2 GENERAL_PRINCIPLE

A `GENERAL_PRINCIPLE` is stable educational or conceptual content that does not
depend on current market state, current regulation, user holdings, or a live
data feed.

Requirements:

- keep it general and non-directional
- avoid implying a buy, sell, hold, rebalance, target price, timing, or ranking
- defer to PolicyGuard and Output Validator documents when the principle is
  used to disguise an action recommendation

### 3.3 USER_PROVIDED_INPUT

`USER_PROVIDED_INPUT` is information supplied by the user. It may be used as a
review object or context, but it is not verified fact.

Requirements:

- label it as user-provided where the distinction matters
- do not infer account state, suitability, wealth, risk tolerance, or portfolio
  structure unless a canonical feature explicitly supports that use
- do not reuse user input as a source of market truth

### 3.4 MODEL_INTERPRETATION

`MODEL_INTERPRETATION` is a structured reading of provided context or canonical
material. It remains a reviewable interpretation, not a fact claim.

Requirements:

- separate observation from implication
- keep uncertainty visible when the interpretation is incomplete
- do not convert interpretation into investment advice or approval

### 3.5 INFERENCE_OR_HYPOTHESIS

`INFERENCE_OR_HYPOTHESIS` is a tentative conclusion that goes beyond directly
verified material.

Requirements:

- mark the inference as tentative
- state what evidence would be needed to verify it
- avoid using the inference as a decision, ranking, target, or timing signal

### 3.6 UNVERIFIED_OR_UNAVAILABLE

`UNVERIFIED_OR_UNAVAILABLE` applies when the evidence is absent, inaccessible,
stale, or outside the service boundary.

Requirements:

- say that the claim cannot be confirmed from available evidence
- avoid substituting a guess or generic market narrative
- redirect to safe verification steps only when those steps are allowed by the
  current service policy

## 4. Fact Standard

A fact claim passes evidence review only if a reviewer can answer:

- What exact claim is being made?
- What source or canonical document supports it?
- Is the source current enough for this claim?
- Does the claim depend on live market, legal, tax, or regulatory status?
- Does the wording overstate certainty?

If any answer is missing, the claim must be downgraded to an interpretation,
hypothesis, or unavailable claim.

## 5. General-Principle Standard

General principles must remain educational and non-actionable. A principle
fails review if it becomes a proxy for:

- a trading instruction
- a target price or return expectation
- a position-size or allocation suggestion
- a current market-state assertion without grounding
- individualized advice based on user holdings or records

## 6. User-Input Standard

User input may be reviewed, summarized, or reflected back when allowed by the
owning canonical document. It must not become evidence of external fact.

Examples of prohibited upgrades:

- treating a user-entered price as verified market price
- treating a user’s self-description as a suitability profile
- treating a journal entry as proof of investment skill or future behavior
- treating a watchlist item as a recommendation target

## 7. Model-Interpretation Standard

A model interpretation is acceptable when it:

- identifies the evidence it interprets
- preserves the distinction between fact and interpretation
- avoids directional language
- includes uncertainty when evidence is incomplete

It fails review when it presents a derived reading as a verified conclusion or
uses a derived reading as a hidden recommendation.

## 8. Inference And Hypothesis Standard

Inferences and hypotheses are allowed only as non-actionable reasoning aids.
They must not be written as conclusions that a user should act on.

Required review questions:

- Is the inference explicitly marked as tentative?
- Is the missing evidence clear?
- Could the user reasonably read it as a buy/sell/hold/timing signal?
- Does it rely on current data that the system has not verified?

## 9. Uncertainty Model

Uncertainty expression must be visible and proportionate.

- **Visible** means the user or reviewer can see which claim is uncertain.
- **Proportionate** means the uncertainty level matches the evidence gap.

Uncertainty wording must not become a disclaimer that permits otherwise
prohibited advice. A risky recommendation with a caveat remains a risky
recommendation and belongs to the owning PolicyGuard or Validator contract.

## 10. Temporal Correctness

Claims about current market state, current legal status, current product
availability, or current regulation require explicit grounding in the owning
canonical contract or a verified source with a date or 기준시각.

When temporal correctness cannot be established, reviewers must classify the
claim as unavailable or unresolved rather than allowing a stale assertion.

## 11. Claim Decomposition

For review purposes, decompose responses into claim units before judging the
whole response. A single response can contain multiple evidence types.

Recommended decomposition fields:

```text
claim_unit
unit_type
supporting_source_or_canonical_reference
uncertainty_level
review_finding
required_owner_if_revision_needed
```

This format is for human review records only. It is not a Runtime schema.

## 12. Response-Type Application

- **Learning responses**: prioritize general principles and verified canonical
  references; avoid current-state claims unless grounded.
- **Checklist responses**: ensure each checklist item is a review question, not
  a hidden direction.
- **Journal review responses**: keep user input labeled as user-provided and do
  not turn observations into behavioral diagnoses.
- **Refusals and redirects**: do not use uncertainty wording to soften or
  bypass a required refusal.
- **Lens authoring review**: verify that examples and data references do not
  imply current market facts without grounding.

## 13. Reviewer Checklist

A reviewer should mark a finding when any of the following is true:

- user input is treated as verified external fact
- current market, legal, tax, or regulatory status is asserted without support
- uncertainty is hidden, vague, or disproportionate
- a hypothesis is written as a conclusion
- an interpretation becomes a recommendation, ranking, or timing signal
- a claim depends on an owner decision that has not been made

## 14. Relationship With Fixtures And Red-Team Cases

This standard may identify evidence-quality findings that should be considered
by the owners of routing fixtures, red-team cases, or validator rules. It does
not create fixture IDs, expected behavior, intents, actions, redirects, or
Runtime assertions.

If a review finding suggests a missing regression case, the reviewer records the
finding and cites the owning canonical document. The fixture or red-team owner
decides whether and how to add coverage.

## 15. Acceptance Criteria

A reviewed artifact passes this standard when:

- each material claim can be classified by evidence type
- fact claims have direct support
- user input is not elevated to verified fact
- uncertainty is visible and proportionate
- temporal claims are grounded or marked unavailable
- no inference is converted into a recommendation or action signal
- any required owner decision is explicitly deferred instead of assumed

## 16. Deferred Maintenance Items

The following items are intentionally deferred until pilot review provides
usable evidence:

- whether to add recommended uncertainty phrases to a prompt/template owner
  proposal
- whether to review every golden response or a representative sample during
  periodic maintenance
- whether reviewer agreement needs a numeric target

These deferred items do not weaken the current standard. They may be addressed
only through a later Professional Standards update with affected-owner review.

## 부록. 본 문서가 하지 않는 것

| 하지 않는 것 | 담당 |
|---|---|
| market-data freshness rule 변경 | `finance-market-data-grounding.md` |
| machine-blocking category 변경 | `finance-output-validator.md` |
| PolicyGuard intent/action 변경 | `finance-policyguard-rules.md` |
| routing fixture 또는 red-team case 생성 | `finance-routing-fixtures.md`, `finance-red-team-cases.md` |
| Lens 작성 규칙 변경 | `finance-lens-authoring-standard.md` |
| legal approval 또는 적법성 판단 | Legal owner / external counsel |
