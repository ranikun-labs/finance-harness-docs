# Finance AI Usage and Quota Policy

## 0. Metadata

```text
policy_id: finance-ai-usage-and-quota-policy
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
  - ./finance-trial-and-entitlement-policy.md
  - ../00-core/0-a-safety-core/00-service-policy.md
  - ../00-core/0-a-safety-core/finance-policyguard-rules.md
  - ../05-product-legal-ops/finance-user-data-policy.md
```

이 문서는 Product Policy 초안이다. AI Pipeline, Provider, 모델, API, Database
Schema, Quota 저장 구조 또는 Runtime 구현을 확정하지 않는다. Metadata의 상태는
문서가 존재해도 Architecture, Implementation, Runtime 또는 Release가 완료되지
않았음을 의미한다.

## 1. 목적과 범위

이 문서는 사용자에게 보이는 AI Usage의 의미, Starter·Pro Trial·Paid Plan의
Usage 적용 원칙, Quota 소진 후 동작, 실패·재시도·중복 요청 처리 및 운영 보정
경계를 정의한다.

Entitlement의 상태 전이와 만료 후 데이터 접근은
[`finance-trial-and-entitlement-policy.md`](./finance-trial-and-entitlement-policy.md)가
소유한다. 정확한 기간·횟수·한도·가중치는
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)가
소유한다.

## 2. 사용자 Usage 단위

### 2.1 기본 원칙

```text
- 사용자에게는 하나의 분석 주제를 하나의 분석 사용으로 표시한다.
- 사용자 Usage는 그 주제에 대한 완료된 분석 결과가 제공될 때 1건 성립한다.
- 내부 모델 호출 횟수, 단계 수 또는 재시도 횟수는 사용자 Usage가 아니다.
- 질문 구조화 호출과 최종 분석 호출을 각각 사용자에게 차감하지 않는다.
- 작은 모델 1회 + 상위 모델 1회 = 사용자 Usage 2회라는 해석은 금지한다.
```

내부 Pipeline은 질문 구조화, 안전 검증, 최종 분석 또는 오류 복구를 연속 수행할
수 있다. 그 비용·관측은 운영 기준으로 다루되, 사용자에게는 하나의 분석 주제와
완료 결과라는 제품 단위로만 표현한다.

### 2.2 완료 결과와 제한된 정상 응답

안전·법률 정책상 답변 범위를 제한했더라도, 사용자에게 허용 범위 안의 완결된
대체 결과가 정상 제공된 경우에는 완료 결과가 될 수 있다. 반대로 PolicyGuard
또는 Output Validator가 결과 제공을 막아 사용 가능한 완료 결과가 없으면 Usage는
성립하지 않는다.

안전 Rewrite 결과, Follow-up 및 사용자가 부분 결과를 수락한 경우의 정확한
차감 경계는 §8 Open Decision으로 남긴다.

## 3. 차감·복구 원칙

| 상황 | 사용자 Usage 처리 |
|---|---|
| 완료된 최종 분석 결과 제공 | 1건 차감 |
| 시스템 오류 | 비차감 또는 자동 복구 |
| 모델 Provider 오류 | 비차감 또는 자동 복구 |
| Timeout 후 완료 결과 미제공 | 비차감 |
| Output Validator 차단 후 사용 가능한 완료 결과 미제공 | 비차감 |
| PolicyGuard 차단으로 분석 결과 미생성 | 비차감 |
| 동일 요청 재전송 | 중복 차감 금지 |
| idempotent retry | 새 Usage로 계산하지 않음 |
| 결과 제공 전 사용자 취소 | 원칙적으로 비차감 |
| 부분 결과만 생성되고 완료 결과 미전달 | 비차감 |

사용자에게 제공하지 못한 내부 작업의 비용은 사용자에게 숨겨진 Usage 또는 초과
과금으로 전가하지 않는다. 구현상의 idempotency key, retry queue, 원자적 차감
방식은 Architecture와 Implementation 범위다.

## 4. Plan별 Usage 구조

