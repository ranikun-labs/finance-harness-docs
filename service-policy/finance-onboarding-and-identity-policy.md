# Finance Onboarding and Identity Policy

## 0. Metadata

```text
policy_id: finance-onboarding-and-identity-policy
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
  - ../05-product-legal-ops/finance-user-data-policy.md
  - ../05-product-legal-ops/finance-disclaimer-and-copy-guide.md
```

이 문서는 Product Policy 초안이다. 구체적인 가입 UI, 동의 문구, OAuth
Endpoint, Token Claim, API, 저장 구조 또는 서비스 배치를 확정하지 않으며
법률 결론을 대신하지 않는다. 출시 전 외부 법무 검토가 필요하다.

## 1. 문서 목적과 상위 정책

이 문서는 [`finance-product-policy.md`](./finance-product-policy.md)의 가입·
Identity 원칙을 Finance Product Onboarding에 적용하기 위한 하위 정책이다.
Kakao-first, Provider-neutral Identity, 명시적 Account Linking 및 전화번호
없는 기본 이용 원칙은 Master Product Policy에서 상속하며 여기서 다시
선택지로 열지 않는다.

이 문서가 정의하는 것은 Product 책임과 사용자 흐름의 경계다. 실제
Architecture, Identity Provider 연동 방식 및 데이터 저장 구조는
`architecture_status: not_started` 상태이며 이 문서의 책임이 아니다.

## 2. Identity 개념

### 2.1 Platform Account

Platform Account는 여러 Product에서 동일 사용자를 안정적으로 참조하기
위한 Shared Identity의 논리적 Account다.

```text
- 내부 Principal은 변경되지 않는 stable accountId로 식별한다.
- Finance는 Platform Account의 Credential, Token 또는 Session을 소유하지 않는다.
- Finance는 Provider 식별자가 아닌 opaque한 Identity 참조로 Account를 연결하고,
  Finance Domain 데이터만 소유한다.
```

Shared Identity 내부의 `stable accountId`와 Finance가 사용하는 opaque
Identity 참조의 물리적 매핑은 이 문서에서 정하지 않는다. `stable accountId`는
Product 식별 원칙이며 Token Claim, API 필드명 또는 Database Schema를
확정하는 표현이 아니다.

### 2.2 External Identity

External Identity는 외부 Provider가 증명한 사용자 식별 관계다.

```text
- Provider와 provider_subject의 조합이 External Identity를 구분한다.
- provider_subject는 내부 Principal 또는 Contact 정보가 아니다.
- Kakao는 첫 Launch UX의 우선 Provider지만 Identity 모델은 Kakao에 종속되지 않는다.
- 다른 Provider를 추가해도 Platform Account와 Finance Profile의 의미는 바뀌지 않는다.
```

`provider_subject`는 Product 수준의 식별 개념이다. Provider별 Claim,
Endpoint 또는 SDK 설정은 이 문서에서 정하지 않는다.

### 2.3 Contact 정보

이메일과 전화번호는 변경 가능한 Contact 또는 Profile 정보다. stable
accountId나 External Identity의 대체 식별자가 아니며, 값이 같다는 이유만으로
Account를 자동 병합하지 않는다.

Provider가 Contact 값을 제공했다는 사실, Provider가 그 값을 검증했다고
표시한 사실, Finance가 별도 SMS 절차로 검증한 사실은 서로 다른
provenance와 의미를 가진다. 하나를 다른 검증 결과로 간주하지 않는다.

## 3. 가입과 Finance Product Onboarding

### 3.1 기본 가입

기본 가입은 Platform Account와 External Identity 관계를 통해 인증 가능한
사용자를 만드는 단계다.

```text
- Kakao-first이지만 Kakao-only가 아니다.
- 전화번호 없이 기본 가입을 완료할 수 있다.
- 전화번호 없이 가입 후 Starter 이용을 할 수 있다.
- 가입 단계에서 향후 혜택·마케팅 목적의 Contact 정보를 선수집하지 않는다.
```

