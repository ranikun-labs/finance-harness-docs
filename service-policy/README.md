# service-policy

## 0. 한 줄 책임

`service-policy`는 금융 하네스 웹·앱의 사용자 가입, 상품, 혜택, Trial, Usage
및 관련 개인정보·마케팅 적용 정책의 canonical 계층이다.

## 1. 이 계층이 존재하는 이유

기존 숫자 Prefix 계층(`00`~`06`)은 Finance AI Knowledge·Safety·Lens·Runtime
Contract·Professional Standards 체계다. 변경 주기와 승인 주체가 AI 품질·안전·
regression lifecycle에 묶여 있다.

`service-policy`는 실제 Finance 웹·앱 서비스의 가입·상품·혜택·Trial·Usage
정책을 다룬다. 이 정책은 운영 실험, 전환율, 비용 데이터에 따라 상대적으로
자주 바뀌는 별도 lifecycle을 가진다. 이 차이 때문에 숫자 계층의 하위 항목이
아니라 별도 최상위 계층으로 둔다.

`service-policy`는 숫자 Prefix를 사용하지 않는다. 기존 00~06 순번 체계의
다음 단계가 아니라, 그 체계와 구분되는 독립 책임 계층이기 때문이다.

## 2. 우선순위

충돌 시 다음 순서를 따른다.

```text
00-core Safety
> 05-product-legal-ops Baseline
> 06-professional-standards
> service-policy의 Product·Commercial 선택
> Launch Experiment Values
```

상위 안전·법적 기준을 하위 Product Policy가 약화할 수 없다. `service-policy`
는 00-core의 안전 규칙, 05의 데이터·법무·카피 baseline, 06의 품질·리뷰
기준을 준수해야 하며, 이를 재정의하거나 완화할 수 없다.

## 3. 소유하지 않는 내용

`service-policy`는 다음을 소유하지 않는다.

```text
PolicyGuard 상세 규칙
Lens 정의
Routing
Fixture
Runtime Activation
API
Database Schema
Migration
구현 상태
Backend Architecture
```

위 항목이 필요한 경우 canonical 문서를 링크로 참조하며, 본문을 복사하지
않는다.

## 4. 문서 목록

### 현재 존재하는 문서

- `finance-product-policy.md` — Master Product Policy. 가입·Promotion·
  Trial·Usage·마케팅 원칙과 무료/Trial/유료 Feature Matrix.
- `finance-launch-experiment-values.md` — 변경 가능한 Launch Experiment
  수치의 단일 Source of Truth.
- `finance-onboarding-and-identity-policy.md` — 가입·External Identity·
  Account Linking과 Shared Identity/Finance Product 책임 경계.
- `finance-privacy-and-marketing-gate.md` — 가입·추가 혜택의 개인정보 처리와
  채널별 마케팅 동의 분리 Gate.

### 후속 계획 문서 (이번 PR에서 생성하지 않음)

다음 문서는 이번 기반 문서 검수·Merge 이후 별도 PR에서 작성한다. 아래
파일명은 계획일 뿐이며, 현재 이 Repository에 존재하지 않는다.

- `finance-welcome-promotion-policy.md`
- `finance-trial-and-entitlement-policy.md`
- `finance-ai-usage-and-quota-policy.md`

## 5. 기존 계층과의 Canonical 관계

| 계층 | 역할 | service-policy와의 관계 |
|---|---|---|
| `00-core` | AI Safety·PolicyGuard·Output Validator·Runtime Contract의 불변 기준 | service-policy는 이를 링크로 참조하며 재정의하지 않는다 |
| `01`/`02`/`03` | Finance Analysis Lens | service-policy는 비용·Trial 제공 범위 판단 시 Lens Catalog를 참조할 수 있으나 Lens를 정의하지 않는다 |
| `05-product-legal-ops` | 데이터·법무·카피·운영 Baseline | service-policy는 개인정보 최소수집·보존·삭제 원칙과 카피 일반 제한을 05에서 참조한다 |
| `06-professional-standards` | Evidence·Uncertainty·Quality·Human Review | 무료·Trial·유료 모든 Plan에 동일하게 적용되며, service-policy는 Plan별 권한을 06에 두지 않는다 |
| `service-policy` | 가입·상품·혜택·Trial·Usage·마케팅 적용 정책 | 이 문서 |

## 6. Product Policy 전용 최소 Metadata

`00-core/0-d-knowledge-architecture/finance-knowledge-artifact-metadata-standard.md`
의 Knowledge Artifact 필드(`runtime_loadable`, `routable`, `fixture_refs`,
`lens_key`, `exposure_class`)는 Product Policy 문서에 적용하지 않는다.
Product Policy 문서는 Runtime Routing 대상이 아니다.

`service-policy` 하위 문서는 다음 최소 Metadata를 사용한다.

```text
policy_id: string
document_type: product-service-policy
product_policy_status: draft | accepted
legal_review_status: not_required | required | pending | completed
architecture_status: not_started | in_progress | completed
implementation_status: not_started | in_progress | completed
runtime_support: not_supported | partial | supported
release_status: not_released | released
launch_values_status: not_applicable | experiment_candidate | pinned
owner: string
canonical_refs: []
```

## 7. 상태 구분

다음 상태는 서로 다르며, 하나의 완료가 다른 것을 의미하지 않는다.

```text
Product Policy Accepted
≠ Legal Review Completed
≠ Architecture Designed
≠ Implementation Completed
≠ Runtime Supported
≠ Product Released
```

이 계층에 문서가 존재한다는 사실 자체는 Architecture 설계, 구현, 또는
Runtime 지원의 완료를 의미하지 않는다.