| Plan 또는 상태 | Usage·기능 원칙 |
|---|---|
| Starter | 제품 가치를 판단할 수 있는 기본 심층 분석, 제한된 일간 Usage와 Lens, 반복·후속 분석 제한 |
| Pro Trial | 일반 Paid Plan에 가까운 Usage와 기능, 고급 Lens 및 반복·후속 분석, 최상위 기능의 제한 체험 |
| Paid | Plan별 Usage, Lens, 분석 깊이·범위, 반복·후속 분석 및 편의 기능 |
| NO_AI_GENERATION_ENTITLEMENT | 신규 AI 분석 생성은 차단하고 기존 데이터 접근은 유지 |

Plan 차이는 투자 판단의 정확성 또는 안전성 차이가 아니다. 모든 Plan은 동일한
Safety·PolicyGuard·Output Validator·Evidence 기준을 적용한다.

Paid Plan의 가격, 상품명, 정확한 Usage 및 기능 한도는 이 문서에서 정의하지
않는다.

## 5. Quota 소진과 사용자 행동

Quota 또는 Plan의 AI 생성 Entitlement가 소진·만료되면 다음 원칙을 적용한다.

```text
- 남은 Usage와 소진 상태를 사용자에게 명확히 표시한다.
- 신규 AI 분석 생성과 새 고비용 AI 처리는 차단한다.
- 기존 Journal과 기존 분석 결과의 조회·삭제·지원되는 Export는 유지한다.
- 자동 추가 결제, 숨겨진 초과 과금 또는 자동 유료 전환은 금지한다.
- 다음 Reset 또는 사용자가 명시적으로 선택하는 Paid Plan 안내는 가능하다.
```

Entitlement 만료 후의 상태와 Paid 전환 우선순위는
[`finance-trial-and-entitlement-policy.md`](./finance-trial-and-entitlement-policy.md)를
따른다.

## 6. 운영 보정과 이의제기

장애·중복 차감 또는 정책상 비차감 상황에서 잘못 차감된 Usage는 복구할 수 있다.
운영자 보정은 사용자의 차감 이의제기 또는 확인 가능한 서비스 장애에 한정하며,
보정 사유·대상·시점은 감사 가능한 기록으로 남긴다.

사용자는 차감 결과에 이의를 제기할 수 있어야 한다. 지원 채널, 승인 권한,
감사 저장 방식 및 운영 도구는 구현 범위이며 이 문서에서 설계하지 않는다.

## 7. 관측과 Validation Gate

정책의 변경 가능한 값은 다음 관측을 함께 검토한 뒤
`finance-launch-experiment-values.md`에서 조정한다.

```text
- 분석 주제당 실제 AI 원가와 내부 모델 호출 수
- 성공·실패·재시도율
- Starter 및 Trial 사용자당 원가
- Quota 소진율과 일간 한도 도달률
- Starter → Pro Trial 및 Pro Trial → Paid 전환
- 최상위 기능 체험 원가
- 다중 계정·반복 사용, quota race, idempotency 우회 징후
- 장애 보정 및 차감 이의제기 발생률
```

Cost Validation은 실패·재시도·Rewrite 비차감으로 발생하는 비용까지 포함한다.
Abuse Validation은 분석 주제 경계 쪼개기, 자동화된 소진, 동시 요청 및 운영자
보정 악용을 포함한다. 관측 지표의 수집 방식이나 저장 구조는 정의하지 않는다.

## 8. Open Decision

다음은 별도 Product Decision 또는 후속 정책에서 정한다.

```text
- Follow-up 질문을 기존 분석에 포함하는 기준
- 질문을 수정해 재분석할 때 새 Usage로 보는 기준
- 안전 Rewrite 결과의 정확한 차감 기준
- 부분 결과를 사용자가 수락한 경우의 처리
- Paid Plan별 정확한 Usage와 분석 유형별 가중치
- 최상위 기능의 정확한 목록
```

## 9. 소유하지 않는 내용

```text
- Trial·Starter·Paid 상태 전이와 재가입·Account Merge 정책
  → finance-trial-and-entitlement-policy.md
- 기간·횟수·한도·가중치·Reset 시각
  → finance-launch-experiment-values.md
- PolicyGuard·Output Validator 상세 규칙
  → 00-core
- 데이터 보존·삭제·Export 형식 및 보안
  → 05-product-legal-ops 및 후속 구현·법무 검토
- API, Database Schema, Quota 저장 구조, AI Provider·모델, Runtime 구현
```
