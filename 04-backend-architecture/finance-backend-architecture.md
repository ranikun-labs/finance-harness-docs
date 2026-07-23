# Finance Backend Architecture

## 0. Metadata

```text
status            : decided v1.0 — Finance Backend canonical architecture
document type     : Architecture Decision Record (ADR)
scope             : Finance Backend의 Domain 소유권, 외부 Identity/Commerce
                    경계, CurrentUserPort, EntitlementPort, V1 adapter 방향,
                    사용자 데이터 소유권 검사, 물리 배포 유예
decision owner    : Finance Backend Architecture
canonical for     :
  - Finance Backend application/domain boundary
  - Identity integration boundary
  - Commerce entitlement boundary
  - V1 local adapter direction
non-goals         :
  - Java 코드·패키지·서버 skeleton
  - Identity Service 또는 Commerce Service 구현
  - 결제 테이블·PG provider·Refund flow
  - JWT/JWKS 상세
  - 서비스 간 이벤트·분산 트랜잭션
```

본 문서는 Finance Backend의 책임 경계와 외부 플랫폼 연동 원칙에 대한
canonical Source of Truth다.

서비스 목적과 금융 안전정책은 `00-service-policy.md`,
`finance-policyguard-rules.md`, `finance-output-validator.md`가 우선한다.
사용자 데이터의 수집·보존·삭제 기준은
`05-product-legal-ops/finance-user-data-policy.md`가 소유한다. 본 문서는
그 정책을 구현 구조로 연결하되 의미를 완화하거나 재정의하지 않는다.

---

## 1. 결정 요약

```text
Finance Backend V1
= Finance Domain
+ CurrentUserPort
+ LocalDevCurrentUserAdapter
+ EntitlementPort
+ AllowAllEntitlementAdapter 또는 승인된 Local Adapter
+ Finance PolicyGuard
+ Output Validator

실제 Shared Identity 의존 없음
실제 Commerce 의존 없음
실제 Billing / Payment / Quota 없음
물리 Identity / Commerce MSA 승인 없음
```

Finance는 외부 사용자를 opaque한 `identity_user_id`로만 참조한다.
Identity Account와 Finance Profile을 분리하며, Finance 데이터 접근은
현재 사용자의 `identity_user_id`와 데이터 소유권을 대조해 결정한다.

---

## 2. Finance Domain 소유 범위

Finance Domain은 다음을 소유한다.

```text
- Finance Profile
- Question
- Checklist와 Checklist 결과
- Journal
- Review와 복기 결과
- Watchlist
- Finance PolicyGuard 실행
- Output Validator 실행
- 위 Finance 데이터의 identity_user_id 기반 소유권
```

Finance Domain은 다음을 소유하지 않는다.

```text
- Account
- Password
- Credential
- Login / Logout
- Access Token
- Refresh Token
- Session
- Identity DB 구조
- 다른 제품의 User Entity, Organization, Role
- Product Membership
- Product / Price
- Subscription
- Billing / Payment
- Entitlement / Quota의 정책 원본
- Payment Provider 세부 구현
```

Finance가 `identity_user_id`를 저장하는 것은 Identity Account를 소유한다는
뜻이 아니다. Finance는 외부 식별자를 Finance 데이터의 소유권 키로
참조할 뿐이다.

---

## 3. Identity Account와 Finance Profile

Identity Account는 로그인 가능한 주체와 Credential·Token·Session을
관리하는 외부 Identity 경계의 개념이다.

Finance Profile은 Finance 제품 안에서만 의미가 있는 사용자별 설정과
표시 데이터를 관리하는 Finance Domain 개념이다.

두 모델은 다음 원칙을 지킨다.

| 원칙 | 결정 |
|---|---|
| 식별 | Finance Profile은 `identity_user_id`로 외부 사용자를 참조한다. |
| 분리 | Finance Profile은 Identity Account의 복제본이 아니다. |
| 금지 | Password, Credential, Token, Session을 Finance Profile에 저장하지 않는다. |
| 제품 격리 | Carelog User Entity, Organization, Role을 Finance 모델에 포함하지 않는다. |
| 변경 영향 | Identity adapter가 교체되어도 Finance Domain 모델과 use case 계약은 바뀌지 않아야 한다. |

---

## 4. `identity_user_id` 불변 원칙

`identity_user_id`는 다음 불변 조건을 갖는다.

1. 외부 Identity 경계가 부여하는 opaque하고 stable한 식별자다.
2. Finance는 값의 생성 규칙이나 내부 구조를 해석하지 않는다.
3. 이메일, 로그인명, 다른 제품의 사용자 PK로 대체하지 않는다.
4. FinanceProfile의 내부 식별자와 구분한다.
5. Journal, Question, Checklist, Review, Watchlist의 소유권 연결에 사용한다.
6. LLM prompt, 사용자 응답, 일반 metric label에 노출하지 않는다.
7. 로그에는 원문 대신 목적이 제한된 비식별 correlation key를 우선한다.
8. Identity Account 삭제 시 Finance가 소유한 데이터의 삭제·보존 처리는
   `finance-user-data-policy.md`를 따른다.

