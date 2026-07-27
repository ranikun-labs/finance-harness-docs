# Finance MVP Implementation Scope

## 0. Metadata

```text
status            : implementation-readiness v1
document type     : implementation scope translation
authority         : subordinate to finance-backend-architecture.md and canonical policy
implementation    : not started
runtime/release   : not approved by this document
```

## 1. Document Purpose

이 문서는 확정된 Product Policy와 Backend Architecture를 V1의 구현 경계와
순서로 번역한다. 새 Product Policy, API, DTO, Database Schema 또는 외부
Runtime 연동을 설계하지 않는다.

## 2. Canonical Dependencies

- [Finance Backend Architecture](finance-backend-architecture.md)가 Domain 소유권,
  port/adapter와 AI Runtime 경계를 소유한다.
- Product, AI Usage/Quota, Trial/Entitlement, Onboarding/Identity 정책이 제품
  원칙을 소유하며, V1 Runtime 구현 여부를 뜻하지 않는다.
- PolicyGuard, Routing Contract, Output Validator가 질문 안전 처리와 노출
  기준을 소유한다.
- User Data Policy가 Journal·질문·분석 결과의 최소화, 보존·삭제 경계를 소유한다.

## 3. V1 Runtime Profile

```text
Current user : CurrentUserPort + LocalDevCurrentUserAdapter
Entitlement  : EntitlementPort + AllowAllEntitlementAdapter
Identity     : Shared Identity 실제 연동 없음
Commerce     : 실제 연동 없음
Billing/Payment/Subscription/Quota : 구현 없음
```

Starter·Trial·Usage 정책이 존재하는 것과 V1 Runtime이 상태 머신을 구현했다는
것은 다르다. LocalDevCurrentUserAdapter는 production 인증이 아니며,
AllowAllEntitlementAdapter는 entitlement 확인 경계만 보존한다.

## 4. V1 Product Core Loop

```text
질문 입력
→ 분석 전 Checklist 확인
→ General 분석
→ 결과 확인
→ 사용자가 명시적으로 Journal 저장
→ Journal 상세 재조회
→ 이후 Review
```

Checklist는 모든 항목 체크를 강제하는 Pass/Fail Gate가 아니라, 분석 전
확인하는 판단 기준이다. 다만 ChecklistResult가 해당 Analysis에 연결되어
확인되기 전에는 Analysis를 실행하지 않는다.

## 5. V1 In Scope

- CurrentUserPort, LocalDevCurrentUserAdapter, identity_user_id 기반 소유권
- Question, ChecklistResult, AnalysisRequest, AnalysisResult
- General 분석, PolicyGuard, Lens Routing, Output Validator
- 분석 완료 후의 명시적 Journal 저장과 Journal 목록·상세 조회
- 기본 오류 경계, 중복 분석 요청 방지
- 제품 Analysis ID와 AI 실행 correlation, 최소 Execution·Cost Telemetry 경계
- 두 번째 Slice의 사용자 작성 복기 Review

## 6. V1 Out of Scope

- 실제 Login/OAuth, Shared Identity, Starter·Trial Runtime, 사용자 Quota·Usage 차감
- Commerce, Billing, Payment, Subscription
- Deep·Extended, 영구 Free Lite, Reactivation Experiment, Welcome Promotion
- Export, 계좌 연동, OCR, 자동 매매, 실제 수익률 자동 계산

## 7. First Vertical Slice

```text
LocalDevCurrentUser
→ Question 생성
→ ChecklistResult 생성·확인
→ AllowAll Entitlement 확인
→ PolicyGuard
→ General 분석
→ Output Validator
→ AnalysisResult 완료
→ 사용자 명시 Journal 저장
→ Journal 상세 재조회
```

분석 전 Journal Draft를 만들지 않는다. Journal은 완료된 AnalysisResult를
참조하며 결과 본문을 단순 문자열로 복제하지 않는다.

## 8. Second Vertical Slice

Review는 첫 Slice 완료 뒤에 구현한다. Review는 사용자가 작성한 Journal을
참조해 관찰·근거·누락 항목·다음 기록 질문을 다루며, 과거 판단 평가나
계좌·수익률·시장 결과 자동 연결을 포함하지 않는다.

## 9. Deferred Product and Runtime Scope

`CANCELLED` 분석 상태, 실제 entitlement/usage 상태 전이, 실제 Identity·Commerce
adapter, provider 가격 계산, 장기 보존 기간, Export의 형식·보안은 후속 승인
사항이다. 이 문서는 이를 구현 대상으로 승격하지 않는다.

## 10. FE Dependency Timing

FE Snapshot은 route·AppShell·i18n·모바일 기반과 Journal List UI가 있으며 나머지는
placeholder라는 입력이다. FE mock은 UI fixture일 뿐 Domain Contract가 아니다.
실제 연결은 각 화면의 계약이 준비된 뒤에만 수행한다.

## 11. Backend Start Conditions

- 상위 Architecture 및 안전 계약을 변경하지 않는다.
- CurrentUserPort를 통해서만 현재 사용자를 얻고 FE payload user ID를 신뢰하지 않는다.
- General 분석의 활성 lens/fixture와 AI adapter 경계가 별도 구현 작업에서 확인된다.
- 사용자 데이터 보존·삭제 및 LLM 전송 최소화 경계를 구현 전에 검토한다.

## 12. Completion Criteria

첫 Slice는 Question부터 Journal 상세 재조회까지 소유권·안전·idempotency 경계를
통과해야 한다. 두 번째 Slice는 Review가 기록 기반 질문에만 머물고 안전 계약을
통과해야 한다. 이는 Runtime·Release 완료 선언이 아니다.

## 13. Non-Goals

본 문서는 endpoint, HTTP method, Java class/package, DTO, database table,
migration, provider/model 또는 deployment topology를 확정하지 않는다.

## 14. Related Documents

- [Finance Backend Architecture](finance-backend-architecture.md)
- [Finance Core Loop and Domain Contract](finance-core-loop-and-domain-contract.md)
- [Finance MVP Build and Verification Plan](finance-mvp-build-and-verification-plan.md)
- [Finance Product Policy](../service-policy/finance-product-policy.md)
- [Finance User Data Policy](../05-product-legal-ops/finance-user-data-policy.md)
