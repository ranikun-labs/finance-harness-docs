# finance-legal-risk-review-notes.md

## 1. Status and authority

```text
document type         : Legal Risk Review Notes
document_id           : finance-legal-risk-review-notes
authority             : canonical internal operational baseline
lifecycle_status      : active
implementation_status : internal-control-baseline
runtime_loadable      : false
routable              : false
distribution_boundary : private-repository
external_review       : not externally reviewed
legal_advice          : false
legal_approval        : false
public_launch_gate    : unresolved
paid_launch_gate      : unresolved
source_provenance     :
  - _fable-candidates/2026-07-14-governance-pending/finance-legal-risk-review-notes--reconciled.md
canonical_sources     :
  - finance-harness-docs/00-core/0-a-safety-core/00-service-policy.md
  - finance-harness-docs/00-core/0-a-safety-core/finance-policyguard-rules.md
  - finance-harness-docs/00-core/0-a-safety-core/finance-output-validator.md
  - finance-harness-docs/00-core/0-a-safety-core/finance-market-data-grounding.md
  - finance-harness-docs/06-professional-standards/README.md
  - finance-harness-docs/06-professional-standards/finance-evidence-and-uncertainty-standard.md
```

This document is canonical only as an internal operational baseline. It is not
legal advice, not legal approval, not externally reviewed, and not public-launch
or paid-launch approval. Unresolved legal and provider questions remain
unresolved.

Internal canonical status permits implementation and operational controls only.
It does not resolve whether a future public service constitutes investment
advice, similar advisory activity, or another regulated activity.

`00-core` safety and Runtime contracts take precedence over this document. This
document is not a source of Runtime, PolicyGuard, Routing, Output Validator,
fixture, red-team, or market-data grounding semantics.

External Legal Review remains unassigned and is a future launch gate. For the
current prototype stage, logical owner roles may be held by the Project Owner.

## 2. Purpose and non-goals

Purpose:

- preserve all material legal-risk categories from the reconciled source
  provenance
- remove duplicate structure and align the analysis to current canonical
  `00-core` contracts
- separate current canonical product boundary from internal legal-risk analysis and
  future feature review requirements
- produce a narrow decision surface for logical owners and future External
  Legal Review

Non-goals:

- legal advice or legal conclusion
- external counsel approval
- privacy policy, terms of service, disclaimer, or marketing-copy final text
- PolicyGuard intent/action changes
- Output Validator behavior changes
- Routing Contract or Runtime behavior changes
- fixture IDs, red-team cases, regression gates, or Runtime enums
- market-data provider selection or data-licensing approval
- public-launch or paid-launch approval

## 3. Current canonical product boundary

The current canonical boundary is defined by the repository's `00-core`
documents, not by this `05` document.

Phase 1 is treated as:

- no account integration
- no automated trading
- no buy, sell, hold, reduce, increase, rebalance, timing, target-price,
  return-forecast, position-size, or account-based advice
- no recommendations or rankings of securities, products, sectors, themes, or
  asset classes
- no unapproved market-data redistribution
- no current market-state assertion without the grounding required by
  `finance-market-data-grounding.md`
- no PolicyGuard, Output Validator, Routing, fixture, or Runtime relaxation via
  disclaimer or educational framing

Current canonical `00-core` ownership:

| area | canonical owner | 05 treatment |
|---|---|---|
| service scope and prohibited product behavior | `00-service-policy.md` | cited only; not redefined |
| risk intents, actions, redirects, guard processing | `finance-policyguard-rules.md` | cited only; not extended |
| machine output checks and rewrite/block behavior | `finance-output-validator.md` | cited only; not changed |
| market-data source, 기준시각, current-claim grounding | `finance-market-data-grounding.md` | cited only; not changed |
| professional evidence quality | `06-professional-standards/finance-evidence-and-uncertainty-standard.md` | used for claim/evidence discipline only |

## 4. Legal-risk domains

The reconciled legal-risk domains are:

