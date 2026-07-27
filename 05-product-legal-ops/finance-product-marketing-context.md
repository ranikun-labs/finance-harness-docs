# Finance Product Marketing Context

## 0. Metadata

```text
status                : canonical internal operational baseline v1.0
document type         : Product Marketing Context
document_id           : finance-product-marketing-context
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
parent references     : finance-product-policy.md; finance-user-data-policy.md; finance-disclaimer-and-copy-guide.md
non-goals             : 가격·결제·환불 확정, Product Policy 변경, Runtime·Implementation 설계
```

## 1. Document Purpose and Status

이 문서는 Finance Harness의 제품 마케팅·포지셔닝·타깃·Retention 기준을 보존하는 canonical internal operational baseline이다. [Master Product Policy](../service-policy/finance-product-policy.md)를 대체하지 않으며, Founder Candidate 또는 수치 후보를 채택하지 않는다. Runtime·Implementation·Legal Review·Release 완료를 뜻하지 않는다.

## 2. Product Category

Finance Harness는 **AI 기반 금융 학습·판단 보조·투자 Journal·Review 서비스**다.

## 3. Target Customer

- 직접 투자하지만 판단 기준이 구조화되지 않은 사용자
- 여러 AI를 사용하지만 금융 답변을 신뢰하기 어려운 사용자
- 투자 전 Checklist와 투자 후 복기를 원하는 사용자
- 반복되는 자신의 투자 판단 오류를 발견하고 싶은 사용자

## 4. Core Job to Be Done

Finance Harness는 투자 결정을 대신하지 않는다. 사용자가 자신의 판단 과정을 구조적으로 검토하도록 다음을 지원한다.

- 질문 구조화
- 누락된 판단 기준 확인
- 다중 Lens 분석
- 투자 전 Checklist
- 투자 Journal
- 사후 Review
- 반복되는 판단 패턴과 오류 발견

## 5. User Problems

- 범용 AI와 정보 서비스의 답변을 자신의 판단 기준에 연결하기 어렵다.
- 투자 전 가설·위험·반증 조건을 빠뜨리기 쉽다.
- 판단 당시 근거가 남지 않아 결과를 복기하기 어렵다.
- 분석과 기록이 분리되어 반복되는 오류를 발견하기 어렵다.

## 6. Current Alternatives

- ChatGPT·Claude 같은 범용 AI
- 증권사 리서치
- 일반 투자 정보 서비스
- 투자 Journal 앱
- 메모와 Spreadsheet

## 7. Product Differentiation

- Learn → Checklist → Journal → Review의 연결된 루프
- 구조화된 Finance Lens
- PolicyGuard
- 장기 사용자 맥락
- 과거 판단과 결과의 복기
- 이전 분석을 이용한 후속 질문

이 차별성은 투자 성과를 보장하는 것이 아니라 판단 과정과 복기 맥락을 연결하는 데 있다.

## 8. Core Product Loop

`Learn → Checklist → Journal → Review → follow-up`

사용자는 분석으로 질문을 구조화하고 Checklist로 판단 기준을 확인한 뒤 Journal에 가설과 근거를 남긴다. Review는 과거 판단과 실제 결과를 비교하여 다음 질문과 Checklist 개선으로 연결한다.

## 9. Paid Value

유료 가치는 더 많은 Usage, 넓은 범위, 다중 Lens, 반복·후속 분석 및 누적 Review 경험에서 나온다. 정확한 Plan 구성, 가격, Usage 한도는 이 문서에서 확정하지 않는다.

## 10. Premium Positioning

- Premium은 더 높은 투자 성공률을 의미하지 않는다.
- Premium은 더 정확한 투자 판단을 보장하지 않는다.
- Premium은 더 많은 Usage, 넓은 범위, 다중 Lens, 반복·후속 분석 및 누적 Review 경험을 의미한다.
- 모든 Plan의 Safety·PolicyGuard·Evidence 기준은 동일하다.

투자 성과·전문가 자문·더 정확한 판단을 암시하는 표현의 경계는 [Disclaimer and Copy Guide](finance-disclaimer-and-copy-guide.md)를 따른다.

## 11. Trust and Safety Messaging Boundary

- 서비스는 투자 판단을 대신하거나 특정 투자 행동을 권고하지 않는다.
- 분석 등급·Plan 차이는 안전성·PolicyGuard·Evidence 기준 차이가 아니다.
- 고비용 또는 넓은 범위의 분석도 전문가 자문이나 성과 보장을 뜻하지 않는다.
- Trial, Credit, Countdown, 할인은 자동 결제나 허위 희소성으로 압박하지 않는다.

## 12. Analysis Tier and Subscription Plan Separation

분석 등급과 Subscription Plan은 다른 개념이다. 사용자 노출 등급 후보는 `General`, `Deep`, `Extended`이며, 최종 Naming과 안정적 등급 구조는 검증 대상이다.

