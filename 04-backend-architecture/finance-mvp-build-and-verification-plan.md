# Finance MVP Build and Verification Plan

## 0. Metadata

```text
status           : implementation-readiness v1
document type    : build and verification plan
implementation   : not started
release          : not approved by this document
```

## 1. Document Purpose

이 문서는 canonical Architecture와 Policy를 변경하지 않고 V1을 작은 검증
단위로 구현하는 순서를 정한다. backend/FE repository 구현이나 release 계획은
이 문서의 승인 범위가 아니다.

## 2. Build Principles

- CurrentUserPort와 ownership을 모든 사용자 resource보다 먼저 고정한다.
- 안전 처리와 Output Validator는 분석 기능의 선택 옵션이 아니다.
- 명시 Journal 저장은 완료 결과 이후에만 허용한다.
- 실패·중복·소유권 우회는 happy path와 같은 batch에서 검증한다.
- provider 세부와 Finance Domain을 분리하고, FE mock을 계약으로 복사하지 않는다.

## 3. Prerequisites

상위 Architecture, Product Policy, Routing/PolicyGuard/Validator contract와 User
Data Policy를 읽고 구현 대상 backend repository의 local build·test baseline을
확인한다. 실제 Shared Identity, Commerce, Billing, Quota, provider 선택은 전제조건이 아니다.

## 4. Batch 0 — Backend Repository Readiness

repository module, local test 실행 방법, configuration의 비밀정보 비기록, failure
baseline을 확인한다. 이 batch는 API·schema·외부 service 구현을 승인하지 않는다.

## 5. Batch 1 — Current User and Domain Foundation

CurrentUserPort와 LocalDevCurrentUserAdapter, identity_user_id ownership, Question·
ChecklistResult·AnalysisRequest·AnalysisResult의 논리 경계를 구현한다. FE user ID는
받지 않으며 타인 resource 접근은 차단한다.

## 6. Batch 2 — Question·Checklist·General Analysis

Question 생성, ChecklistResult 생성·확인, AllowAll entitlement 확인, PolicyGuard,
General Lens routing, 분석 실행, Output Validator, AnalysisResult 상태를 연결한다.
Checklist 확인 전 실행, guard 우회, validator 미통과 결과 노출을 실패로 본다.

## 7. Batch 3 — Explicit Journal Save and Read

COMPLETED AnalysisResult만 명시 Journal 저장에 연결하고 Journal 목록·상세 read
model을 검증한다. 자동 draft 생성, 결과 본문 복제, 타인 Journal 조회는 허용하지 않는다.

## 8. Batch 4 — Review

Journal 참조 Review를 추가한다. 기록 기반 관찰과 다음 기록 질문만 허용하며,
과거 판단 평가·매매 지시·계좌/수익률/시장 결과 자동 연결을 회귀 대상으로 둔다.

## 9. Batch 5 — Failure·Idempotency·Ownership Hardening

current user 실패, ownership mismatch, entitlement 거절, invalid input, policy
block, execution/output validation failure, resource 없음, duplicate request를
검증한다. 재전송이 중복 Analysis 또는 Journal을 만들지 않아야 한다.

## 10. Batch 6 — Execution and Cost Telemetry

product analysis ID와 provider execution correlation을 연결한다. Finance에는
기능·결과·제품 수준 correlation만 두고, AI Runtime/V1 AI Adapter에서 execution,
token, retry, latency 및 provider cost execution summary를 관측한다. 가격 계산은
Finance Domain 책임이 아니다.

## 11. Deferred Entitlement·Trial Runtime

Starter, Trial, Paid, Usage, quota 차감·복구, Billing·Payment·Subscription은
별도 Runtime 승인이 있기 전까지 구현하지 않는다. V1 AllowAll adapter는 이
유예를 숨기지 않는다.

## 12. FE Integration Gates

| 시점 | 연결 gate |
|---|---|
| Onboarding | 정적 UI 가능; 인증 contract 불필요 |
| Ask | Question/Checklist/Analysis 상태와 오류 경계 준비 후 |
| Journal Detail | Journal read model과 completed AnalysisResult 참조 준비 후 |
| Journal Review | Review contract와 안전 회귀 준비 후 |
| Journal Create | 명시 저장·idempotency 준비 후 |
| Home | 최근 Journal·Analysis summary contract 준비 후 |

FE STEP 9 순서와 FE repository는 변경하지 않는다.

## 13. Fixture and Test Strategy

PolicyGuard fixture, routing regression, Output Validator failure case와 domain
unit/integration boundary를 함께 검증한다. 테스트 fixture에는 실제 사용자 원문이나
FE mock type을 사용하지 않고, 소유권·idempotency·상태 전이를 독립적으로 단언한다.

## 14. Vertical Slice Acceptance Criteria

- FE는 user ID를 전송하지 않고 Backend가 CurrentUserPort에서 identity_user_id를 얻는다.
- 다른 사용자의 resource 접근은 불가하다.
- Checklist 확인 전 Analysis 실행은 불가하다.
- Policy 차단 요청은 LLM 실행 전 또는 승인된 guard 경계에서 차단된다.
- COMPLETED 결과만 Journal에 연결할 수 있다.
- 실패 결과는 Usage 차감을 발생시키지 않는다. V1에는 Quota Runtime 자체가 없다.
- 동일 idempotency 요청은 중복 Analysis를 만들지 않는다.
- Output Validator 실패는 완료 결과로 노출되지 않는다.
- 기존 Journal 상세를 재조회할 수 있다.
- AI Provider 세부가 Finance Domain에 노출되지 않는다.

## 15. Regression Criteria

안전한 질문도 validator를 통과하고, 위험 질문은 lens가 금지를 우회하지 않으며,
rewrite 결과도 재검사한다. ownership·중복·실패·resource 없음·Review의 과거 평가
금지를 regression suite에 포함한다.

## 16. Release Non-Claim

이 plan의 batch 또는 acceptance criteria 통과는 제품 Runtime, 실제 Identity·
Commerce·AI Runtime 연동, 공개/유료 release, legal approval 완료를 뜻하지 않는다.

## 17. Related Documents

- [Finance MVP Implementation Scope](finance-mvp-implementation-scope.md)
- [Finance Core Loop and Domain Contract](finance-core-loop-and-domain-contract.md)
- [Finance Backend Architecture](finance-backend-architecture.md)
- [Finance AI Usage and Quota Policy](../service-policy/finance-ai-usage-and-quota-policy.md)
- [Finance Trial and Entitlement Policy](../service-policy/finance-trial-and-entitlement-policy.md)
