# Finance Product Policy

## 0. Metadata

```text
policy_id: finance-product-policy
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
  - ../00-core/0-a-safety-core/00-service-policy.md
  - ../00-core/0-a-safety-core/finance-policyguard-rules.md
  - ../05-product-legal-ops/finance-user-data-policy.md
  - ../05-product-legal-ops/finance-disclaimer-and-copy-guide.md
  - ../06-professional-standards/README.md
```

이 문서가 존재한다는 사실은 Architecture 설계, API 설계, Database Schema,
Migration, 또는 Implementation 완료를 의미하지 않는다. 위 Metadata의
`architecture_status`, `implementation_status`, `runtime_support`,
`release_status`가 각각 별도로 실제 상태를 나타낸다.

## 1. 문서 목적

이 문서는 `service-policy` 계층의 Master Product Policy다. 가입, Welcome
Promotion, Starter, Trial, Usage, Entitlement, 마케팅 정책의 불변 원칙과
Starter/Pro Trial/Paid Feature Matrix를 정의한다.

정확한 수치(기간, Usage Unit, 가중치, 신청 가능 일수 등)는 이 문서가
소유하지 않는다. 수치는 `finance-launch-experiment-values.md`가 소유한다.

가입·Promotion·Trial·Usage·마케팅의 세부 정책은 이 문서가 아니라
[README.md](./README.md) §4의 후속 계획 문서에서 관리한다. 이 문서는
그 하위 문서들이 따라야 할 원칙만 고정한다.

## 2. 제품 목적

금융 하네스는 다음을 위한 제품이다.

```text
투자 질문 구조화
판단 전 Checklist
사용자 Journal
과거 기록 Review
Market Context 이해
```

금융 하네스는 다음을 제공하지 않는다.

```text
매수·매도 권유
종목 추천
목표가·손절가
수량·비중 지시
자동매매
수익 보장
```

상세 안전 규칙은 이 문서에 복사하지 않는다. 다음 문서가 canonical이다.

- [`00-core/0-a-safety-core/00-service-policy.md`](../00-core/0-a-safety-core/00-service-policy.md)
- [`00-core/0-a-safety-core/finance-policyguard-rules.md`](../00-core/0-a-safety-core/finance-policyguard-rules.md)

## 3. 가입 원칙

```text
Kakao-first이지만 Kakao-only가 아니다
OAuth Core는 Provider-neutral이다
전화번호 없이 가입 후 Starter 이용을 할 수 있다
이메일·전화번호는 자동 Account 병합 기준이 아니다
Account Linking은 인증된 Account에서 명시적으로 수행한다
```

세부 Identity 정책(Provider별 UX, Account 모델, 전화번호 검증 흐름 등)은
후속 문서 `finance-onboarding-and-identity-policy.md`에서 관리한다.

## 4. Welcome Promotion 원칙

```text
기본 가입과 분리된 선택 혜택이다
전화번호 검증 후 신청할 수 있다
원칙적으로 1회 지급한다
중복 수령을 방지한다
탈퇴·재가입 후 자동 재지급하지 않는다
마케팅 수신 동의와 분리한다
```

세부 Eligibility 조건, 중복 검사 방식, 운영자 예외 승인 절차는 후속 문서
`finance-welcome-promotion-policy.md`에서 관리한다.

## 5. Starter·Trial·Entitlement 원칙

```text
영구 무료 Plan은 제공하지 않는다
신규 가입자에게 기간 제한 Starter Entitlement를 제공한다
Starter에서도 안전하고 유의미한 기본 심층 분석을 제공한다
Pro Trial은 사용자가 명시적으로 활성화한다
Pro Trial은 계정당 한 번의 활성화 자격이다
Pro Trial은 자동 결제나 자동 유료 전환으로 연결되지 않는다
Starter 기간은 Pro Trial 중에도 계속 경과한다
Trial 종료 후 Starter 기간이 남았을 때만 Starter로 복귀한다
Starter까지 만료되면 미결제 사용자의 신규 AI 분석 생성을 중단한다
Entitlement가 만료돼도 기존 Journal과 기존 분석 결과의 조회·삭제·Export는 유지한다
```

