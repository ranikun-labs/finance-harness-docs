# Finance Growth and Monetization Validation Plan

## 0. Metadata

```text
status                : canonical internal operational baseline v1.0
document type         : Growth and Monetization Validation Plan
document_id           : finance-growth-and-monetization-validation-plan
authority             : canonical internal operational baseline
lifecycle_status      : active
implementation_status : internal-control-baseline
runtime_loadable      : false
routable              : false
exposure_class        : internal-controlled
distribution_boundary : repo-only — Runtime package에 포함하지 않음
external_review       : not externally reviewed
legal_advice          : false
legal_approval        : false
public_launch_gate    : unresolved
paid_launch_gate      : unresolved
decision owner        : Finance Harness Product Owner
parent references     : finance-product-policy.md; finance-launch-experiment-values.md; finance-ai-usage-and-quota-policy.md; finance-trial-and-entitlement-policy.md
non-goals             : Product Policy 변경, 영구 Free Lite 채택, 가격·결제·환불 확정, Runtime·Implementation 설계
```

## 1. Document Purpose and Status

이 문서는 Free, Trial, 분석 등급, Pricing, Discount, Export 및 Reactivation 후보를 Product Policy 변경 전에 검증하기 위한 영구 Validation Plan이다. 실제 Experiment 실행 완료, Runtime 지원, Legal Review 완료 또는 Release를 의미하지 않는다. 실행에는 별도의 Founder Experiment Exception 승인과 구현·Legal·Cost·Abuse Gate 충족이 필요하다.

## 2. Current Canonical Baseline

이 문서는 다음 canonical baseline을 변경하지 않는다.

- Finance Harness는 premium-oriented service다.
- 영구 Free Plan은 없고 신규 가입자는 기간제 Starter Entitlement를 받는다.
- Starter에서도 안전하고 유의미한 기본 심층 분석을 제공한다.
- Pro Trial은 명시적으로 활성화하며 계정당 1회다.
- 자동 결제와 자동 유료 전환은 없다.
- Trial 중 Starter 기간은 계속 경과한다.
- Starter가 종료되면 신규 AI 분석 생성을 중단한다.
- 기존 Journal·분석 결과의 조회·삭제·지원되는 Export는 유지한다.
- 내부 모델 호출 수와 사용자 Usage를 분리한다.

## 3. Decision-State Classification

### Stable Product and Marketing Principles

- 투자 판단을 대신하지 않고 Safety·PolicyGuard·Evidence 기준을 Plan별로 차등하지 않는다.
- 내부 모델 호출 수는 사용자 Usage가 아니다.
- Trial은 명시적으로 활성화하며 자동 결제는 없다.
- Extended를 무제한 제공하지 않는다.
- 분석 등급과 Subscription Plan을 분리한다.
- Deep 사용 전 차감을 안내하고 Extended 사용 전 명시적 확인을 받는다.
- 상품형 Export와 데이터 권리를 분리하고 Healthy Retention을 우선한다.
- Fake Scarcity를 금지한다.

### Founder Product Decision Candidate

- 영구 Free Lite 재도입과 Trial 종료 후 Free Lite 복귀
- `General / Deep / Extended`의 안정적 사용자 등급 채택
- Paid Plan에서 Extended 제한 제공
- 상품형 Export의 Plan별 차등 원칙

### Launch Experiment Candidate

- Free 또는 Reactivation General 월 3회와 Calendar Month Reset
- Trial 활성화 기한 7일 또는 14일, 활성화 후 7일
- Deep 2회, Extended 1회, Plan별 Extended 횟수
- 가격, 할인율, 할인 대상 수, 할인 기간

### Currently Unadopted

- 영구 Free Lite의 즉시 Product Policy 도입
- 12개월 50% 할인
- Free / Standard / Pro / Expert 4개 Plan 즉시 도입
- 자동 Deep 차감
- Extended를 더 정확한 판단이나 전문가 자문으로 광고
- Export 제한 기반 Hostile Lock-in
- 최고 등급이면 모든 요청에 최고가 모델을 사용하는 방식
- Fake Scarcity

## 4. Validation Principles

