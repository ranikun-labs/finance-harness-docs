# Finance Launch Experiment Values

## 0. Metadata

```text
policy_id: finance-launch-experiment-values
document_type: product-service-policy
product_policy_status: draft
legal_review_status: required
architecture_status: not_started
implementation_status: not_started
runtime_support: not_supported
release_status: not_released
launch_values_status: experiment_candidate
owner: Product Owner (prototype stage: Project Owner)
canonical_refs:
  - ./finance-product-policy.md
```

이 문서가 존재한다는 사실은 Architecture 설계, API 설계, Database Schema,
Migration, 또는 Implementation 완료를 의미하지 않는다.

## 1. 문서 목적과 성격

이 문서는 변경 가능한 Launch Experiment 수치의 단일 Source of Truth다.

```text
이 문서의 값은 Product Policy의 불변 원칙이 아니다.
운영 비용, 사용성, 악용률, Trial 활성화율 및 유료 전환율에 따라 변경할 수 있다.
이 문서의 값 변경은 원칙적으로 Master Product Decision 재개를 요구하지 않는다.
```

불변 원칙(영구 무료 Plan 없음, 명시적 활성화, 자동 결제 없음, Trial 중
Starter 기간 경과, 남은 Starter로의 조건부 복귀 등)은
[`finance-product-policy.md`](./finance-product-policy.md)가 소유한다. 이
문서는 그 원칙 위에서 바뀔 수 있는 숫자만 소유한다.

각 항목은 다음 필드로 기록한다.

```text
Current candidate
Decision status
Evidence required
Measurement
Adjustment trigger
Owner
Last reviewed
```

`Current candidate`에 적힌 값은 Accepted Product Policy가 아니라 논의된
후보값이다.

## 2. Welcome Promotion 신청 가능 기간

```text
Current candidate: 가입 후 30일 (예시 후보, 미확정)
Decision status: experiment candidate
Evidence required: Promotion 신청 전환율, 신청 지연 분포
Measurement: 가입일 대비 Promotion 신청일 분포
Adjustment trigger: 신청 전환율이 목표치 대비 현저히 낮거나 높을 때
Owner: Product Owner
Last reviewed: 미실측 (문서 생성 시점 기준 값 없음)
```

## 3. Starter 기간

```text
Current candidate: 가입 후 30일
Decision status: experiment candidate
Evidence required: Starter 활성 사용자 비율, 사용자당 AI 원가, Starter → Trial 전환율
Measurement: 가입일 대비 Starter 사용일수, 일간 Usage 소진율, 전환 시점 분포
Adjustment trigger: 원가·악용률 또는 전환율이 목표와 현저히 다를 때
Owner: Product Owner
Last reviewed: 미실측
```

이 값은 §2 Welcome Promotion 신청 가능 기간과 별개의 Starter Entitlement
후보다. 두 기간을 같은 정책으로 합치지 않는다.

## 4. Starter 일간 기본 Usage

```text
Current candidate: 하루 1개 분석 주제
Decision status: experiment candidate
Evidence required: Starter 사용자당 원가, 일간 한도 도달률, 분석 주제당 완료율
Measurement: 사용자별 일간 완료 분석 주제 수와 실제 AI 원가
Adjustment trigger: 원가·사용성·악용률이 목표와 현저히 다를 때
Owner: Product Owner
Last reviewed: 미실측
```

## 5. Pro Trial 기간

```text
Current candidate: 7 days
Decision status: experiment candidate
Evidence required: Trial 활성화율, Trial 중 유료 전환율
Measurement: Trial 시작 대비 활성 사용일수, 전환 시점 분포
Adjustment trigger: 전환율이 목표치 대비 현저히 낮거나, Trial 기간 중
  미사용 이탈이 높을 때
Owner: Product Owner
Last reviewed: 미실측 (문서 생성 시점 기준 값 없음)
```

## 6. Pro Trial 활성화 기한

```text
Current candidate: 가입 후 14일 이내
Decision status: experiment candidate
Evidence required: 활성화 지연 분포, Trial 활성화율, Starter → Trial 전환율
Measurement: 가입일 대비 Trial 활성화 시점과 활성화 포기율
Adjustment trigger: 기한이 활성화 또는 전환을 과도하게 막거나 악용을 늘릴 때
Owner: Product Owner
Last reviewed: 미실측
```

