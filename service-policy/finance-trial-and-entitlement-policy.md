# Finance Trial and Entitlement Policy

## 0. Metadata

```text
policy_id: finance-trial-and-entitlement-policy
document_type: product-service-policy
product_policy_status: draft
legal_review_status: required
architecture_status: not_started
implementation_status: not_started
runtime_support: not_supported
release_status: not_released
launch_values_status: not_applicable
owner: Product Owner (prototype stage: Project Owner)
canonical_refs:
  - ./finance-product-policy.md
  - ./finance-launch-experiment-values.md
  - ./finance-ai-usage-and-quota-policy.md
  - ./finance-onboarding-and-identity-policy.md
  - ./finance-privacy-and-marketing-gate.md
  - ../05-product-legal-ops/finance-user-data-policy.md
  - ../05-product-legal-ops/finance-disclaimer-and-copy-guide.md
```

이 문서는 Product Policy 초안이다. 결제수단, 결제 API, 자동 갱신, 환불, API,
Database Schema, Entitlement 저장 구조 또는 Runtime 구현을 확정하지 않는다.
문서의 상태는 Architecture, Implementation, Runtime 또는 Release 완료를
의미하지 않으며, Paid public launch는 별도 Legal Review 전까지 blocked다.

## 1. 목적과 범위

이 문서는 Starter, Pro Trial, Paid 및 AI 생성 Entitlement 종료 상태의 제품
의미와 전이 원칙을 정의한다. 영구 Free Plan은 제공하지 않는다.

정확한 기간·활성화 기한·횟수·한도는
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)가
소유한다. 사용자 Usage 차감 기준과 Quota 동작은
[`finance-ai-usage-and-quota-policy.md`](./finance-ai-usage-and-quota-policy.md)가
소유한다.

## 2. Entitlement 상태

| 상태 | 의미 |
|---|---|
| `STARTER_ACTIVE` | 신규 가입자에게 제공되는 기간 제한 기본 AI 생성 Entitlement |
| `PRO_TRIAL_AVAILABLE` | 계정당 한 번 제공되는 Pro Trial의 명시적 활성화 자격 |
| `PRO_TRIAL_ACTIVE` | 사용자가 활성화한 기간 제한 Pro Trial Entitlement |
| `PAID_ACTIVE` | 사용자가 명시적으로 선택해 활성화된 Paid Plan Entitlement |
| `NO_AI_GENERATION_ENTITLEMENT` | 신규 AI 분석 생성 권한이 없는 상태. 계정 삭제나 기존 데이터 접근 차단을 의미하지 않음 |

`PRO_TRIAL_AVAILABLE`는 Starter와 함께 존재할 수 있는 활성화 자격이며, Pro
Trial이 시작되면 `PRO_TRIAL_ACTIVE`가 분석 생성 권한에 우선한다. Paid Plan이
활성화되면 `PAID_ACTIVE`가 우선한다. 구체적인 저장 모델이나 상태 표현은
구현에서 정하지 않는다.

## 3. 신규 가입과 Starter

```text
- 신규 가입 시 기간 제한 Starter Entitlement가 시작된다.
- 신규 가입자에게는 계정당 한 번의 Pro Trial 활성화 자격이 부여된다.
- Pro Trial은 자동 시작하지 않는다.
- 전화번호 없이 기본 가입과 Starter 이용이 가능하다.
- Identity Role 또는 Access Context에 Trial, Plan, Entitlement 또는 Quota를
  인코딩하지 않는다.
```

Starter도 사용자가 제품 가치를 판단할 수 있는 안전하고 유의미한 기본 심층
분석을 제공한다. Starter·Pro Trial·Paid 간 차이는 정확성이나 안전성이 아니라
Usage, 분석 깊이·범위, Lens, 반복·후속 분석 및 편의 기능이다.

## 4. Pro Trial 활성화

```text
- 인증된 사용자가 명시적으로 활성화한다.
- 계정당 한 번만 활성화할 수 있다.
- 활성화 기한과 Trial 기간은 Launch Experiment Value다.
- 자동 결제나 자동 유료 전환을 연결하지 않는다.
- 결제수단 등록을 기본 전제로 하지 않는다.
```

Pro Trial은 일반 Paid Plan에 가까운 경험을 제공할 수 있다. 최상위·고비용
기능은 Trial 전체에서 제한된 횟수만 체험하도록 할 수 있으며, 정확한 횟수와
기능 목록은 Product Policy가 아니라 Launch Experiment Value 및 Open Decision의
범위다.

### 4.1 NOTE-01 — 전화번호·SMS 검증 Gate

전화번호 수집, SMS 검증 또는 이를 이용한 Trial 악용 방지 수단은 현재
Entitlement 활성화의 기본 전제가 아니다. 이를 도입하기 전에는
`finance-privacy-and-marketing-gate.md`의 목적 분리·최소수집 원칙, Contact
검증 상태의 Open Decision 및 외부 법무 검토를 충족해야 한다. Device
Fingerprint, IP 기반 탐지 및 전화번호 인증 구현은 이 문서의 범위 밖이다.