- 실험은 canonical Product Policy와 명시적으로 분리한다.
- 수치·Plan·할인·가격은 검증 전 영구 정책으로 고정하지 않는다.
- 사용자 가치, 증분 Paid Gross Margin, 원가, Abuse, Trust를 함께 평가한다.
- 초기에는 한 번에 하나의 주요 가설을 검증하고 Full Factorial을 피한다.
- 대상·기간·계정 수·총 AI 비용에는 사전 Hard Cap을 둔다.

## 5. Reactivation Credit Hypothesis

이 가설은 영구 Free Lite가 아닌 제한된 **Reactivation Experiment Exception**이다.

| Arm | 대상 후 신규 AI 분석 |
| --- | --- |
| A0 Control | 없음 |
| A1 Reactivation | 자격 있는 만료·미결제 사용자에게 Calendar Month 기준 General Credit 3회. 미사용분 이월 없음, Deep·Extended 없음 |

- A1 실행 전 Founder의 Experiment Exception 승인이 필요하다.
- 현재 Product Policy 수정이나 영구 Free Lite 채택이 아니다.
- 대상·기간·계정 수·총비용을 제한한다.
- 사용자에게 Free Lite로 마케팅하지 않는다.
- 반복 결과는 향후 영구 Free Lite Product Decision의 근거가 될 수 있다.

## 6. Trial Activation Window Hypothesis

| Arm | Ticket Window |
| --- | --- |
| B1 | 첫 General 완료 후 7일 내 활성화 |
| B2 | 첫 General 완료 후 14일 내 활성화 |

공통 원칙은 명시적 활성화, 활성화 후 7일 유효, 자동 결제 없음, 계정당 1회 및 가입 즉시 자동 Trial 시작 금지다. 권장 Primary Trigger는 `general_analysis_completed`이며, Journal 생성과 Checklist 완료는 보조 Activation Signal이다.

## 7. Deep and Extended Trial Mix Hypothesis

| Arm | Trial Mix |
| --- | --- |
| C1 | Deep 분석 2회 |
| C2 | Deep 분석 2회 + Extended 분석 1회 |

Extended가 증분 Paid 전환을 만드는지, 사용자가 Deep과 차이를 설명할 수 있는지, 추가 원가를 정당화하는지, 전문가 자문으로 오인되지 않는지를 검증한다. B와 C는 초기부터 Full Factorial로 동시에 실행하지 않고, Window 확인 뒤 Mix를 순차 검증한다.

## 8. Subscription Packaging Hypothesis

- 초기 Subscription Plan은 최대 3개를 후보로 둔다.
- Standard 또는 Basic Plan 필요성은 미검증이다.
- Pro 고객에게 제한된 Extended를 제공할 수 있는지 검증한다.
- 미채택 Subscription Plan 후보인 Expert는 Extended 횟수만이 아니라 누적
  Review·Report 등 검증된 추가 가치가 있어야 한다.

Free / Standard / Pro / Expert 4개 Plan과 가격·정확한 Usage는 현재 확정하지 않는다.

## 9. AI Unit Economics

목표는 할인 후 실제 인식 매출 대비 AI 직접 원가 P90 20% 이하이며, P95 또는 Plan별 최대 30%는 Hard Cap 후보다.

```text
C_general  : General 분석 1건의 총 직접 원가
C_deep     : Deep 분석 1건의 총 직접 원가
C_extended : Extended 분석 1건의 총 직접 원가

Reactivation 사용자 최대 원가
= General 완료 횟수 × C_general

Trial 최대 원가
= Deep 완료 횟수 × C_deep
+ Extended 완료 횟수 × C_extended

유료 Plan 허용 AI 원가
= 할인 후 실제 인식 매출 × 0.20
```

총 직접 원가는 입력·출력·Reasoning Token, Web Search, RAG Context, Validator Rewrite, Provider Retry, 중복·취소 요청, Abuse 및 Extended Tail Cost를 포함한다. 절대 원가는 실측 전 창작하지 않는다.

## 10. Early-Open Offer and Discount