## 7. 전체 Pro Trial Usage Unit

```text
Current candidate: 미정
Decision status: experiment candidate
Evidence required: 사용자당 AI 원가, Trial 기간 내 평균 사용량
Measurement: 사용자별 누적 Usage Unit 소비
Adjustment trigger: 원가 대비 전환율이 목표치를 충족하지 못할 때
Owner: Product Owner
Last reviewed: 미실측
```

## 8. 일간 Usage Unit

```text
Current candidate: 미정
Decision status: experiment candidate
Evidence required: 일간 한도 도달률, 악용 패턴 데이터
Measurement: 사용자별 일간 Usage Unit 소비 분포
Adjustment trigger: 일간 한도 도달률이 과도하게 높거나 낮을 때
Owner: Product Owner
Last reviewed: 미실측
```

## 9. 분석 유형별 Usage 가중치

```text
Current candidate: 미정 (기능별 원가 차이만 정성적으로 확인된 상태)
Decision status: experiment candidate
Evidence required: 기능별 실제 AI 원가, 고비용 기능 사용 빈도
Measurement: 기능별 Usage Unit 소비량과 실제 원가의 상관관계
Adjustment trigger: 특정 기능의 원가가 가중치 대비 과소·과대 반영될 때
Owner: Product Owner
Last reviewed: 미실측
```

## 10. 최상위·고비용 기능 제공 횟수

```text
Current candidate: Pro Trial 전체 기간 중 총 1~2회
Decision status: experiment candidate
Evidence required: 고비용 기능 사용률, Trial 대비 유료 전환 기여도
Measurement: Trial 사용자당 고비용 기능 호출 횟수
Adjustment trigger: 고비용 기능이 Trial 원가의 과도한 비중을 차지할 때
Owner: Product Owner
Last reviewed: 미실측
```

## 11. Rate Limit 후보

```text
Current candidate: 미정
Decision status: experiment candidate
Evidence required: 단시간 반복 요청 패턴, 악용·자동화 시도 로그
Measurement: 사용자별 단위 시간당 요청 수 분포
Adjustment trigger: 자동화·악용 의심 트래픽이 관측될 때
Owner: Product Owner
Last reviewed: 미실측
```

## 12. Usage Reset 운영값

```text
Current candidate: 미정
Decision status: experiment candidate
Evidence required: 일간 Usage 한도 도달 시점, 지역별 사용 분포, Support 문의
Measurement: Reset 시각 전후의 요청·소진·이의제기 분포
Adjustment trigger: 특정 Reset 기준이 사용성 또는 운영 부담을 과도하게 높일 때
Owner: Product Owner
Last reviewed: 미실측
```

## 13. Cost Guardrail 후보

```text
Current candidate: 미정
Decision status: experiment candidate
Evidence required: 분석 주제당 실제 AI 원가, Starter·Trial 사용자당 원가 상한
Measurement: Plan별 평균·상위 원가와 전환율의 상관관계
Adjustment trigger: 비용이 지속 가능한 상한을 초과하거나 기능 제한이 전환을 과도하게 막을 때
Owner: Product Owner
Last reviewed: 미실측
```

## 14. 실험 시작·종료 조건

```text
Current candidate: 미정
Decision status: experiment candidate
Evidence required: 최소 표본 수, 관측 기간 하한
Measurement: 실험군 표본 크기, 경과 일수
Adjustment trigger: 표본 부족으로 결론을 내릴 수 없을 때 실험 연장
Owner: Product Owner
Last reviewed: 미실측
```

## 15. 관측 지표

이 문서의 값을 조정할 때 최소한 다음 지표를 함께 검토한다.

```text
Trial 활성화율
Starter 활성 사용자 비율
전화번호 인증 이탈률
사용자당 AI 원가
분석 주제당 실제 AI 원가
내부 모델 호출 수
전체 Usage 소진율
일간 한도 도달률
고비용 기능 사용률
Starter → Trial 전환율
Trial 중 유료 전환율
중복 가입 차단률
Support·이의제기 발생률
```

각 지표의 실제 측정 방식, 집계 주기, 대시보드는 Architecture·Implementation
단계에서 별도로 정의한다. 이 문서는 어떤 지표를 관측해야 하는지만 소유하며,
측정 구현을 소유하지 않는다.