V1 Local Adapter가 반환하는 값도 이 계약을 따라야 한다. 이후 실제
Shared Identity로 전환할 때 Domain 데이터의 소유권 모델을 다시 만들지
않기 위해서다.

---

## 5. Current User 경계

### 5.1 `CurrentUserPort`

`CurrentUserPort`는 Finance Application이 현재 요청의 외부 사용자
식별자를 얻는 유일한 경계다.

논리 계약:

```text
CurrentUserPort
  → current user
  → identity_user_id
```

`CurrentUserPort`는 Password, Credential, Access Token, Refresh Token,
Session 또는 Identity 내부 Entity를 Finance Domain에 반환하지 않는다.
Controller나 use case가 인증 구현 세부를 직접 해석하지 않는다.

### 5.2 V1 `LocalDevCurrentUserAdapter`

V1은 실제 Shared Identity Service에 의존하지 않는다.
`LocalDevCurrentUserAdapter`가 개발·prototype 범위에서 안정된
`identity_user_id`를 제공한다.

이 adapter는 다음을 의미하지 않는다.

```text
- Finance가 Account를 구현함
- Finance가 Password나 Session을 저장함
- 임시 사용자 ID를 Domain 전체에 하드코딩함
- Local Adapter를 production Identity로 승인함
```

Local Adapter의 허용 환경과 값 주입 방식은 실제 구현 착수 시 별도로
승인한다. 본 문서는 Java class, package 또는 configuration을 확정하지
않는다.

### 5.3 향후 `JwtCurrentUserAdapter`

실제 Shared Identity 연동 단계에서는 `CurrentUserPort` 뒤의 adapter를
`JwtCurrentUserAdapter`로 교체할 수 있다.

이 결정은 adapter 명칭과 책임 경계만 승인한다. JWT 형식, claim,
검증 방식, JWKS, key rotation, Login/Logout, Token 발급·갱신 상세는
승인하거나 설계하지 않는다.

---

## 6. Finance 데이터 소유권 검사

Finance 데이터의 조회·수정·삭제는 현재 사용자의 `identity_user_id`와
대상 데이터의 소유권을 대조해야 한다.

```text
CurrentUserPort에서 identity_user_id 확인
  → 대상 Finance 데이터 조회
  → owner identity_user_id 일치 확인
  → 허용된 Finance use case 실행
```

불변 규칙:

- 요청 payload나 URL에 포함된 사용자 식별자를 신뢰하지 않는다.
- 다른 사용자의 Journal, Question, Checklist, Review, Watchlist에 접근할
  수 있는 우회 경로를 두지 않는다.
- Repository 조회 조건 또는 동등한 Application 경계에서 소유권을
  강제한다.
- 운영·지원 목적의 예외 접근은 일반 사용자 경로와 분리하고
  `finance-user-data-policy.md`의 승인·감사 기준을 따라야 한다.
- 소유권 확인은 Entitlement 확인으로 대체할 수 없다.

---

## 7. Commerce와 Entitlement 경계

Commerce는 미래에 다음 정책의 원본을 소유할 수 있다.

```text
- Product Membership
- Product / Price
- Subscription
- Billing / Payment
- Entitlement
- Quota
```

Finance는 Payment Provider, 결제 이력, 구독 상태 머신을 직접 해석하지
않는다. Finance가 필요한 것은 특정 Finance 기능을 사용할 수 있는지에
대한 결과뿐이다.

### 7.1 미래 `EntitlementPort`

`EntitlementPort`는 Finance Application과 미래 Commerce 경계 사이의
논리적 포트다.

```text
EntitlementPort
  → identity_user_id + Finance feature
  → allowed / denied
```

이 계약은 결제·구독 구현을 승인하지 않는다. Entitlement key, 장애
정책, cache, quota 계측 상세도 후속 결정이다.

### 7.2 V1 Adapter

V1은 실제 Commerce Service에 의존하지 않는다.

- 기본 방향은 `AllowAllEntitlementAdapter`다.
- 로컬 플랜 구분이 제품 검증에 꼭 필요하다고 별도 승인된 경우에만
  Commerce 비의존 `LocalPlanEntitlementAdapter`를 사용할 수 있다.
- 두 adapter 모두 Billing, Payment, Subscription, Quota를 구현하지 않는다.
- V1은 결제·구독 데이터를 수집하지 않는다.

### 7.3 Finance 안전정책 불변

Commerce와 Entitlement는 “이 기능을 사용할 수 있는가”만 결정한다.
Finance는 “이 기능이 어떤 금융 분석을 제공할 수 있는가”를 결정한다.