| ID | domain | source provenance | internal baseline summary | owner |
|---|---|---|---|---|
| LR-01 | investment-advice / advisory-status risk | reconciled external source | checklist and learning flows may still be interpreted as individualized investment guidance depending on service structure, monetization, and presentation | Product Owner + future External Legal Review |
| LR-02 | recommendation / ranking / analysis-report appearance | reconciled external source | Lens/checklist content can be misread as a recommendation if it highlights a security, sector, or industry in a directional way | Product Owner + Professional Standards Owner |
| LR-03 | explicit trading-decision leakage | reconciled external source | model, prompt, or UI regressions can leak buy/sell/target/size/timing content; canonical machine controls own behavior | Product Owner + Operations Owner |
| LR-04 | personalized or account-based advice | reconciled external source | journal, watchlist, price, account, or holdings context can create a personalized-advice appearance | Product Owner + Privacy Owner + future External Legal Review |
| LR-05 | market-data freshness and current-state claims | reconciled external source | delayed, stale, or ungrounded data can be consumed as a current investment signal | Product Owner + Operations Owner |
| LR-06 | market-data licensing and redistribution | reconciled external source | external data display or citation requires provider-term review before Phase 1.5 | Operations Owner + future External Legal Review |
| LR-07 | marketing, UI, CTA, disclaimer, and copy | reconciled external source | copy can imply outcomes, urgency, recommendation, or legal safety even when the AI path is safe | Product Owner + Professional Standards Owner + future External Legal Review |
| LR-08 | user-data and privacy | reconciled external source | records, watchlists, logs, and quality-review samples can contain sensitive financial or behavioral information | Privacy Owner + Security Owner + Operations Owner |
| LR-09 | image/OCR and future sensitive-input expansion | reconciled external source | image/OCR introduces account screenshots or third-party data risks; it is not part of current Phase 1 | Product Owner + Privacy Owner + Security Owner |
| LR-10 | internal rule and security exposure | reconciled external source | guard, validator, fixture, and red-team internals can become bypass material if exposed | Security Owner + Operations Owner |
| LR-11 | operations, incident, escalation, and evidence | reconciled external source | logging, escalation, incident, and review evidence need product/privacy/security/operations boundaries before public launch | Operations Owner + Privacy Owner + Security Owner |

## 5. Investment-advice and recommendation boundary

Candidate analysis:

- Even if responses are framed as education or checklist support, service design
  can still create investment-advice appearance if the user receives a
  security-specific answer that feels individualized.
- Monetization can increase regulatory and user-expectation risk, but free MVP
  status alone should not be treated as eliminating advisory-status risk.
- Recommendation risk includes direct recommendations and indirect signals such
  as rankings, highlighted winners, checklist completion, cycle-stage timing,
  target-price conversion, or product selection.

Canonical boundary:

- `00-service-policy.md`, `finance-policyguard-rules.md`, and
  `finance-output-validator.md` own the no-recommendation and no-trading-advice
  controls.
- This document does not add new risk intents, actions, redirects, or output
  checks.

Open legal issue:

- Whether the overall product structure requires External Legal Review before
  public release remains open and remains a public-launch gate.

## 6. Personalized/account-based advice risk

Candidate analysis:

- Journal review, user-entered prices, watchlist entries, and future account
  integration can create the appearance of personalized advice even if the
  response is framed as reflection or checklist guidance.
- The reconciled source provenance treats account integration as a future
  service-identity shift and treats record-referencing responses as a possible
  personalized-advice appearance.

Internal baseline treatment:

- Keep question and AI-response originals session-only by default.
- Treat watchlist data as service-use only; do not use it for aggregate
  analytics yet.
- Keep account integration outside Phase 1.
- Keep OCR/image input outside Phase 1.

These are internal baseline assumptions, not legal approvals.

## 7. User-data and privacy risk

Candidate analysis:

- Investment journals, emotion tags, user-entered prices, watchlists, logs, and
  quality-review records can reveal financial behavior or allow financial
  profile inference.
- Retention, deletion, backup expiry, audit-event preservation, customer-support
  access, and quality-review sampling remain unresolved.
- Professional Standards review should minimize copied user data in findings,
  but privacy/legal authority remains outside 06.

Current internal baseline assumptions:

| assumption | owner status | approval status | downstream documents affected |
|---|---|---|---|
| question and AI-response originals are session-only by default | Product Owner + Privacy Owner | internal baseline; not legal approval | `finance-user-data-policy.md`, `finance-ops-monitoring-guide.md` |
| watchlist data is service-use only with no aggregate analytics yet | Product Owner + Privacy Owner | internal baseline; not legal approval | `finance-user-data-policy.md`, `finance-disclaimer-and-copy-guide.md` |
| quality improvement uses synthetic or minimized review artifacts by default | Product Owner + Privacy Owner + Professional Standards Owner | internal baseline; not legal approval | `finance-user-data-policy.md`, `finance-human-review-protocol.md`, `finance-ops-monitoring-guide.md` |
| retention/deletion/backup limits require owner-approved treatment and future External Legal Review before public launch | Privacy Owner + Operations Owner | unresolved | `finance-user-data-policy.md`, `finance-ops-monitoring-guide.md` |