| 후보 | 판단 | 이유 |
| --- | --- | --- |
| 3개월 30% | VALIDATE FIRST | 초기 현금·Retention 효과와 가격 신뢰를 실측해야 한다. |
| 6개월 40% | ADJUST | 원가 Tail Risk와 장기 Support 부담을 낮추는 조건 조정이 필요하다. |
| 12개월 50% | REJECT | 장기 AI 원가와 가격 Anchor 위험이 현재 단계에서 과도하다. |

대안 후보는 초기 N명 Founding Price, 짧은 선결제 기간의 제한적 할인, 실제 제공 가능한 Review·Extended Benefit이다. 할인 종료일 또는 대상 수는 실제 Enforce하고 허수 정가 Anchor와 Fake Scarcity를 사용하지 않는다. 갱신·환불·중도 변경은 Legal Review 전 확정하지 않는다.

## 11. Export Validation

- Journal CSV는 데이터 이동성과 신뢰를 검증할 후보다.
- 분석 Markdown은 유료 상품 기능 후보다.
- 개별 분석 PDF는 V1 이후 검증 후보다.
- 기간별 Report는 상위 Plan 후보다.
- 묶음 Export는 비용·보안·권리 검토 이후 후보다.

현재 Plan별 Export 범위는 확정하지 않는다. 상품형 Export와 법적으로 필요한 데이터 제공 절차는 별개다.

## 12. Experiment Sequence

1. baseline 계측과 원가 측정을 먼저 완료한다.
2. B1/B2 Trial Window를 순차 검증한다.
3. 선택된 Window에서 C1/C2 Trial Mix를 검증한다.
4. Founder가 승인한 경우에만 A0/A1 Reactivation Exception을 제한적으로 검증한다.
5. Packaging·가격·할인은 가치와 원가 경계가 확인된 뒤 검증한다.

각 단계는 이전 단계의 Trust·Abuse·Cost Guardrail을 통과해야 다음 단계로 넘어간다.

## 13. Cohort and Sample Guidance

대상 Cohort는 상태·유입 시점·사용 이력·Paid 전환 이력으로 사전 정의한다. Reactivation은 Starter와 Trial이 모두 종료되고 Paid 전환하지 않은 사용자로 제한한다.

초기 표본이 작으면 고정 기간 A/B 승패 선언보다 Cohort 비교, 사용자 인터뷰, Sequential Test를 우선한다. 표본 수·최소 관측 기간·분배 방식·중단 기준은 실행 전 원가 상한과 함께 승인한다.

## 14. Analytics Events

```text
credit_exposed
credit_used
free_user_returned
reactivation_to_paid

trial_ticket_viewed
trial_activated
trial_activation_delay

general_analysis_completed
deep_analysis_completed
extended_analysis_recommended
extended_analysis_confirmed
extended_analysis_completed

quota_warning_viewed
quota_exhausted
paywall_viewed
plan_selected
checkout_started
subscription_started
subscription_expired

journal_reviewed
export_viewed
export_attempted
export_completed
```

Analytics Event에는 질문 원문, Journal 원문, 종목명, 가격·수량, 분석 결과 원문 또는 민감 금융정보를 넣지 않는다.

## 15. KPI

| 영역 | KPI |
| --- | --- |
| Activation | 첫 가치 도달률, 첫 General 완료율, 첫 Journal 생성률, Time to Value |
| Trial | Ticket 노출률, 활성화율, Activation Delay, Deep 완료율, Extended 확인·완료율 |
| Conversion | Trial → Paid, Reactivation → Paid, Paywall → Checkout, Checkout → Subscription |
| Retention | D7, D30, D90, Journal Review율, 만료 사용자 재방문율 |
| Economics | C_general, C_deep, C_extended, 사용자별 P50·P90·P95 원가, 할인 후 매출 대비 AI 원가율 |
| Trust | Trial 압박 불만, Countdown 부정 반응, 분석 등급 오인, 전문가 자문 오인, Support Contact Rate |

## 16. Cost Guardrails

- 실험 대상 계정 수와 전체 AI 비용에 Hard Cap을 둔다.
- 사용자별 General·Deep·Extended 비용 상한을 둔다.
- P90 목표 20%와 P95 또는 Plan별 최대 30% 후보를 매 Cohort에서 확인한다.
- Retry·Validator Rewrite·취소·중복 요청·Extended Tail Cost를 별도 관측한다.
- Cost Guardrail을 넘으면 신규 노출을 중지하고 원인 검토 전 확대하지 않는다.