### 3.2 Finance Product Onboarding

Finance Product Onboarding은 인증된 Platform Account가 Finance Product를
처음 사용하는 데 필요한 Product-specific 상태를 만드는 별도 단계다.

Finance Product가 소유하는 범위는 다음과 같다.

```text
- Finance Profile
- Finance Product Onboarding 상태
- Finance-specific Consent
- Finance-specific Role 또는 Access Context의 소유 경계
```

Finance Product Onboarding은 Platform Account 생성이나 External Identity
연결을 재구현하지 않는다. 기본 가입 완료가 Finance-specific Consent의
획득이나 Finance Product Onboarding 완료를 자동으로 의미하지도 않는다.

## 4. Kakao-first와 Provider-neutral 원칙

Kakao-first는 첫 가입 경로의 Product 우선순위다. Kakao만 허용한다는
Identity 제약이 아니다.

```text
- 사용자 대면 첫 경로는 Kakao를 우선할 수 있다.
- 내부 Principal과 Finance 데이터는 Provider 종류로 식별하지 않는다.
- Provider별 응답 차이는 External Identity와 Contact provenance로 보존한다.
- Provider 추가가 Account 자동 병합 또는 Finance 데이터 이동을 유발하지 않는다.
```

가입 흐름에 변경 가능한 기간·횟수·임계값이 필요하면
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)가
그 값을 소유한다. 이 문서는 해당 숫자를 고정하지 않는다.

## 5. Contact와 검증 상태

Contact 정보는 목적별로 최소 처리한다. Finance가 전화번호를 요구할 수 있는
경우는 기본 가입이 아니라 별도 선택 기능이 명확한 목적을 가지는 경우다.
개인정보 처리 기준은
[`finance-user-data-policy.md`](../05-product-legal-ops/finance-user-data-policy.md)를
따르며, 사용자 대면 문구는
[`finance-disclaimer-and-copy-guide.md`](../05-product-legal-ops/finance-disclaimer-and-copy-guide.md)의
검토 경계를 약화하지 않는다.

검증 출처는 최소한 다음처럼 구분한다.

| 상태 | 의미 | 의미하지 않는 것 |
|---|---|---|
| Provider 제공 Contact | Provider 응답에 Contact 값이 포함됨 | 서비스 자체 검증 완료 |
| Provider 검증 표시 | Provider가 자신의 기준으로 검증 상태를 제공함 | Finance SMS 검증 완료 |
| 서비스 자체 SMS 검증 | Finance 목적의 별도 검증 절차가 성공함 | Platform Account 또는 External Identity의 대체 식별 |

검증 상태의 물리 저장 위치, 서비스 배치 및 동기화 방식은 Architecture와
Implementation 범위이며 아직 시작하지 않는다.

## 6. Account Linking 원칙

Account Linking은 인증된 Platform Account에서 사용자가 명시적으로
수행하는 작업이다.

```text
- 이메일 또는 전화번호 일치만으로 자동 Linking하지 않는다.
- 새 Provider 로그인 성공만으로 기존 Account와 자동 병합하지 않는다.
- Linking 전에 현재 Account와 추가할 External Identity 양쪽의 인증을 확인한다.
- Linking 결과와 영향은 사용자에게 확인 가능한 형태로 제공해야 한다.
- 충돌·중복·복구 정책이 확정되기 전에는 임의 병합이나 데이터 이동을 하지 않는다.
```

위 원칙은 Product 안전 조건이다. 인증 프로토콜, 재인증 방식, 오류 코드,
API 또는 Transaction 설계는 이 문서에서 확정하지 않는다.

## 7. Shared Identity와 Finance Product 책임 경계

