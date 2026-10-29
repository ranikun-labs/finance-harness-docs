# Finance AI Agent — 제품 비전·실행 가능한 MVP 행동 계약 (제안)

> 상태: **PROPOSED / NOT_EFFECTIVE** · Owner 기획 방향 선택: 2026-10-29 · 정식 정책 승인·구현 완료·운영 출시 아님
>
> 소유: Finance Product · 소비: Finance BE/FE, Shared AI · 문서 유형: 신규 행동 계약 초안 v0.1
>
> 우선순위: 기존 Finance Product Policy → Safety Core/PolicyGuard/Output Validator → Accepted Finance·Shared AI ADR → 이 제안. 상충 시 이 문서가 상위 정책을 변경하지 않으며 별도 Owner 결정을 요구한다.

## 1. F1 — 처음 보는 사람을 위한 핵심정리

Finance Harness의 장기 목표는 단순 금융 챗봇이나 질문 체크리스트 생성기가 아니다. 사용자가 직접 입력·선택한 투자 판단 기록, 검증할 수 있는 시장·기업 자료, 전문 금융 분석 Lens, 허용된 이전 대화의 맥락을 결합해 **근거와 가정을 구분하면서 깊이 있게 분석·토론하는 금융 AI Agent**를 만드는 것이다.

제품은 최종 투자 행동을 대신 결정하는 것이 아니라, 사실·사용자 주장·분석 해석·불확실성·미래 조건부 가정·반론·남은 질문을 명확히 보여준다. 장기 목표와 현재의 실제 동작 상태를 혼동하지 않는다.

## 2. 장기 사용자 경험 네 가지

| 사용자 관심사 | 원하는 경험 | 초기에 허용할 최소 범위 |
|---|---|---|
| ① 내 투자 | “내가 기록한 반도체 투자 가정과 아직 확인하지 못한 위험은?” | **본인이 명시 선택한 Journal** 및 권한 있는 기록에 대한 조회 전용 분석. 보유 Portfolio 전체 정본 관리·계좌 자동 연동은 후속 결정 |
| ② 시장 현황 | “반도체 업황과 금리 변화에 어떤 근거가 있어?” | 승인된 자료의 출처·기준시각을 확인해 설명. 데이터 제공처·라이선스가 없으면 현재 상황을 지어내지 않음 |
| ③ 미래 가능성 | “금리 인하와 경기 둔화가 겹치면 어떤 시나리오가 가능할까?” | 가정·기간·인과·반론·확인할 지표를 비교. 단정적 목표가·투자 행동 지시 없음 |
| ④ 대화 연속성 | “지난번에 말한 공급과잉 가설을 다시 검토해 줘” | 같은 세션의 허용된 대화 맥락 + 사용자 지정 기록을 사용. 영구 대화 저장·장기 기억은 별도 동의/삭제 정책 Gate |

이는 **목표 사용자 경험**이지, 현재 4가지 기능이 모두 Runtime에 구현됐다는 주장이 아니다.

## 3. 무엇을 MVP라 부를 것인가?

MVP도 **실제 Agent 실행 흐름이 한 번 완성되는 수준**이어야 한다. 프롬프트에 금융 전문가 역할을 넣은 LLM 단발 호출, 동일 Fixture PASS, 추상 인터페이스 작성만으로는 MVP 완료가 아니다.

권장 최소 실행 시나리오:

1. Finance가 인증 사용자와 대상 Resource 소유권을 검증한다.
2. 기존 Input PolicyGuard가 질문을 분류한다. **Guard보다 먼저 Lens나 Tool을 실행하지 않는다.**
3. 현재 사용 가능한 Core Expert Lens 중 질문에 맞는 1~3개를 선택한다. Draft·비활성 Lens 7개를 자동으로 활성화하지 않는다.
4. 명시적으로 허용된 **실제 조회 전용 Tool을 최소 하나** 호출한다. 사용자 소유 Journal 같은 Product Tool부터 시작할 수 있다.
5. 시장 최신 자료까지 MVP에 포함하려면 데이터 제공처의 사용·재제공 권한, 출처, 신선도, 기준시각을 별도 승인해야 한다. 아직 승인되지 않았다면 시장 Tool 구현을 성공으로 표시하지 않는다.
6. 조회 결과와 출처·시간·사용자 입력·모델 해석을 분리하고, 필요한 수치·규칙 계산은 결정적 코드에서 수행한다.
7. Shared AI 실행 결과를 Finance Output Validator와 Evidence 검증을 거쳐 사용자에게 제공한다.
8. 같은 세션의 후속 질문에서 방금 사용한 가정·근거와 사용자가 선택한 Journal을 참조한다. 원문 장기 저장을 기본 허용하지 않는다.
9. 사용자가 명시 저장하지 않은 경우 Journal·Portfolio·투자 상태는 자동 변경하지 않는다.