## 8. Disclaimer, UI, and marketing-copy risk

Candidate analysis:

- Disclaimers do not permit otherwise prohibited advice.
- UI, CTA, store copy, landing copy, alerts, and marketing can create risk even
  when AI output contracts are safe.
- Editorial stock/sector curation is the shortest path to recommendation
  appearance and is prohibited for MVP as an internal baseline assumption.
- Alert/notification copy should be deferred until the feature exists.

Current internal baseline assumptions:

| assumption | owner status | approval status | downstream documents affected |
|---|---|---|---|
| editorial stock/sector curation is prohibited for MVP | Product Owner | internal baseline; not legal approval | `finance-disclaimer-and-copy-guide.md`, `finance-ops-monitoring-guide.md` |
| alert/notification copy is deferred until feature design exists | Product Owner | internal baseline; not legal approval | `finance-disclaimer-and-copy-guide.md`, `finance-ops-monitoring-guide.md` |
| final disclaimer/legal copy requires future External Legal Review before public launch | Product Owner + future External Legal Review | unresolved | `finance-disclaimer-and-copy-guide.md` |
| “맞춤/개인화” wording remains unavailable until separate review | Product Owner + future External Legal Review | unresolved | `finance-disclaimer-and-copy-guide.md`, `finance-user-data-policy.md` |

Public-copy hard gate:

- No public landing page, application-store listing, SNS campaign, paid-plan
  copy, or other public marketing copy may ship until the public-facing copy
  has been separately reviewed.
- Internal examples remain labeled `INTERNAL DRAFT — NOT LEGALLY APPROVED`.
- Until separately reviewed, public use of `personalized`, `custom`, `맞춤`,
  `개인화`, `portfolio review`, `portfolio assessment`, `포트폴리오 평가`,
  `AI investment assistant`, and `AI 투자 어시스턴트` is prohibited.
- The bounded internal expression `저장한 기록을 바탕으로 한 복기 질문` may
  appear only as internal draft copy and must not imply a recommended next
  action.

## 9. Market-data licensing and redistribution risk

Candidate analysis:

- Phase 1 has no approved market-data redistribution.
- Phase 1.5 or any display of current market values requires provider-term
  review, source labeling, 기준시각, and stale-data handling.
- Numeric display does not authorize directional interpretation or investment
  judgment.

Canonical boundary:

- `finance-market-data-grounding.md` owns grounding requirements.
- This document does not alter those requirements.

Open provider/legal issue:

- Provider terms, redistribution rights, display permissions, and external data
  usage remain unresolved and block any Phase 1.5 market-data feature.
- Phase 1 remains free of external market-data redistribution.
- No external market-data display, caching, screenshot use, derived-data use,
  redistribution, or AI grounding use may be enabled until provider-specific
  license and redistribution terms are documented.
- User-entered values remain user input, not verified market data.

## 10. Operations, logging, escalation, and incident risk

Candidate analysis:

- Logging must not store unnecessary original questions, prices, quantities,
  account data, or sensitive financial records.
- Incident handling must distinguish safety-policy failure, legal/privacy event,
  market-data failure, and operational failure.
- External legal review is required for legal notification/reporting obligations
  and incident communications.
- Internal rule exposure remains a security risk and must be controlled through
  exposure and implementation boundaries.

Open operations issues:

- metric and alert thresholds
- event/log retention period
- deletion SLO and backup expiry
- incident severity model
- release gate owner
- legal notification/reporting procedure

Temporary incident escalation rule:

- Any suspected Restricted or Confidential data exposure, deletion failure, or
  LLM over-transmission is treated as SEV-1 minimum.
- It must immediately route to the logical Privacy Owner, Security Owner, and
  Operations Owner.
- Do not invent statutory notification deadlines. Legal notification and
  reporting deadlines remain unresolved.

## 11. Relationship to canonical 00-core

This document is subordinate to canonical `00-core` documents.

