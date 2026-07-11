# Finance Harness 문서 체계 정리 보고서

> 목적: 현재 금융 하네스 문서의 계층, 각 문서군의 목적, 향후 확장 방향, 그리고 문서가 많아질 때 필요한 “문서 DB/의존성 관리” 구조를 정리한다.

---

## 1. 현재 결론

현재 금융 하네스는 **주식 중심 1차 분석 렌즈 MVP**를 거의 완료한 상태다.

```text
완료: 1차 Core Analysis Lens 10/10 (macro-rate-fx 포함 전종 완료)
```

현재 구조는 단순 금융 지식 모음이 아니다.  
정확히는 다음 6개 층으로 보는 것이 맞다.

```text
0차. Safety / Lens Infra
1차. Core Analysis Lens
2차. Long-term / Asset Class Lens
3차. Domain Deep-Dive Lens
4차. Service Contract / Test
5차. Product / Legal / Ops
6차. Professional Practice Standards
```

---

## 2. 0차 — Safety / Lens Infra

### 목적

렌즈가 함부로 투자 조언, 매수/매도 판단, 목표가, 손절가, 비중 제안으로 새지 않도록 막는 **상위 규칙 계층**이다.

### 주요 문서

| 문서 | 목적 |
|---|---|
| `00-service-policy.md` | 서비스 최상위 금지 원칙 |
| `finance-policyguard-rules.md` | 사용자 질문 intent 분류 |
| `finance-output-validator.md` | 최종 출력 검사 |
| `finance-market-data-grounding.md` | 현재 데이터 단정 금지, 출처/기준시각 요구 |
| `finance-prompt-templates-v2.1.md` | intent/action별 응답 구조 |
| `finance-routing-fixtures-v2.md` | 기존 테스트 기준선 |
| `finance-lens-catalog.md` | lens key/책임 등록부 |
| `finance-analysis-lens-pack-overview.md` | 렌즈 전체 분류와 확장 계획 |
| `finance-lens-authoring-standard.md` | 모든 per-lens 문서 작성 표준 |

### 핵심 규칙

```text
렌즈는 “무엇을 보는가”만 정의한다.
렌즈는 “그래서 무엇을 해야 하는가”를 정의하지 않는다.
```

---

## 3. 1차 — Core Analysis Lens Pack

### 목적

주식/시장 질문을 안전한 분석 체크리스트와 투자 일지 질문으로 바꾸는 기본 렌즈 세트다.

현재 1차는 **주식 중심 MVP**다.  
다만 일부 렌즈는 ETF, 채권, 환율, 포트폴리오 확장에도 재사용 가능하다.

### 1차-A. 공통 시장 렌즈

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-lens-market-context.md` | 완료 | 막연한 시장/종목 질문을 기간·범위·반응 요인으로 분해 |
| `finance-lens-supply-demand.md` | 완료 | 수급을 매매 신호가 아니라 주체/시장/기간/성격 확인 항목으로 분해 |
| `finance-lens-event-calendar.md` | 완료 | 이벤트 일정 확인과 시장 방향 예측을 분리 |

### 1차-B. 안전/일지/행동 렌즈

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-lens-risk-checklist.md` | 완료 | 리스크 기준을 AI가 제시하지 않고 사용자가 기록하게 함 |
| `finance-lens-journal-review.md` | 완료 | 과거 기록을 평가하지 않고 다음 기록 질문으로 전환 |
| `finance-lens-emotion-check.md` | 완료 | 감정과 판단 근거를 분리해 기록 |

### 1차-C. 주식/가치 렌즈

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-lens-earnings.md` | 완료 | 실적 일정, 기대, 컨센서스, 가이던스, 요인 분해 |
| `finance-lens-valuation.md` | 완료 | PER/PBR 등 지표를 가치 결론이 아니라 전제/비교축 확인으로 분해 |
| `finance-lens-sector-cycle.md` | 완료 | 업황/섹터/사이클을 업종 추천으로 새지 않게 분해 |

### 1차-D. 매크로/환율 렌즈

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-lens-macro-rate-fx.md` | 미완료 | 금리, 환율, 달러, 원화, 중앙은행, 외국인 수급, 매크로 이벤트를 확인 항목으로 분해 |

---