**완료 판정:** 실제 허용 Tool 호출, 올바른 Lens 선택, 정책/권한 검사, 근거가 있는 출력, 같은 세션 후속 대화가 **동일한 실제 워크플로우**에서 검증되어야 한다.

## 4. 목표 시스템과 책임

### Finance Product — 무엇을 분석하고 허용할지 결정

- 질문·Checklist·Journal·Analysis·Review·후속 Portfolio 데이터의 의미와 정본 소유
- CurrentUserPort, 사용자/Resource 인가와 Tenant 경계
- Finance Input PolicyGuard, 전문 Lens/Skill 정의·선택, 최종 Output Validator
- 사용자 동의, 데이터 최소 전송, 사용자 기록 수정·삭제, 법무/서비스 정책 준수

### Shared AI — 공용 실행 기반 담당

- **현재 Phase 1: Platform Core의 Kotlin/JVM + Spring AI Adapter.** 과거 FastAPI 개념도와 구분
- 선언된 Skill을 실행하는 범용 Runtime, Product Tool/Retriever/Model 포트, 실행 ID/Context version, Correlation/Evidence/Scope 검사, Timeout·Telemetry
- Finance DB 직접 소유·조회 금지, Finance 도메인 투자 정책의 재결정 금지
- Finance가 선택·승인한 범위 내에서만 Tool 수행. 보호된 Tool/Retriever는 실행 시점의 최신 Product 권한을 확인하지 못하면 Fail-closed

### 통합 단계

| 수준 | 실제 의미 | 완료 상태 |
|---|---|---|
| L1 | 양쪽 저장소에서 동일한 논리 소비자 계약 테스트 | 기존 PASS |
| L2 | 실제 컴파일되는 Outbound/Inbound Bridge와 교차 계약 테스트 | 미완료 |
| L3 | 두 서비스 사이 실제 HTTP 요청/응답 | 미완료 |
| L4 | 신뢰된 실제 사용자·Tenant/Resource 권한 E2E | 미완료 |
| 출시 | 운영 Credential·Provider·보안·법무·배포 승인 | 별도 Gate |

내부 Direct HTTP/JSON은 Foundation ADR-0015의 기준 방향. 실제 endpoint, wire DTO, timeout, 서비스 인증은 별도 결정이므로 임의 확정하지 않는다.

## 5. Agent / Lens / Tool / Memory / Guardrail 구분

| 요소 | 쉬운 설명 | 초기 설계 원칙 |
|---|---|---|
| Agent Orchestrator | 어떤 확인을 어떤 순서로 할지 조정하는 실행 감독 | **단일 bounded Orchestrator**부터 시작. 무한 반복·자율 쓰기 금지 |
| Expert Lens / Skill | 실적·수급·가치평가·위험 등 전문가 분석 관점 | 기존 Catalog 재사용. Lens 문서가 존재해도 실제 Tool이나 활성 Runtime을 뜻하지 않음 |
| Tool | 실제 DB/공시/시장 자료 조회나 계산을 하는 코드 | 명시적 Allowlist, 인가된 Resource, 입력 검증·시간/비용 상한 |
| Memory / Context | 이전 대화·투자 일지·선택 기억·분석 당시 자료 | 원본 투자 기록, 대화, 파생 기억, 실행 Snapshot을 분리. 영구 저장은 명시 동의/삭제 정책 선행 |
| Hook / Guardrail | 권한·안전 규칙 위반을 코드에서 막는 검사 | 입력 전, Tool 호출 전, Evidence 처리 시, 출력 노출 전 강제 실행 |
| Reviewer | 중요한 분석 결과의 추가 독립 검증 | 충돌 자료·고위험 요청 등 필요성이 입증된 때만 호출. 상시 멀티에이전트는 MVP 필수 아님 |

## 6. 근거·안전·품질 기준