| 책임 | 논리적 소유 경계 |
|---|---|
| Platform Account | Shared Identity |
| External Identity | Shared Identity |
| 인증 상태 | Shared Identity |
| stable accountId | Shared Identity |
| Finance Profile | Finance Product |
| Finance Product Onboarding 상태 | Finance Product |
| Finance-specific Consent | Finance Product |
| Finance-specific Role / Access Context | Finance Product 경계 후보, 정확한 의미는 Open Decision |
| Contact 검증 상태 | Shared Identity 논리적 소유 후보, 별도 Product Decision 필요 |

Finance-specific Role 또는 Access Context는 Finance 기능 안에서의 역할·
접근 맥락만 표현해야 한다. Trial, Paid Entitlement, Plan, Subscription,
AI Usage Quota 또는 Promotion 권한을 인코딩하지 않는다.

## 8. Open Product Decisions

### 8.1 Contact 검증 상태의 논리적 소유권

Decision required:
Contact 검증 상태를 Shared Identity가 소유할지, 각 Product가
Product-specific Verification 상태를 소유할지 별도 Product Decision이
필요하다.

Options:
- Option A — Shared Identity가 Contact별 검증 provenance와 상태를 논리적으로 소유한다.
- Option B — 각 Product가 자신의 목적에 한정된 Verification 상태를 소유한다.

Why not fixed here:
현재 canonical 문서는 Contact 값과 검증 출처의 구분은 요구하지만, 여러
Product 사이에서 검증 상태를 재사용할 책임과 법적 목적 제한을 확정하지
않는다.

Current temporary state:
Shared Identity의 논리적 소유 후보로만 기록한다. 별도 Decision 전에는
Architecture와 Implementation을 시작하지 않으며, Provider 검증과 Finance
자체 검증을 서로 대체하지 않는다.

### 8.2 Finance-specific Role 또는 Access Context의 정확한 정의

Decision required:
Finance-specific Role 또는 Access Context가 어떤 Finance 기능의 역할과
접근 맥락을 표현해야 하는지 별도 Product Decision이 필요하다.

Options:
- Option A — Finance 운영·사용자 역할만 표현하는 좁은 Role 모델을 둔다.
- Option B — Role 없이 Finance 기능별 Access Context만 명시한다.

Why not fixed here:
현재 canonical 문서는 Finance Product의 소유 경계만 제시하며 필요한
역할 종류와 접근 맥락을 확정하지 않는다. Batch B가 소유하는 Trial·Usage·
Entitlement와 결합하면 책임 경계가 흐려진다.

Temporary state:
Finance Product의 논리적 소유 후보로만 기록한다. 별도 Decision 전에는
Role 또는 Access Context를 구현하지 않으며 Entitlement 계층을 인코딩하지
않는다.

## 9. 후속 정책 경계

다음 내용은 이 문서에서 정의하지 않는다.

```text
- Trial, Usage, Paid Entitlement, Plan 및 Subscription
  → finance-ai-usage-and-quota-policy.md
  → finance-trial-and-entitlement-policy.md
- Welcome Promotion Eligibility, 중복 지급, 지급·회수·재지급
  → finance-welcome-promotion-policy.md
- OAuth Endpoint, Token Claim, API, DTO, Database 및 Migration
  → 후속 Architecture와 Implementation
```

후속 문서가 생성되기 전까지 파일명은 계획 참조이며, 이 문서가 해당 정책을
선행 확정하지 않는다.

## 10. 법률 검토 경계

이 문서는 `legal_review_status: required`인 Product Policy 초안이다.

```text
- Contact 수집·이용 목적과 고지 방식은 외부 법무 검토가 필요하다.
- Provider 정보와 자체 검증 결과의 사용자 고지 방식은 외부 법무 검토가 필요하다.
- 구체적인 동의 문구, Checkbox 카피 및 UI는 이 문서에서 확정하지 않는다.
- 이 문서는 개인정보처리방침, 약관 또는 법률 자문을 대체하지 않는다.
```
