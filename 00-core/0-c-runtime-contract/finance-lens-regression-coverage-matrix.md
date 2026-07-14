# finance-lens-regression-coverage-matrix.md

## 0. Metadata

```text
document_id           : finance.contract.lens-regression-coverage-matrix
artifact_type         : contract
version               : 0.1.0
lifecycle_status      : draft
implementation_status : contract-only
owner                 : finance-harness
exposure_class        : internal-controlled
distribution_boundary : repo-only
runtime_loadable      : false
routable              : false
last_updated          : 2026-07-14
depends_on            : finance.contract.routing,
                        finance.fixture.routing,
                        finance.red-team-cases,
                        finance.contract.lens-catalog,
                        finance.contract.lens-authoring-standard
```

---

## 1. Purpose and boundary

This matrix is the canonical verification map for the seven known but inactive Product-MVP Lens contracts:

- `etf-index-structure`
- `asset-bond`
- `semiconductor-cycle`
- `ai-compute-value-chain`
- `equity-kr-structure`
- `equity-us-structure`
- `earnings-revision`

It distinguishes:

1. PolicyGuard intent/action coverage
2. Routing Fixture coverage
3. Red Team coverage
4. Lens authoring/content-review coverage
5. inactive Runtime routing status
6. deferred activation coverage

This matrix is **not** an activation record. In plain contract terms, this matrix is not an activation record.

```text
known key ≠ runtime_loadable
known key ≠ routable
fixture coverage ≠ selected Runtime Lens expectation
guard/action coverage can exist before Lens activation
safe content routing is deferred until activation
Lens-specific response validation is deferred until activation
```

PR 3 does not modify Catalog registration, Routing Contract semantics, PolicyGuard rules, Output Validator rules, Lens metadata, or Lens body content. This matrix does not modify Routing Contract semantics, does not modify PolicyGuard rules, and does not modify Output Validator rules. The seven Lens documents keep:

```text
lifecycle_status      : draft
implementation_status : contract-only
runtime_loadable      : false
routable              : false
redirect_to           : []
fixture_refs          : []
```

---

## 2. Current inactive Runtime rule

The Routing Contract treats these seven keys as catalog-known but inactive. They must not appear as selected Runtime Lens expectations in:

- routing fixture `expected_lenses`
- Red Team `allowed_redirect_lens`
- generated Runtime routing results

Fixture coverage of a subject only verifies that the existing guard/action contract holds when the user uses Product-MVP topic wording. It does not imply routing to the inactive Lens.

---

## 3. Coverage matrix

| Lens key | Relevant safe question categories | Safe Runtime Lens routing | Relevant risk intents | Routing Fixture coverage | Red Team coverage | Lens-specific safety boundaries | Coverage status | Deferred activation work |
|---|---|---|---|---|---|---|---|---|
| `etf-index-structure` | ETF selection criteria, tracking error vs premium/discount, leverage/inverse mechanics, hedge structure, distribution checks | Deferred — safe questions require the inactive Lens or generic active fallback only | RISK_RECOMMENDATION, RISK_BUY_SELL, RISK_POSITION_SIZE, RISK_TARGET_PRICE, RISK_DATA_GROUNDING | RF-11, RF-12, RF-13, DG-06 | RT-H-09, RT-A-10, RT-G-09 | no product recommendation, no leverage/inverse timing, no current premium/discount or tracking-state assertion, no allocation request | PARTIAL — guard/action covered; safe Lens content deferred | activate only after Routing Contract update, fixture registration, response validation, and full regression |
| `asset-bond` | duration vs maturity, bond vs bond ETF structure, coupon/yield concepts, call features, currency exposure | Deferred — active fallbacks such as `macro-rate-fx` or `risk-checklist` may be used only where already supported | RISK_BUY_SELL, RISK_RECOMMENDATION, RISK_POSITION_SIZE, RISK_TARGET_PRICE, RISK_DATA_GROUNDING | RF-14, RF-15, RF-16, DG-07, DG-08 | RT-A-11 | no rate-direction prediction, no bond or bond-ETF recommendation, no current yield/price assertion, no duration-as-trading-signal | PARTIAL — guard/action covered; bond Lens routing deferred | define safe routing role, redirect role, and Lens response checks before activation |
| `semiconductor-cycle` | cycle variables, inventory layers, utilization/capacity, CAPEX/order timing, product family differences | Deferred — `sector-cycle`/`earnings` may remain active fallbacks only where current contract supports them | RISK_DATA_GROUNDING, RISK_BUY_SELL, RISK_RECOMMENDATION, RISK_TARGET_PRICE, RISK_ACCOUNT_BASED_ADVICE | RF-17, RF-18, DG-09 | RT-T-08, RT-G-10 | no cycle-stage assertion, no current inventory/utilization/capacity assertion, no sector/company recommendation, no cycle-as-buy-signal | PARTIAL — topic risk boundaries covered; content validation deferred | add safe deep-dive routing and semiconductor-specific response validation only during activation work |
| `ai-compute-value-chain` | AI infrastructure nodes, upstream/downstream dependencies, workload distinction, supply bottleneck structure | Deferred — no current active Lens fully owns AI compute value-chain safe content | RISK_RECOMMENDATION, RISK_DATA_GROUNDING, RISK_BUY_SELL, RISK_TARGET_PRICE, RISK_POSITION_SIZE, RISK_ACCOUNT_BASED_ADVICE, RISK_PROMPT_INJECTION | RF-19, RF-20, DG-10 | RT-P-09, RT-T-09 | no winner ranking, no company/segment recommendation, no current demand/supply/capacity/bottleneck assertion, no bottleneck-as-trading-signal | PARTIAL — guard/action and prompt-injection boundaries covered; safe content deferred | require routing role, response examples, validator focus, and full regression before Runtime selection |
| `equity-kr-structure` | Korean investor flow publication, short-selling/stock loan disclosures, index events, settlement/session rules, current-rule verification | Deferred — `supply-demand`/`event-calendar` may remain active fallbacks only where current contract supports them | RISK_DATA_GROUNDING, RISK_BUY_SELL, RISK_RECOMMENDATION | RF-21, RF-22, DG-11, DG-12 | RT-A-12, RT-G-11 | no current foreign/institutional flow assertion, no short-selling strategy, no deterministic index-event effect, no Korea-market recommendation | PARTIAL — guard/action covered; market-structure Lens routing deferred | define safe Korean market-structure route and stale-rule validation before activation |
| `equity-us-structure` | US sessions, options/index calendars, passive-flow structure, institution reports, market-rule verification | Deferred — `event-calendar`/`market-context` may remain active fallbacks only where current contract supports them | RISK_DATA_GROUNDING, RISK_BUY_SELL, RISK_RECOMMENDATION | RF-23, RF-24, DG-13, DG-14, DG-15 | RT-T-11, RT-G-12 | no current passive-flow/options-positioning assertion, no pre-market/after-hours strategy, no index-rebalance deterministic prediction, no stale market-rule claim | PARTIAL — guard/action and current-rule risk covered; safe content deferred | add safe US market-structure route, stale-regulation checks, and response validation before activation |
| `earnings-revision` | consensus/revision definitions, comparable snapshot checks, revision breadth/dispersion concepts, revision recordkeeping | Deferred — `earnings`/`journal-review` may remain active fallbacks only where current contract supports them | RISK_DATA_GROUNDING, RISK_RECOMMENDATION, RISK_BUY_SELL, RISK_TARGET_PRICE, RISK_POSITION_SIZE, RISK_ACCOUNT_BASED_ADVICE | RF-25, RF-26, RF-27, DG-16, DG-17 | RT-H-10, RT-T-10 | no current consensus or revision-direction assertion, no earnings prediction, no target-price conversion, no revision-based buy/sell recommendation | PARTIAL — guard/action covered; revision Lens response validation deferred | define safe revision route, data-grounding requirements, and target-conversion validation before activation |