| 05 subject | canonical owner | rule for this document |
|---|---|---|
| service scope | `00-service-policy.md` | cite only |
| prohibited advice and risk handling | `finance-policyguard-rules.md` | cite only |
| machine output rejection/rewrite | `finance-output-validator.md` | cite only |
| market-data grounding | `finance-market-data-grounding.md` | cite only |
| evidence quality for human review | `06-professional-standards/finance-evidence-and-uncertainty-standard.md` | use for claim discipline; not legal authority |

If this document appears to conflict with a canonical `00-core` contract, the
canonical contract controls and this document must be revised.

## 12. Version reconciliation table

| issue | source provenance position | current canonical contract | internal baseline resolution | required owner |
|---|---|---|---|---|
| Legal-risk source basis | reconciled external source preserves material categories and removes duplicate structure | `00-core` controls safety/Runtime behavior | use `05-product-legal-ops/finance-legal-risk-review-notes.md` as the canonical internal legal-risk baseline | Product Owner |
| Advisory-status timing | public review recommended before public launch and again before paid/advice-adjacent expansion | `00-service-policy.md` prohibits advice but does not provide legal conclusion | require future External Legal Review before public release and again before paid/advice-adjacent expansion | Product Owner + future External Legal Review |
| Recommendation risk | direct and indirect recommendation appearance both matter | PolicyGuard/Validator prohibit recommendations; 06 reviews hidden advice by function | include direct and indirect recommendation appearance; create no new Runtime rule here | Product Owner + Professional Standards Owner |
| Checklist as approval | redirect checklist may be consumed as answer | Output Validator and service policy prohibit approval/advice | copy/review must prevent checklist-completion approval; no new Runtime rule here | Product Owner + Professional Standards Owner |
| Watchlist scope | watchlist/question entry is allowed only as service-use material | current 00-core permits limited watchlist/price policy but no recommendation | assume service-use only and no aggregate analytics until owner approval | Product Owner + Privacy Owner |
| Market data | Phase 1.5+ requires source/기준시각 and provider review | market-data grounding says Phase 1 has no live data and Phase 1.5 needs source/기준시각 | Phase 1 no redistribution, Phase 1.5 provider/license review required | Operations Owner + future External Legal Review |
| Image/OCR | future feature with sensitive-input risk | service policy says OCR/image currently absent | keep outside Phase 1; require product/privacy/security review before any design work | Product Owner + Privacy Owner + Security Owner |
| Internal rule exposure | internal guard/red-team details can become bypass material | exposure/metadata documents own exposure classification | preserve as security/ops risk; do not expose internal rules in user-facing output | Security Owner + Operations Owner |
| Legal-review labels | avoid absolute “legal review unnecessary” framing | no legal approval in canonical docs | mark technical-owner vs future External Legal Review separately | Product Owner |
| Open questions | unresolved owner and review decisions remain | `05` cannot create legal approval | map to L-01~L-05 plus product assumptions in §§13~15 | Product Owner |

## 13. Product owner decisions

| ID | question | options | recommendation | consequence | blocking documents | required owner |
|---|---|---|---|---|---|---|
| P-01 | Should question and AI-response originals remain session-only by default? | A: session-only; B: user opt-in storage; C: default storage | A | A keeps ops/user-data docs aligned to minimization; B/C require privacy design and future External Legal Review before public launch | `finance-user-data-policy.md`, `finance-ops-monitoring-guide.md` | Product Owner + Privacy Owner |
| P-02 | Should watchlist data be used beyond service-use? | A: service-use only; B: anonymized aggregate analytics; C: marketing/product analytics | A | A avoids analytics/privacy expansion; B/C require anonymization and review | `finance-user-data-policy.md`, `finance-disclaimer-and-copy-guide.md` | Product Owner + Privacy Owner |
| P-03 | Should editorial stock/sector curation be allowed in MVP? | A: prohibited; B: allowed after copy/review | A | A avoids recommendation appearance; B requires public-copy and legal review before launch | `finance-disclaimer-and-copy-guide.md`, `finance-ops-monitoring-guide.md` | Product Owner |
| P-04 | When should alert/notification copy be approved? | A: defer until feature exists; B: approve now | A | A avoids speculative copy policy; B may create unused canonical scope | `finance-disclaimer-and-copy-guide.md`, `finance-ops-monitoring-guide.md` | Product Owner |

All four rows are internal baseline assumptions only. None is final legal
approval.

