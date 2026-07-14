# 06 Professional Standards

This canonical layer defines human and professional review quality standards
for Finance Harness documents and responses.

## Canonical Documents

- `finance-evidence-and-uncertainty-standard.md` — evidence quality,
  evidence-to-claim traceability, and uncertainty expression for human review.
- `finance-response-quality-rubric.md` — qualitative response review rubric for
  human reviewers.
- `finance-human-review-protocol.md` — review workflow, reviewer roles,
  findings intake, and joint sign-off expectations.

## Ownership Boundary

06 owns human/professional review standards only. It does not define or replace:

- PolicyGuard intents, actions, or redirect behavior
- Output Validator machine checks
- Routing Contract behavior or Runtime Lens selection
- routing fixtures, red-team execution, or regression pass/fail contracts
- market-data freshness contracts
- service-policy scope
- legal approval or legal conclusions

Those responsibilities remain with the existing `00-core` canonical documents.
When a 06 review finds a safety, routing, validator, evidence, or regression
issue, it records a finding and routes the proposed change to the owning
canonical document instead of changing the rule locally.

## Review And Sign-Off Model

Professional Standards review is qualitative. Minimum coverage is based on the
relevant review axes, not on a fixed mandatory sample count. A fixed numeric
sample threshold may be introduced only after pilot review data supports it.

A second reviewer is mandatory only for suspected hidden investment advice,
suspected safety-policy failure, or unresolved high-severity evidence or
uncertainty conflicts.

Release-facing sign-off requires joint approval by the Professional Standards
owner and the owner of the reviewed artifact or document set.

## Noncanonical Design Record

`finance-professional-standards-pack-design.md` remains an external candidate
architecture/design record. It is not a canonical 06 standard.