## 4. 2차 — Long-term / Asset Class Lens

### 목적

주식 중심 MVP를 장기 투자와 다양한 자산군으로 확장한다.

이는 “주식이 아니니까 2차”라는 의미가 아니다.  
정확히는 **장기 자산관리에서 같이 봐야 하는 자산군과 포트폴리오 구조**를 다루는 계층이다.

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-lens-asset-bond.md` | 예정 | 채권 금리, 가격, 듀레이션, 신용등급, 만기 구조 확인 |
| `finance-lens-cash-deposit.md` | 예정 | 예금, 적금, 파킹통장, 현금성 자산 조건 확인 |
| `finance-lens-etf-fund.md` | 예정 | ETF/펀드의 기초지수, 보수, 추적오차, 괴리율, 환헤지 확인 |
| `finance-lens-portfolio-structure.md` | 예정 | 자산 배분, 집중도, 통화 노출, 기간 구조 점검 |
| `finance-lens-long-term-plan.md` | 예정 | 장기 목표, 납입 방식, 점검 주기, 기록 기준 정리 |

### 장기 주식 투자는 어디에 들어가나?

장기 주식 투자는 1차와 2차를 함께 쓴다.

```text
장기 주식 투자 =
1차: earnings / valuation / sector-cycle / journal-review / risk-checklist
+
2차: portfolio-structure / long-term-plan
```

---

## 5. 3차 — Domain Deep-Dive Lens

### 목적

기본 렌즈보다 더 깊은 시장 구조와 전문 도메인 분석 기준을 만든다.

이 계층은 “유행하는 시장 보고서”가 아니다.  
돈이 쏠리는 시장을 따라가는 것이 아니라, **돈이 쏠릴 때 무엇을 확인해야 하는지**를 전문가 관점으로 구조화한다.

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-equity-kr-market-structure-lens.md` | 예정 | 한국장 외국인/기관/프로그램/공매도/지수 편출입 구조 |
| `finance-equity-us-market-structure-lens.md` | 예정 | 미국장 ETF flow, buyback, earnings season, mega-cap concentration |
| `finance-semiconductor-cycle-lens.md` | 예정 | DRAM/NAND/HBM/ASP/재고/capex/AI 인프라 사이클 |
| `finance-fx-rate-cross-asset-lens.md` | 예정 | USD/KRW, USD/JPY, DXY, 금리차, 외국인 수급과 환율 |
| `finance-earnings-revision-lens.md` | 예정 | 컨센서스 revision, margin bridge, guidance, 실적 기대 변화 |

---

## 6. 4차 — Service Contract / Test

### 목적

