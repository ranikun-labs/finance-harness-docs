# Finance Core Loop and Domain Contract

## 0. Metadata

```text
status           : implementation-readiness v1
document type    : domain and FE-BE contract boundary
authority        : subordinate to canonical Architecture, PolicyGuard and Runtime Contract
implementation   : not started
```

## 1. Document Purpose

V1 Core Loop의 resource 관계, 상태·오류·소유권 경계와 FE가 실제 연결 전에
필요한 계약을 정의한다. 전송 형식이나 persistence 구현을 확정하지 않는다.

## 2. Contract Ownership

Finance Backend Architecture는 Domain과 port/adapter 경계를 소유한다.
PolicyGuard, Lens Routing, Output Validator는 안전 의미를 소유한다. FE는 이
문서의 read/write 의미를 소비할 뿐 mock type을 Domain Contract로 승격하지 않는다.

## 3. Core Loop

```text
Question → ChecklistResult → AnalysisRequest → AnalysisResult
                                      ↓ completed result only
                              explicit Journal save → Journal detail → Review
```

분석 전 ChecklistResult를 확인한다. 분석 완료 자체가 Journal 생성은 아니며,
사용자의 명시적 저장만 Journal을 만든다.

## 4. Current User and Ownership Boundary

Backend는 CurrentUserPort에서 opaque `identity_user_id`를 얻는다. FE는 user ID를
전송하거나 선택하지 않는다. 모든 resource의 조회·연결·변경은 현재 사용자와
owner identity_user_id 일치를 확인하며, entitlement는 이를 대체하지 않는다.

## 5. Question Contract

Question은 사용자가 제출한 분석 주제와 필요한 입력을 나타낸다. 소유자와 생성
시점은 보존하되, Question 원문의 영속 보존 여부는 User Data Policy의 미결
결정을 따른다. Question은 AnalysisResult나 Journal과 동일 resource가 아니다.

## 6. ChecklistResult Contract

ChecklistResult는 특정 Question에 대해 분석 전 표시된 확인 항목과 사용자의
확인 결과를 남기는 불변 기록이다. 모든 항목을 체크해야 하는 판정 gate는 아니나,
해당 ChecklistResult의 확인 없이 AnalysisRequest를 실행하지 않는다.

## 7. AnalysisRequest Contract

AnalysisRequest는 Question 및 ChecklistResult에 연결된 실행 요청이다. product
analysis ID는 idempotency와 제품 수준 correlation의 기준이며, provider 요청 ID나
모델 세부를 Domain에 노출하지 않는다.

## 8. AnalysisResult Contract

AnalysisResult는 완료된 분석의 별도 resource다. 완료 결과는 불변이며 Journal은
이를 참조한다. PolicyGuard 또는 Output Validator가 사용 가능한 결과를 막으면
사용자에게 COMPLETED 결과로 노출하지 않는다.

## 9. Journal Contract

Journal은 사용자 작성 정보와 완료된 AnalysisResult 참조를 보유한다. 저장은 분석
완료 후 명시적으로 요청할 때만 가능하다. V1은 `DRAFT`/`SAVED`/`DELETED` runtime
enum을 자동 채택하지 않으며, 삭제 요청·삭제 상태는 User Data Policy 경계다.

## 10. Review Contract

Review는 Journal을 참조하는 두 번째 Slice resource다. 사용자 작성 기록의 사실,
근거, 누락 항목, 다음 기록 질문만 다룬다. 과거 판단 평가·향후 행동 지시·계좌,
수익률, 시장 결과 자동 연결은 계약 밖이다.

## 11. Resource Relationship

```text
Question
  → ChecklistResult
  → AnalysisRequest
  → AnalysisResult (immutable when completed)

Journal → user-authored information + completed AnalysisResult reference
Review  → Journal reference
```

## 12. State Semantics