## 17. Abuse and Trust Guardrails

- 중복 요청과 Idempotent Retry의 중복 차감을 방지한다.
- 다중 계정, 탈퇴·재가입, Account Linking·Merge 전후 Credit·Trial 중복을 관측한다.
- Trial 재발급을 허용하지 않는다.
- Extended는 명시적 확인 후에만 시작한다.
- 자동 결제와 허위 희소성을 사용하지 않는다.
- 전화번호·SMS 인증은 기본 대응책으로 자동 채택하지 않으며, 도입 전 NOTE-01과 별도 검토가 필요하다.

## 18. Stop Conditions

다음 중 하나가 발생하면 해당 Arm의 신규 노출을 중지하고 검토한다.

- P95 원가 또는 사전 승인된 총비용 Hard Cap 초과
- Control 대비 Paid Gross Margin이 악화되고 회복 근거가 없음
- Trial 압박·전문가 자문 오인·데이터 접근 불만이 Guardrail을 초과
- 다중 계정·Credit·Trial 반복 수령 또는 Idempotency 우회가 확인됨
- Legal 또는 Privacy Gate가 충족되지 않음

## 19. Adoption Criteria

- Control 대비 의미 있는 증분 Paid Gross Margin 또는 검증된 장기 Retention 가치
- 사용자가 등급과 Credit·Trial 조건을 이해하고 Trust 지표가 허용 범위 내에 있음
- P50·P90·P95 원가와 사용자별 Hard Cap이 목표 범위에 있음
- Abuse 방어와 운영 보정 Audit이 가능한 범위임
- Legal Review와 Founder의 명시적 의사결정이 있음

## 20. Product Policy Change Conditions

영구 Free Lite Product Policy 변경은 다음이 충족되기 전에는 하지 않는다.

1. 최소 2개 Cohort의 일관된 결과
2. Control 대비 의미 있는 증분 Paid Gross Margin
3. P95 원가 Hard Cap 충족
4. Complaint·오인·Abuse Guardrail 통과
5. Legal Review 통과
6. Founder의 명시적 Product Decision
7. Product Policy와 Experiment Value를 분리한 변경안 승인

## 21. Legal, Cost and Abuse Gates

| Gate | 검증 항목 |
| --- | --- |
| Legal | Paid 서비스 약관, Starter·Trial 만료 고지, 자동 결제 없음 고지, 할인·갱신·환불 경계, 데이터 열람·삭제·Export, 분석 결과·Usage Event 보존 |
| Cost | 분석 등급별 실제 원가, Free·Trial 최대 원가, Retry·Rewrite 비차감 비용, 할인 후 매출 대비 원가율, Extended Tail Cost |
| Abuse | 다중 계정, 탈퇴·재가입, Account Linking·Merge, Quota Race, Idempotency 우회, 분석 주제 경계 쪼개기, 운영자 보정 악용 |

전화번호 수집 또는 SMS 검증은 NOTE-01 충족 전 도입하지 않는다.

## 22. Open Decisions

- 영구 Free Lite 채택 여부와 Trial 후 상태 전이
- 안정적인 분석 등급 Naming과 Plan별 정확한 Usage
- Standard 또는 Basic Plan 필요성
- Paid 전환 중 남은 Trial 처리와 Paid 종료 후 상태 전이
- Extended의 정확한 기능 범위와 Plan별 횟수
- 가격, 할인 대상·기간·갱신·환불·중도 변경
- Export의 정확한 형식·필드·보안 방식과 법적 범위

## 23. Related Documents

- [Finance Product Policy](../service-policy/finance-product-policy.md)
- [Finance Launch Experiment Values](../service-policy/finance-launch-experiment-values.md)
- [AI Usage and Quota Policy](../service-policy/finance-ai-usage-and-quota-policy.md)
- [Trial and Entitlement Policy](../service-policy/finance-trial-and-entitlement-policy.md)
- [Product Marketing Context](finance-product-marketing-context.md)
- [Finance User Data Policy](finance-user-data-policy.md)
- [Finance Disclaimer and Copy Guide](finance-disclaimer-and-copy-guide.md)