0차~3차 문서를 실제 서비스에서 안전하게 동작시키는 **실행 계약 계층**이다.

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-routing-contract-v1.md` | 예정 | 질문 → intent → lens → redirect → output shape 계약 |
| `finance-routing-fixtures-v3.md` | 예정 | 1차/2차 렌즈 반영 테스트 케이스 |
| `finance-red-team-cases-v1.md` | 예정 | 우회 질문, 악의적 질문, 경계 질문 |
| `finance-output-examples-v1.md` | 예정 | 실제 안전 응답 예시 |
| `finance-policyguard-alignment.md` | 예정 | 렌즈 금지 표현을 policyguard/validator에 반영할지 검토 |
| `finance-lens-index.md` | 예정 | 전체 lens 목록, 상태, 의존성, 라우팅 가능 여부 정리 |

### 역할

```text
입력 전: PolicyGuard가 위험 intent 분류
응답 생성 중: Routing이 lens 선택
출력 전: Output Validator가 금지 표현 검사
```

---

## 7. 5차 — Product / Legal / Ops

### 목적

금융 질문에 직접 답하는 문서가 아니라, 서비스가 법적·운영적으로 사고 나지 않게 만드는 내부 거버넌스 문서다.

| 문서 | 상태 | 목적 |
|---|---:|---|
| `finance-legal-risk-review-notes.md` | 예정 | 투자자문/일임 오인 가능성, 금지 UX, 광고 문구 리스크 |
| `finance-disclaimer-and-copy-guide.md` | 예정 | 서비스 고지, 버튼 문구, 마케팅 문구 가이드 |
| `finance-user-data-policy.md` | 예정 | 일지/OCR/이미지/민감정보 저장·삭제·접근로그 정책 |
| `finance-ops-monitoring-guide.md` | 예정 | 위험 응답 로그, false positive/false negative 개선 루프 |

---

## 8. 6차 — Professional Practice Standards

### 왜 추가해야 하나?

해외 탑급 시니어 트레이더나 자산관리사가 보는 기준은 단순히 “종목 분석”이 아니다.  
오히려 다음을 본다.

```text
목표
기간
유동성
위험 한도
현금흐름
세금
비용
상품 구조
자산 배분
리밸런싱 정책
기록/검증 체계
```

따라서 6차를 별도로 두는 것이 좋다.

### 관련 직군

| 직군 | 보는 기준 |
|---|---|
| Portfolio Manager | 자산 배분, 위험 한도, 성과 기여도, drawdown |
| Wealth Manager / Private Banker | 고객 목표, 유동성, 세금, 가족 자산 구조 |
| Financial Planner | 생애 주기, 현금흐름, 목표 기반 계획 |
| Investment Strategist / CIO Office | 매크로, 자산군 전망, 정책 포트폴리오 |
| Trader | 유동성, 체결, 포지션 리스크, 이벤트 리스크 |
| Risk Manager | VaR보다 더 실무적으로 drawdown, concentration, stress scenario |
| Credit Analyst | 신용등급, 현금흐름, 만기, 부도 가능성 |
| Equity Analyst | 실적, 산업 구조, valuation 전제 |
| ETF/Fund Analyst | 기초지수, 비용, 추적오차, 유동성, 운용 구조 |
| Pension / Endowment Manager | 장기 부채, 자산배분 정책, 리밸런싱 거버넌스 |
| Tax / Estate Planner | 세금, 계좌 래퍼, 상속/증여 구조 |
| Compliance Officer | 적합성, 설명의 경계, 금지 표현, 기록 증빙 |

### 6차 후보 문서

| 문서 | 목적 |
|---|---|
| `finance-investment-policy-statement.md` | 투자 목적, 기간, 허용 손실, 금지 행동, 점검 주기 기록 |
| `finance-liquidity-cashflow-lens.md` | 비상금, 생활비, 목돈 지출, 투자 가능 자금 분리 |
| `finance-product-due-diligence.md` | ETF/채권/펀드/예금의 비용, 유동성, 구조, 발행자/운용사 리스크 확인 |
| `finance-rebalancing-policy-lens.md` | 리밸런싱 조건/주기/기록 기준 점검. 비중 제안 금지 |
| `finance-tax-account-wrapper-checklist.md` | ISA, 연금저축, IRP, 일반계좌, 해외주식/배당/이자 세금 확인 |
| `finance-scenario-planning-lens.md` | Base/Bull/Bear 시나리오를 예측이 아니라 가정 목록으로 기록 |
| `finance-data-source-hierarchy.md` | 공시/거래소/중앙은행/통계기관/IR/뉴스/커뮤니티 출처 우선순위 |
| `finance-decision-journal-schema.md` | 질문, 가정, 확인 데이터, 감정, 반증 신호, 사후 복기 필드 표준화 |
| `finance-review-cadence-governance.md` | 일간/주간/월간/분기 점검 주기와 책임 범위 |
| `finance-cost-fee-liquidity-checklist.md` | 보수, 스프레드, 세금, 중도해지, 환전비용 등 비용 구조 확인 |

---

## 9. Learning Concept Layer

### 목적

기본 금융 지식을 렌즈 안에 흩뿌리지 않고, 개념 사전처럼 분리한다.

```text
Concept = 설명
Lens = 확인 질문으로 변환
PolicyGuard = 위험 질문 차단
Fixture = 테스트
```

### 후보 문서

| 문서 | 연결 렌즈 |
|---|---|
| `concept-per-pbr.md` | valuation |
| `concept-consensus-guidance.md` | earnings |
| `concept-fomc-cpi.md` | event-calendar, macro-rate-fx |
| `concept-foreign-institution-flow.md` | supply-demand |
| `concept-bond-duration.md` | asset-bond |
| `concept-etf-tracking-error.md` | etf-fund |
| `concept-deposit-interest-tax.md` | cash-deposit |

---

## 10. 문서 DB / Registry 구조

### 왜 필요한가?

문서가 많아질수록 단순 폴더와 파일명만으로는 관리가 어렵다.

필요한 것은 “Markdown 문서 DB”다.

```text
문서 = row
frontmatter = column
index/registry = query
dependency map = relationship
```

### 권장 frontmatter

```yaml
---
doc_id: finance.lens.valuation
title: finance-lens-valuation
layer: 1
type: lens
status: draft-v0.1
visibility: internal
routing_enabled: false
client_exposable: false
guard_required: true
depends_on:
  - finance.lens.catalog
  - finance.lens.authoring_standard
  - finance.policyguard.rules
  - finance.output_validator
