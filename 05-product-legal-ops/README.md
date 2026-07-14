# 05 Product / Legal / Operations

## 1. Purpose

`05-product-legal-ops` is the canonical internal operational baseline for
Product / Legal / Operations controls in Finance Harness.

This layer turns reviewed candidate material into implementation and operations
controls for a small side-project service. It is an internal operational
baseline only.

It is not legal advice, not legal approval, not externally reviewed, not
public-launch approval, and not paid-launch approval. External legal review
remains unassigned and is a future launch gate.

## 2. Document Scope

| Document | Scope |
|---|---|
| `finance-legal-risk-review-notes.md` | Internal legal-risk issue map, launch-gate framing, and owner-review decision surface. |
| `finance-user-data-policy.md` | Internal user-data inventory, minimization, LLM transmission, logging, retention/deletion placeholders, and future-feature data gates. |
| `finance-disclaimer-and-copy-guide.md` | Internal copy, disclaimer, CTA, marketing, personalization-wording, and public-copy gate baseline. |
| `finance-ops-monitoring-guide.md` | Internal monitoring, incident triage, release checks, evidence, deletion, LLM transmission, market-data, and copy-compliance operations baseline. |

## 3. Authority Hierarchy

When documents conflict, apply this order:

1. `00-core` safety and Runtime contracts
2. applicable canonical product contracts
3. `05-product-legal-ops` internal operational controls
4. `06-professional-standards` human-review and professional standards where applicable

`00-core` controls over any conflicting `05` wording.

## 4. Internal-Baseline Status

Every document in this layer is canonical only as an internal operational
baseline. Canonical status permits implementation and operational controls only.

It does not resolve whether a future public service constitutes investment
advice, similar advisory activity, or another regulated activity.

Public launch remains unresolved and gated. Paid launch remains unresolved and
gated. Unresolved legal, privacy, provider, market-data, and public-copy
questions remain unresolved.

For the current prototype stage, the logical roles used in this layer may be
held by the Project Owner:

- Product Owner
- Privacy Owner
- Security Owner
- Operations Owner
- Professional Standards Owner

External Legal Review remains unassigned and is not implied to have occurred.

## 5. What 05 Does Not Own

This layer does not define, replace, or relax:

- service purpose
- PolicyGuard intents or actions
- Output Validator behavior
- market-data grounding rules
- Lens routing
- Runtime activation
- fixture IDs or regression expected behavior
- human-review standards
- legal conclusions or legal approval

## 6. Relationship To 00-Core

`00-core` remains the source of truth for:

- service scope and prohibited behavior
- PolicyGuard risk intents, actions, and redirects
- Output Validator blocking and rewrite behavior
- market-data grounding, source, and 기준시각 rules
- Runtime routing and Lens activation boundaries

`05` may add stricter internal operational controls, but it may not weaken
`00-core`.

## 7. Relationship To 06 Professional Standards

`06-professional-standards` owns evidence, uncertainty, response quality, and
human-review procedure. `05` may route findings to 06 or use 06 review
discipline, but it does not create or replace 06 standards.

06 review does not create legal approval.

## 8. Launch Gates

| Gate | Internal readiness status | Required unresolved items |
|---|---|---|
| Private prototype | Allowed with conditions | Keep no public users, no payment, no market-data redistribution, session-only Q/A originals by default, and metadata-only safety logs. |
| Closed beta | Gated by operational readiness | Persistent user data must not be enabled until an owner-approved retention, deletion, backup, and audit treatment exists. |
| Free public launch | Blocked | Advisory-status review, public privacy/terms/disclaimer/copy review, retention/deletion/backup/audit decisions, provider-processing review, and incident notification procedure remain unresolved. |
| Paid public launch | Blocked | Separate paid-service review is required; paid or premium copy must not imply deeper judgment, greater accuracy, improved returns, reduced losses, personalized advice, portfolio advice, premium recommendations, rankings, or signals. |

## 9. Future Feature Gates

Phase 1 remains free of external market-data redistribution. User-entered
values remain user input, not verified market data.

No Phase 1.5 external market-data display, caching, screenshot use,
derived-data use, redistribution, or AI grounding use may be enabled until
provider-specific license and redistribution terms are documented.

## 10. Unresolved Decision Handling

Do not replace unresolved legal, privacy, provider, retention, deletion,
backup, audit, public-copy, paid-launch, or market-data items with assumptions.

Use explicit placeholders such as:

- `RETENTION-TO-BE-DECIDED`
- `LEGAL-REVIEW-REQUIRED`
- `OWNER-DECISION-REQUIRED`

Internal operational periods may be selected by the responsible owner where
needed for implementation, but they must not be labeled legally mandated unless
verified.

## 11. Provenance

The four canonical documents in this layer are derived from fixed external
candidate sources. The independent English red-team review and Korean decision
summary remain external evidence and provenance records, not canonical legal
approval.

## 12. Document Index

- `README.md`
- `finance-legal-risk-review-notes.md`
- `finance-user-data-policy.md`
- `finance-disclaimer-and-copy-guide.md`
- `finance-ops-monitoring-guide.md`