- Deep는 사용 전 차감 안내를 제공한다.
- Extended는 사용 전 명시적 확인을 제공한다.
- Extended는 더 정확하거나 전문가 자문이라는 의미가 아니다.
- 구체 AI Vendor와 모델명은 이 문서에 고정하지 않는다.
- 분석 등급에 `Pro`를 사용해 Subscription `Pro Plan`과 충돌시키지 않는 원칙은 유지한다.

## 13. Healthy Retention

건전한 Retention은 사용자가 누적 가치를 얻어 자발적으로 계속 사용하는 구조다.

- 누적 Journal
- Review History
- 과거 판단과 실제 결과 비교
- 개인화 Checklist
- 사용자별 Lens 선호
- 반복되는 투자 판단 오류 분석
- 이전 분석 기반 Follow-up

다음은 권고하지 않는다.

- Export를 어렵게 만들어 사용자를 가두는 방식
- 데이터 이동을 방해하는 Hostile Lock-in
- 사용자가 나갈 수 없어서 남는 구조

## 14. Export and Data Rights Boundary

상품형 Export 후보는 분석 Markdown, 개별 분석 PDF, Journal CSV, 기간별 Report, 분석·Journal 묶음 Export다. Plan별 차등 가능성은 검증 대상이다.

상품형 Export와 사용자 데이터 권리는 구분한다. 데이터 권리는 열람, 삭제, 동의 철회 및 법적으로 필요한 제공 절차를 포함할 수 있다. 적용 범위·형식·필드·보안·제공 방법은 [User Data Policy](finance-user-data-policy.md)의 Legal Review와 후속 검토 대상으로 남긴다.

## 15. Stable Principles

- 투자 판단을 대신하지 않는다.
- 모든 Plan의 Safety·PolicyGuard·Evidence 기준은 동일하다.
- 내부 모델 호출 수는 사용자 Usage가 아니다.
- Trial은 명시적으로 활성화하며 자동 결제·자동 유료 전환은 없다.
- Max 또는 Extended를 무제한 제공하지 않는다.
- 분석 등급과 Subscription Plan을 분리한다.
- Deep는 사용 전 안내, Extended는 사용 전 명시적 확인을 제공한다.
- 상품형 Export와 사용자 데이터 권리를 분리한다.
- Healthy Retention을 우선하고 Fake Scarcity를 사용하지 않는다.

## 16. Founder Candidates

다음은 현재 Product Policy가 아닌 Founder Product Decision Candidate다.

- 영구 Free Lite 재도입
- Trial 종료 후 Free Lite 복귀
- `General / Deep / Extended`의 안정적 사용자 분석 등급 채택
- Paid Plan에서 Extended를 제한적으로 제공하는 원칙
- 상품형 Export의 Plan별 차등 원칙

## 17. Validation Candidates

다음은 Launch Experiment Candidate이며 영구 정책·가격으로 해석하지 않는다.

- Free 또는 Reactivation General 분석 월 3회와 Calendar Month Reset
- Trial 활성화 기한 7일 또는 14일, 활성화 후 7일
- Deep 분석 2회와 Extended 분석 1회
- Plan별 Extended 횟수
- 가격, 할인율, 할인 대상 수, 할인 기간

## 18. Rejected or Currently Unadopted Directions

- 영구 Free Lite를 현재 Product Policy로 즉시 도입
- 12개월 50% 할인
- Free / Standard / Pro / Expert 4개 Plan 즉시 도입
- 자동 Deep 차감
- Extended를 더 정확한 판단이나 전문가 자문으로 광고
- Export 제한 기반 Hostile Lock-in
- 최고 등급이면 모든 요청에 최고가 모델을 사용하는 방식
- Fake Scarcity

## 19. Required Evidence

- 사용자 인터뷰와 행동 데이터로 확인한 가치·오인·불만
- Control 대비 증분 Paid Gross Margin과 Retention 효과
- 사용자·Plan별 P50·P90·P95 AI 원가와 Hard Cap 충족
- 다중 계정·중복 요청·Credit·Trial 반복 수령 Abuse 검증
- 상품형 Export와 데이터 권리 경계의 Legal Review
- Founder의 명시적 Product Decision

## 20. Related Canonical Documents

- [Finance Product Policy](../service-policy/finance-product-policy.md)
- [Finance Launch Experiment Values](../service-policy/finance-launch-experiment-values.md)
- [AI Usage and Quota Policy](../service-policy/finance-ai-usage-and-quota-policy.md)
- [Trial and Entitlement Policy](../service-policy/finance-trial-and-entitlement-policy.md)
- [Growth and Monetization Validation Plan](finance-growth-and-monetization-validation-plan.md)
- [Finance User Data Policy](finance-user-data-policy.md)
- [Finance Disclaimer and Copy Guide](finance-disclaimer-and-copy-guide.md)