related_lenses:
  - earnings
  - market-context
  - sector-cycle
fixtures:
  - RF-04
  - DG-03
  - AM-03
owner: finance-harness
last_reviewed: 2026-07-09
---
```

### 핵심 필드

| 필드 | 목적 |
|---|---|
| `doc_id` | 문서 고유 ID |
| `layer` | 0~6차 계층 |
| `type` | policy, lens, concept, fixture, contract, legal, ops |
| `visibility` | internal / restricted / public-safe |
| `client_exposable` | 클라이언트에 노출 가능한지 |
| `routing_enabled` | 실제 라우팅에 사용 가능한지 |
| `guard_required` | policyguard 선행 필수 여부 |
| `depends_on` | 의존 문서 |
| `fixtures` | 연결 테스트 케이스 |
| `related_lenses` | 연관 렌즈 |

---

## 11. 노출 제어 구조

### 문제

이 문서들은 프로젝트/클라이언트 레벨에 그대로 노출되면 안 된다.

이유:

```text
- 정책 우회 힌트가 포함될 수 있음
- 금지 표현 목록이 prompt injection 재료가 될 수 있음
- 내부 라우팅/거절 규칙이 공격 표면이 될 수 있음
- 법무/운영 문서는 외부 공개용이 아님
```

### 권장 구조

```text
finance-knowledge-private/
  registry/
    finance-doc-registry.yaml
    finance-lens-index.md
    finance-dependency-map.md
    finance-exposure-manifest.md

  internal/
    safety-core/
    lens-infra/
    lens/
    contracts/
    professional-standards/
    legal-ops/

  public-safe/
    learning-concepts/
    sanitized-examples/
```

### 원칙

```text
1. internal 문서는 클라이언트에 직접 전달하지 않는다.
2. public-safe 문서는 별도 sanitize 과정을 거친다.
3. 서버는 internal 문서를 참조해 답변을 생성하되, 사용자에게는 결과만 제공한다.
4. prompt에는 금지 규칙 전체를 노출하지 않고, 필요한 policy summary만 주입한다.
5. client_exposable=false 문서는 앱/클라이언트 번들에 포함하지 않는다.
```

---

## 12. 다음 우선순위

```text
1. finance-lens-macro-rate-fx.md 완료
2. finance-lens-index.md 생성
3. finance-doc-registry.yaml 생성
4. finance-routing-contract-v1.md 생성
5. finance-routing-fixtures-v3.md 생성
6. finance-red-team-cases-v1.md 생성
7. finance-output-examples-v1.md 생성
8. 2차 자산군 확장 시작
9. 6차 Professional Practice Standards 시작
```

---

## 13. 최종 판단

현재 문서 체계는 주식 중심 금융 분석 MVP로는 충분히 강하다.

하지만 장기 자산관리, 가족용 자산 점검, ETF/채권/예금, 전문 자산관리 기준까지 가려면 2차와 6차가 중요하다.

```text
1차 = 주식 중심 분석 MVP
2차 = 장기 투자와 자산군 확장
3차 = 시장/섹터/구조 심화
4차 = 라우팅/테스트/출력 계약
5차 = 법무/제품/운영
6차 = 전문가 자산관리 기준
```

특히 문서가 계속 늘어나는 현재 상황에서는 `finance-doc-registry.yaml`, `finance-lens-index.md`, `finance-dependency-map.md`, `finance-exposure-manifest.md`를 먼저 만들어야 한다.

이 네 개가 있어야 문서 체계가 단순 md 묶음이 아니라, 접근 제어와 라우팅을 가진 내부 지식 DB처럼 관리된다.