## 5. Starter와 Trial의 시간 관계

```text
- Starter 기간은 가입 시점부터 계속 경과한다.
- Pro Trial을 활성화해도 Starter 시계는 정지하거나 연장되지 않는다.
- Pro Trial 종료 시 Starter 유효기간이 남아 있으면 남은 Starter로 복귀한다.
- Starter 유효기간도 종료됐다면 미결제 사용자는
  NO_AI_GENERATION_ENTITLEMENT 상태가 된다.
```

따라서 Trial 종료 후 영구 Free Entitlement로 복귀하는 경로는 없다. 기간의
정확한 수치와 시간 계산 기준은 Launch Experiment Value 및 후속 구현 검증에서
다룬다.

## 6. 만료 후 데이터 접근

Starter·Pro Trial·Paid Entitlement가 만료돼도 AI 생성 Entitlement와 기존
사용자 데이터 접근 권한은 분리한다.

| 허용 | 차단 |
|---|---|
| 기존 Journal 조회 | 신규 AI 분석 생성 |
| 기존 분석 결과 조회 | 새 고비용 AI 처리 |
| 사용자 데이터 삭제 | — |
| 지원되는 Export | — |

Export는 정책상 Entitlement 만료를 이유로 막지 않는다. Export의 형식, 필드,
보안, 감사 기록, 제공 방식 및 Runtime 지원 여부는
[`finance-user-data-policy.md`](../05-product-legal-ops/finance-user-data-policy.md)와
후속 구현·Legal Review에서 정한다.

## 7. Paid 전환

사용자는 명시적으로 Paid Plan을 선택할 수 있으며, `PAID_ACTIVE`가 되면 Paid
Entitlement가 우선한다. Pro Trial에서 자동 결제 또는 자동 유료 전환은 일어나지
않는다.

가격, 결제수단, 결제 API, 자동 갱신, 환불 및 구체 Paid Plan 상품은 이 문서에서
정의하지 않는다.

## 8. 재가입·Account Linking·악용 경계

```text
- 탈퇴·재가입으로 Pro Trial이 자동 재발급되지 않는다.
- Account Linking 또는 Merge로 Trial 이력이 초기화되지 않는다.
- 계정당 한 번 원칙을 우회하지 못해야 한다.
- 다중 계정, 반복 가입, quota race 및 운영자 보정 악용은 Abuse Validation 대상이다.
- 운영자 보정에는 사유·대상·시점의 Audit가 필요하다.
```

이 원칙은 Product 경계다. 재가입 식별 방식, Account Merge 우선순위, Device
Fingerprint, IP 탐지, 전화번호 검증, Database 또는 운영 도구는 구현 또는 별도
Product Decision 범위다.

## 9. Legal·Cost·Abuse Gate

```text
Legal Gate
- Paid 서비스 이용약관, Starter·Trial 만료 및 자동 결제 없음 고지
- 분석 깊이와 투자 성과·정확성·판단 강화 표현의 분리
- 기존 데이터 조회·삭제·Export 및 분석 결과·Usage Event 보존
- Paid public launch 전 별도 Legal Review

Cost Validation
- 분석 주제당 실제 AI 원가, Starter 최대 원가, Trial 평균·상위 원가
- 최상위 기능 체험 원가와 실패·재시도·Rewrite 비차감 비용
- Starter → Pro Trial → Paid 전환율

Abuse Validation
- 다중 계정 Starter·Trial 반복 수령, 탈퇴·재가입
- Account Linking·Merge 전후 Trial 중복
- quota race, idempotency 우회, 분석 주제 경계 쪼개기, 운영자 보정 악용
```

이 Gate들은 Legal Review, Architecture, Implementation 또는 Runtime 지원이
완료됐다는 뜻이 아니다.

## 10. Open Decision

```text
- Account Merge 시 Trial 이력의 우선순위
- Trial 중 Paid 전환 시 잔여 Trial 처리
- Paid 종료 후 상태 전이와 남은 Starter 처리
- 탈퇴 후 법적 보존기간 중 재가입 식별
- 운영자 Trial 재발급의 예외 조건
- 최상위 기능의 정확한 정의
- Export의 정확한 형식·필드·보안 방식
```

## 11. 소유하지 않는 내용

```text
- User Usage 정의·차감·Quota 소진·보정 세부
  → finance-ai-usage-and-quota-policy.md
- 기간·활성화 기한·횟수·한도·Reset 시각
  → finance-launch-experiment-values.md
- Welcome Promotion Eligibility·지급·회수
  → Batch C
- Contact 검증 상태의 최종 소유 및 Finance Role 정의
  → finance-onboarding-and-identity-policy.md의 Open Decision
- 데이터 보존·삭제·Export 보안·법률 해석
  → 05-product-legal-ops
- 가격·결제·자동 갱신·환불·API·Database·구현
```