정확한 Trial 기간은 이 문서에 고정하지 않는다. 후속 문서
`finance-trial-and-entitlement-policy.md`와
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)를
참조한다.

## 6. Usage 원칙

```text
사용자에게 의미 있는 분석 주제와 완료된 분석 결과를 Usage 단위로 사용한다
내부 모델 호출 횟수는 사용자 Usage가 아니다
질문 구조화 호출과 최종 분석 호출을 각각 차감하지 않는다
전체 한도와 일간 한도가 존재한다
고비용 기능은 별도 가중치 또는 제한을 적용한다
서비스 내부 실패는 원칙적으로 사용자 Usage를 차감하지 않는다
동일 요청의 재전송은 중복 차감하지 않는다
```

정확한 Usage Unit 수치는 이 문서가 소유하지 않는다. 후속 문서
`finance-ai-usage-and-quota-policy.md`와
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)가
소유한다.

## 7. 마케팅 원칙

```text
Promotion 신청과 마케팅 수신 동의를 분리한다
채널별 동의를 분리한다
수신 동의는 철회할 수 있다
운영 메시지와 광고 메시지를 구분한다
```

세부 채널 정책과 개인정보 취급은 후속 문서
`finance-privacy-and-marketing-gate.md`와
[`05-product-legal-ops/finance-user-data-policy.md`](../05-product-legal-ops/finance-user-data-policy.md)를
참조한다.

## 8. Starter·Trial·유료 Feature Matrix

아래는 개념 Matrix다. 정확한 횟수나 Usage Unit 수치는 포함하지 않는다.

| 기능 | Starter | Pro Trial | Paid Plan | AI 생성 Entitlement 종료 |
|---|---|---|---|---|
| 질문 및 분석 | 기본 심층 분석·제한된 Usage | Paid에 가까운 범위 | Plan별 확장 | 신규 생성 차단 |
| Checklist·Journal·기존 결과 조회 | 제공 | 제공 | 제공 | 기존 데이터 접근 유지 |
| 고급 Lens | 제한 | 제공 | Plan별 확장 | 신규 AI 처리 차단 |
| 최상위·고비용 기능 | 제한 또는 제외 | 제한 체험 | Plan별 제공 | 신규 AI 처리 차단 |
| 반복·후속 분석 | 제한 | 제공 | Plan별 확장 | 신규 AI 처리 차단 |
| 편의 기능 | 제한 | Paid에 가까운 범위 | Plan별 확장 | 기존 데이터 접근 범위에서 유지 |
| API·자동화 연동 | 현재 범위 밖 | 현재 범위 밖 | 현재 범위 밖 | 현재 범위 밖 |

각 셀의 정확한 한도, 제한 방식, 제공 횟수는
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)와
후속 세부 정책 문서가 소유한다.

### 원칙

```text
Starter·Pro Trial·Paid의 차이는 투자 판단 정확성이나 안전성의 우열이 아니다.
모든 Plan은 동일한 Safety·PolicyGuard·Evidence 기준을 적용한다.
Plan별 차이는 사용량, 분석 깊이, 분석 범위, Lens, 반복·후속 분석 및 편의 기능이다.
분석 깊이는 투자 성과, 정확성 또는 더 강한 투자 판단을 약속하는 광고 표현이 아니다.
```

## 9. 이 문서가 소유하지 않는 것

```text
PolicyGuard 상세 규칙 (00-core 소유)
Output Validator 규칙 (00-core 소유)
Lens 정의와 Routing (00-core, 01/02/03 소유)
개인정보 최소수집·보존·삭제 원칙 본문 (05-product-legal-ops 소유)
카피 일반 제한 본문 (05-product-legal-ops 소유)
Evidence·Uncertainty·Quality·Human Review 기준 (06-professional-standards 소유)
정확한 Trial 기간, Usage Unit, 가중치, 신청 가능 일수 (finance-launch-experiment-values.md 소유)
Architecture, API, Database Schema, Migration, 구현 세부
```