---

## 4. Coverage type separation

### 4.1 PolicyGuard intent/action coverage

Routing Fixture rows added for PR 3 verify that Product-MVP topic wording still maps to existing canonical intents and actions. They do not create new intents, actions, fixture families, schema fields, or gates.

Representative rows:

```text
RF-11~RF-27 : topic-specific RISK_RECOMMENDATION / RISK_BUY_SELL /
              RISK_POSITION_SIZE / RISK_TARGET_PRICE checks
DG-06~DG-17 : topic-specific RISK_DATA_GROUNDING checks
```

### 4.2 Routing Fixture coverage

Routing Fixtures may use only active fallback Lens keys already supported by the current Routing Contract, such as:

```text
market-context, supply-demand, event-calendar, earnings, valuation,
sector-cycle, macro-rate-fx, risk-checklist, journal-review, emotion-check
```

They must not place any of the seven Product-MVP keys in `expected_lenses`.

### 4.3 Red Team coverage

Red Team cases exercise bypass and adversarial phrasing while keeping `allowed_redirect_lens` limited to currently active Lens keys.

Representative rows:

```text
RT-H-09, RT-H-10
RT-P-09
RT-A-10, RT-A-11, RT-A-12
RT-T-09, RT-T-10, RT-T-11
RT-G-09, RT-G-10, RT-G-11, RT-G-12
```

### 4.4 Lens authoring/content-review coverage

The seven Lens documents already contain authoring-time safety boundaries and self-checklists. PR 3 does not edit them and does not convert those authoring notes into Runtime routing expectations.

Content-review coverage remains document review, not Runtime selection.

### 4.5 Deferred activation coverage

Deferred activation work includes:

- safe content routing to the inactive Lens
- Lens-specific response examples
- Output Validator expression-list updates, if approved
- `redirect_to` population, if approved by Routing Contract change
- Lens `fixture_refs` population, if approved by fixture registration work
- `runtime_loadable=true` / `routable=true`, only after activation gate approval

---

## 5. Regression assertions

A future implementation or PR touching these contracts must preserve all assertions below:

- no inactive key appears in routing fixture `expected_lenses`
- no inactive key appears in Red Team `allowed_redirect_lens`
- no Product-MVP Lens document has `runtime_loadable: true`
- no Product-MVP Lens document has `routable: true`
- `redirect_to` remains `[]` until a Routing Contract change approves redirect membership
- `fixture_refs` remains `[]` until fixture registration metadata is approved
- safe concept/checklist questions that require selecting an inactive Lens remain deferred
- risk/adversarial topic wording is still classified by existing canonical intent/action contracts

---

## 6. Non-scope

This matrix does not:

- activate any Lens
- modify Lens Catalog registration
- modify Routing Contract semantics
- modify PolicyGuard rules
- modify Output Validator rules
- add fixture schema fields
- add new fixture families
- add new intents or actions
- migrate Lens metadata
- include P2 candidates such as `asset-commodity`, `financial-statement-quality`, or `portfolio-structure`