## 14. External legal-review and owner decisions

| ID | question | options | recommendation | consequence | blocking documents | required owner |
|---|---|---|---|---|---|---|
| L-01 | What is the legal-risk source basis? | A: earlier competing drafts; B: reconciled external source; C: this canonical 05 document | C for internal baseline | C preserves material categories and resolves duplicate structure; earlier drafts are source provenance only | all 05 legal-risk references | Product Owner |
| L-02 | Can any internal 05 document become canonical before external legal review? | A: no; B: internal canonical allowed with explicit legal placeholders | B for internal operational baseline only | B permits implementation controls while keeping external legal and launch gates unresolved | all 05 documents | Product Owner |
| L-03 | What retention/deletion requirements apply? | A: externally reviewed numeric retention/deletion/backup values; B: internal placeholders with owner-approved treatment | B for internal baseline; public launch remains gated | persistent closed-beta data requires owner-approved treatment; numeric periods are not legally mandated unless verified | `finance-user-data-policy.md`, `finance-ops-monitoring-guide.md` | Privacy Owner + Operations Owner |
| L-04 | What disclaimer/legal copy is approved? | A: externally reviewed fixed copy; B: internal draft only | B for internal baseline | public-facing copy is frozen until separately reviewed | `finance-disclaimer-and-copy-guide.md` | Product Owner + future External Legal Review |
| L-05 | How are market-data provider terms handled? | A: Phase 1 excludes market-data redistribution; B: Phase 1.5 requires provider-term review before display; C: allow unreviewed display | A now, B before Phase 1.5; reject C | preserves Phase 1 boundary and blocks unapproved redistribution | `finance-legal-risk-review-notes.md`, `finance-ops-monitoring-guide.md`, market-data sections of 05 docs | Operations Owner + future External Legal Review |

## 15. External legal-review requirements

External Legal Review remains unassigned and remains required before:

- advisory-status / investment-advice risk of the service structure
- paid feature or membership model before launch
- final disclaimer and public/legal copy
- retention, deletion, backup expiry, and audit-event preservation
- LLM provider data processing and user notice
- market-data licensing and redistribution
- incident notification/reporting procedure
- account integration, OCR/image input, or portfolio/holdings-based expansion
- any claim that a feature is legally safe, compliant, or not regulated

## 16. Downstream 05 document dependencies

| downstream document | dependency on this canonical baseline | blocked by |
|---|---|---|
| `finance-user-data-policy.md` | must use `05-product-legal-ops/finance-legal-risk-review-notes.md` for data categories, retention, deletion, original-storage, quality sampling, and watchlist scope | L-03, P-01, P-02 |
| `finance-disclaimer-and-copy-guide.md` | must use this document for recommendation, disclaimer, marketing, CTA, “맞춤/개인화”, and editorial-curation boundaries | L-04, P-03, P-04 |
| `finance-ops-monitoring-guide.md` | must use this document for logging, escalation, incident, release-gate, and provider-review dependencies | L-03, L-05, P-01 |
| `README.md` | must state that 05 does not override 00-core safety/Runtime contracts | L-01, L-02 |

## 17. Remaining public and paid launch gates

This document is canonical as an internal operational baseline. The following
items remain unresolved and block public or paid launch:

1. Future External Legal Review of advisory-status / investment-advice risk.
2. Owner-approved retention, deletion, backup, and audit treatment before
   persistent closed-beta user data is enabled.
3. Public-facing disclaimer, UI, and marketing-copy review before any public
   copy ships.
4. Provider-specific LLM processing checklist and review before beta/public
   handling of user data.
5. Provider-specific market-data license and redistribution terms before any
   Phase 1.5 market-data feature.
6. Separate paid-service review before paid launch.
7. Continued confirmation that no 05 document duplicates or weakens `00-core`
   contracts.

## 18. Final internal-baseline readiness assessment

Assessment: CANONICAL INTERNAL OPERATIONAL BASELINE.

Rationale:

- all material legal-risk categories from the reconciled external source are preserved
- duplicate section structure is consolidated
- canonical `00-core` boundaries are treated as controlling
- product assumptions are marked as internal baseline assumptions, not legal
  approval
- legal-review requirements remain open and visible
- no Runtime, PolicyGuard, Routing, Output Validator, fixture, red-team,
  regression, or market-data grounding semantics are created

Public launch and paid launch remain gated.