| 상태 | 의미 |
|---|---|
| REQUESTED | 유효한 요청을 받아 아직 실행을 시작하지 않음 |
| RUNNING | 안전 처리 이후 분석 실행이 진행 중 |
| COMPLETED | Output Validator를 통과한 사용자 노출 가능 결과가 있음 |
| FAILED | 실행 또는 검증 실패로 완료 결과를 제공하지 못함 |
| BLOCKED | PolicyGuard 또는 entitlement 경계에서 실행이 차단됨 |

`CANCELLED`는 실제 V1 필요성이 확인될 때까지 Deferred다.

## 13. Error Semantics

최소 오류 범주는 current user 확인 실패, 데이터 소유권 불일치, entitlement 거절,
validation 실패, policy 차단, 분석 실행 실패, output validation 실패, 중복 요청,
resource 없음이다. 내부 provider·retry 세부는 사용자 계약으로 노출하지 않는다.

## 14. Idempotency Boundary

동일 사용자의 동일 분석 의도에 대한 동일 idempotency 요청은 새 AnalysisRequest나
중복 실행을 만들지 않는다. 명시 Journal 저장도 재전송에 안전해야 하며, key의
표현과 저장 방식은 후속 구현 설계다.

## 15. Entitlement Boundary

분석 생성 전 EntitlementPort를 확인한다. V1은 AllowAllEntitlementAdapter를
사용하므로 Starter·Trial·Paid 상태나 quota를 구현하지 않는다. 허용은 Safety
계약을 우회하지 않는다.

## 16. Usage and Quota Non-Implementation Boundary

정책상 완료 결과만 사용자 Usage가 될 수 있고 실패·차단·중복은 차감 대상이
아니다. 그러나 V1은 usage ledger, quota 차감, plan 상태 머신을 구현하지 않는다.

## 17. PolicyGuard and Output Validator

PolicyGuard는 Lens보다 먼저 실행한다. 안전한 경로만 routing하고, 모든 생성 응답은
Output Validator를 통과한 뒤에만 노출한다. 재작성도 재검사하며 실패 시 안전한
fallback으로 종결한다.

## 18. AI Execution and Cost Telemetry Boundary

Finance는 product analysis ID, 사용자 기능, 완료·실패 결과와 제품 수준
correlation을 소유한다. AI Runtime 또는 V1 AI Adapter는 provider/model execution,
token, retry, latency, provider cost 관련 execution summary를 다룬다. Finance
Domain이 provider 가격 계산 로직을 직접 소유하지 않는다.

## 19. FE-BE Contract Map

| FE surface | 필요한 계약 |
|---|---|
| Onboarding | 정적 UI는 계약 없이 가능 |
| Ask | Question, ChecklistResult, Analysis 상태 |
| Journal Detail | Journal read model과 AnalysisResult 관계 |
| Journal Review | Review Contract |
| Journal Create | 명시 저장 Contract와 idempotency |
| Home | 최근 Journal·Analysis summary |
| Starter·Trial·Usage | 별도 Runtime 승인 전 구현 보류 |

## 20. FE Contract Availability Timing

FE STEP 9 순서는 변경하지 않는다. Ask, Journal Detail, Journal Review, Journal
Create, Home은 위 계약이 준비된 뒤 실제 연결할 수 있다. Journal mock은 API
contract가 아니며 FE repository의 구현 순서를 지시하지 않는다.

## 21. Deferred Decisions

HTTP endpoint/method, DTO, Java package, database schema, retention 기간,
deletion 표현, cancellation, actual Identity/Commerce, quota ledger와 provider
가격 계산은 미확정이다.

## 22. Related Documents

- [Finance Backend Architecture](finance-backend-architecture.md)
- [Finance MVP Implementation Scope](finance-mvp-implementation-scope.md)
- [Finance Routing Contract](../00-core/0-c-runtime-contract/finance-routing-contract.md)
- [Finance PolicyGuard Rules](../00-core/0-a-safety-core/finance-policyguard-rules.md)
- [Finance Output Validator](../00-core/0-a-safety-core/finance-output-validator.md)