- 검증된 외부 사실은 출처 ID·게시 시점·적용 시각·단위를 갖는다. 사용자 입력 가격/보유 정보는 시장의 검증된 현재 사실과 구분한다.
- 최신 시황 자료가 없으면 임의의 현재가나 컨센서스를 만들어내지 않고 **UNKNOWN / 조회 불가**를 표시한다.
- 다른 사용자의 Journal·대화·Evidence는 검색·임베딩·캐시·Tool 등 어떤 경로로도 섞이지 않는다.
- Prompt Injection이 Tool 권한을 확대하지 못한다. Tool 결과에 담긴 자연어 명령은 권위 있는 정책이 아니다.
- 모델 출력의 Evidence ID는 실제 승인된 조회 결과와 연결되어야 하며, 없으면 주장 노출을 차단하거나 제한한다.
- 숫자 계산과 유효일 기준 규칙은 Kotlin/Java 등의 결정적 코드로 검증한다.
- Golden Set: 권한 A/B 격리, 실제 Tool 실패, 오래된 데이터, 가짜 출처, 잘못된 Tool 인자, 계산, Timeout/중복, 조건부 시나리오, 후속 대화, 삭제/동의 경계를 검증한다.
- 측정: 정확한 Task Success, Unsupported Claim, Tool Selection/Argument Accuracy, Latency P50/P95, 사용량·비용. 미측정은 UNKNOWN.

## 7. 단계별 개발 계획

**A. 기존 통합 완결:** RPL-127 Finance + RPL-126 Shared AI의 General Analysis 첫 Slice에서 L2/L3 통합을 진행한다. 새 장기 Agent 기능을 해당 Jira 범위에 임의 추가하지 않는다.

**B. 실제 Agent MVP:** 허용된 조회 Tool 최소 하나, 기존 사용 가능한 Lens 일부, 정책·근거 기반 구조화 답변, 세션 후속 대화, 재현 테스트를 한 경로에서 동작시킨다.

**C. 개인화 Research:** 사용자 선택형 Portfolio·장기 Memory, Market Provider 다중화. 제품·데이터·법무·권한·삭제 승인을 별도로 받는다.

**D. 고급 Agent:** 조건부 전문가/Reviewer, 풍부한 시나리오, 더 많은 Tool·자료원. 품질·비용 측정으로 확장 여부를 판단한다.

**E. 운영:** Provider·보안·출시 Gate, 지속성·관측성·비용 최적화를 실측 기준으로 판단한다.

## 8. 아직 Owner가 별도로 결정해야 하는 것

1. MVP에 붙일 첫 시장 Evidence Source와 라이선스·최신성 정책
2. 운영 Finance Tenant/Authority 발급 근거와 보호된 Tool의 실행 시점 인가
3. 실제 HTTP wire schema·endpoint·timeout·서비스 인증
4. 장기 대화 원문 저장·검색·정정·삭제 및 사용자 동의
5. Portfolio 정본·계좌연동·개인 맞춤 투자자문 확장 여부와 정책/법무 검토
6. Draft Lens 활성화 여부 및 개별 회귀 검증

위 사항은 이 문서의 존재만으로 승인되지 않는다.

## 9. 정본 배치 및 참고

- [Confluence Finance — Backend·AI 실행 로드맵](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/18153473/Finance+Harness+Backend+AI): 제3자용 상위 제품 목표·개발 단계·현재 증거
- 본 문서: **제안된 Finance Agent 행동과 MVP 완료 기준**. 기존 계약을 대체하지 않음
- [Finance Routing Contract](finance-routing-contract.md), [Finance Lens Catalog](../0-b-lens-infra/finance-lens-catalog.md), [Finance Product Policy](../../service-policy/finance-product-policy.md), [Finance User Data Policy](../../05-product-legal-ops/finance-user-data-policy.md): 현재 의미/정책 정본
- [Shared AI ADR-0001](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0001-skill-lens-workflow-execution-ownership.md), [ADR-0004](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0004-execution-context-api-tenant-isolation.md): 공용 실행 책임 및 Scope 경계
- [Confluence 06. 외부 AI 기술 레퍼런스](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/26902529/06.+AI): Harvard 평가, Google EGM 설계 검수, Upstage 결정적 Tool 패턴의 **도입 후보**, 현재 구현 증거 아님