따라서 무료·유료·내부 플랜 모두 다음 경계를 동일하게 적용한다.

```text
Finance PolicyGuard
Finance Routing Contract
Finance Output Validator
Market Data Grounding
금지된 투자 판단·표현
```

Entitlement의 `allowed` 결과는 PolicyGuard, Output Validator 또는 다른
Finance 안전 계약을 우회하거나 완화할 수 없다. 유료 플랜이 더 강한
판단, 추천, 목표가, 수량·비중 또는 계좌 기반 조언을 제공하는 구조도
허용하지 않는다.

---

## 8. V1 요청 처리 경계

V1의 논리적 처리 순서는 다음과 같다.

```text
Request
  → CurrentUserPort
  → Finance data ownership check
  → EntitlementPort (V1 local adapter)
  → Finance PolicyGuard
  → Lens Routing / Use Case
  → LLM Generation where applicable
  → Output Validator
  → Sanitized Response
```

소유권 검사와 Entitlement 검사는 서로 대체할 수 없다.
Entitlement가 거절된 요청은 Finance 기능을 실행하지 않지만,
Entitlement가 허용된 요청도 모든 Finance 안전 계층을 통과해야 한다.

---

## 9. V1과 후속 단계

| 경계 | V1 | 후속 단계 |
|---|---|---|
| Current user | `CurrentUserPort` + `LocalDevCurrentUserAdapter` | `JwtCurrentUserAdapter`를 통한 Shared Identity 연동 검토 |
| Identity data | `identity_user_id`만 참조 | 실제 Identity 의존 승인 후에도 동일한 opaque 참조 유지 |
| Entitlement | `EntitlementPort` + AllowAll 또는 승인된 Local Adapter | Commerce Entitlement Adapter 검토 |
| Billing / Payment | 없음 | 별도 제품·법무·보안 승인 전까지 유예 |
| Quota | 없음 | Commerce 정책과 실제 필요가 승인된 뒤 검토 |
| 물리 배포 | Finance 경계 안의 논리적 port/adapter | 별도 ADR 없이는 Identity·Commerce MSA로 분리하지 않음 |

Shared Identity와 Commerce는 동급의 서로 다른 논리 경계다.
Payment를 Identity에 포함하지 않는다. 다만 이 논리 분리는 지금 물리
서비스, 별도 저장소, 별도 배포 단위 또는 네트워크 호출을 승인한다는
뜻이 아니다.

---

## 10. 물리 MSA 유예

현재 결정은 port와 adapter를 통한 논리적 책임 분리다.

다음은 아직 승인하지 않는다.

```text
- 물리 Identity MSA
- 물리 Commerce MSA
- 별도 서버 repository 또는 deployment
- 서비스 간 동기/비동기 통신
- 서비스 간 이벤트
- 분산 트랜잭션
- 독립 장애·재시도·보상 처리
```

물리 분리는 실제 다제품 공유 수요, 독립 배포 필요, 운영 책임,
장애 격리, 보안 요구와 비용을 근거로 별도 ADR에서 결정해야 한다.

---

## 11. Legacy 문서와의 관계

`legacy-review-pending/90-meta-handoff/finance-harness-mvp-implementation-handoff-v1_2.md`
등 `legacy-review-pending` 문서는 역사적 참고 자료다.

해당 문서의 Spring Boot 구성, Finance-owned User/Password 모델, API 후보,
PR 순서는 canonical Backend Architecture가 아니다. 본 문서 또는 현행
canonical 정책과 충돌하면 본 문서와 상위 Safety Core가 우선한다.
Legacy 문서는 보존하며 수정하지 않는다.

---

## 12. 명시적 비목표

본 결정으로 다음을 생성·승인하지 않는다.

```text
- 실제 Java 코드, package, interface, adapter 구현
- Identity Service 또는 Commerce Service
- Account/Credential/Session 저장소
- 결제·구독 테이블
- PG provider 선정
- Refund flow
- Subscription state machine
- JWT/JWKS 상세
- 서비스 간 이벤트
- 분산 트랜잭션
- Commerce server skeleton
- 실제 Billing / Payment / Quota
```

---

## 13. 최종 결정

```text
Finance Backend의 사용자 경계
= opaque identity_user_id
+ CurrentUserPort
+ V1 LocalDevCurrentUserAdapter

Finance Backend의 미래 상업 경계
= EntitlementPort
+ V1 AllowAll 또는 승인된 Local Adapter

Finance가 계속 소유하는 것
= Finance Profile과 Finance 데이터
+ 데이터 소유권 검사
+ Finance PolicyGuard
+ Output Validator

현재 승인하지 않는 것
= 실제 Shared Identity / Commerce 의존
+ Billing / Payment / Quota
+ 물리 Identity / Commerce MSA
```
