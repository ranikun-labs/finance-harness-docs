# Finance AI Agent — 제품 비전·실행 가능한 MVP 행동 계약 (제안)

> 상태: **PROPOSED / NOT_EFFECTIVE** · Owner 기획 방향 선택: 2026-10-29 · MVP Q4 범위 합의: 2026-11-02 · 정식 정책 승인·구현 완료·운영 출시 아님
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
| ④ 대화 연속성 | “지난번에 말한 공급과잉 가설을 다시 검토해 줘” | 같은 세션의 최소 Context + 사용자가 명시적으로 “기억해줘”라고 요청한 허용 선호·판단 기준의 별도 Memory를 사용. 영구 대화 원문·자동 Memory 추출은 제외. 영구 Memory 운영 활성화는 동의·보존·삭제·백업·감사 Gate 승인 후 |

이는 **목표 사용자 경험**이지, 현재 4가지 기능이 모두 Runtime에 구현됐다는 주장이 아니다.

## 3. 무엇을 MVP라 부를 것인가?

MVP도 **실제 Agent 실행 흐름이 한 번 완성되는 수준**이어야 한다. 프롬프트에 금융 전문가 역할을 넣은 LLM 단발 호출, 동일 Fixture PASS, 추상 인터페이스 작성만으로는 MVP 완료가 아니다.

권장 최소 실행 시나리오:

1. Finance가 인증 사용자와 대상 Resource 소유권을 검증한다.
2. 기존 Input PolicyGuard가 질문을 분류한다. **Guard보다 먼저 Lens나 Tool을 실행하지 않는다.**
3. 현재 사용 가능한 Core Expert Lens 중 질문에 맞는 1~3개를 선택한다. Draft·비활성 Lens 7개를 자동으로 활성화하지 않는다.
4. 사용자가 명시 선택한 Journal 한 건에 대해 Finance Product가 소유한 조회 전용 Tool을 호출한다. Finance는 호출 시점마다 신뢰된 Principal·Tenant·Resource 권한 출처와 선택 범위를 확인한다. Tenant/Authority 출처가 승인되지 않았거나 확인되지 않으면 호출을 차단한다. Shared AI는 Finance DB를 직접 읽지 않으며 Tool 결과 Scope가 실행 Context보다 넓으면 거부한다. 입력·출력·실패 계약은 §5.1을 따른다.
5. 시장 최신 자료까지 MVP에 포함하려면 데이터 제공처의 사용·재제공 권한, 출처, 신선도, 기준시각을 별도 승인해야 한다. 아직 승인되지 않았다면 시장 Tool 구현을 성공으로 표시하지 않는다.
6. Journal 내용과 사용자가 입력한 수치는 ‘사용자가 기록한 내용’으로 표시하고 외부에서 검증된 시장 사실로 승격하지 않는다. Tool 결과의 source ID, 기록 시각, 수정 시각, 조회 시각 및 원본에서 확인 가능한 revision을 모델 해석과 분리한다. 필요한 수치·규칙 계산은 결정적 코드에서 수행한다.
7. Shared AI 실행 결과를 Finance Output Validator와 Evidence 검증을 거쳐 사용자에게 제공한다.
8. 같은 세션 후속 질문은 §5.2의 제한된 Context만 사용한다. 매 턴 Input PolicyGuard를 다시 실행하고, 선택 Journal은 현재 권한으로 재인가·재조회한다. 로그아웃·세션 만료·동의 또는 선택 철회·원본 삭제 시 해당 Context와 파생 참조·캐시를 사용 불가로 만든다. TTL·삭제 전파 정책이 승인되지 않으면 이 기능을 활성화하지 않는다.
9. **명시적 장기 Memory(새 MVP Slice):** 사용자가 “이 기준을 다음에도 기억해줘”처럼 명시적으로 요청한 허용된 선호·판단 기준에 한해, Finance가 분류·정규화·권한·중복·동의·보존 정책을 검증한 후 별도의 Product-owned Write Command로 저장한다(§5.3). 일반 진술만으로 자동 장기 저장하지 않고 LLM이 DB/Tool에 직접 쓰지 않는다. 실제 영구 쓰기 활성화는 Finance User Data Policy의 별도 Gate 승인 전 금지한다.
10. 사용자가 명시 저장하지 않은 경우 Journal·Portfolio·투자 상태는 자동 변경하지 않는다.

**완료 판정(제품 목표):** 실제 허용 Tool 호출, 올바른 Lens 선택, 정책/권한 검사, 근거가 있는 출력, 같은 세션 후속 대화, 명시적 Memory 저장·후속 조회·삭제/철회가 **승인된 테스트 경로**에서 검증되어야 한다. **구현 시험 PASS ≠ 영구 저장 운영 승인**. 동의·보존·삭제·백업·감사 Gate가 미완료면 Production Memory 쓰기는 OFF이며 출시 가능 MVP라고 주장하지 않는다.

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
| Memory / Context | 이번 대화의 맥락·명시 저장한 사용자 기준·원본 Journal | Conversation Context(일시), Explicit Long-term Memory(Product 소유), Journal SSOT, Execution State를 분리. AI 추론·분류 로그 자동 프로파일링 금지. 영구 저장 활성화는 데이터·보존/삭제 정책 승인 선행 |
| Hook / Guardrail | 권한·안전 규칙 위반을 코드에서 막는 검사 | 입력 전, Tool 호출 전, Evidence 처리 시, 출력 노출 전 강제 실행 |
| Reviewer | 중요한 분석 결과의 추가 독립 검증 | 충돌 자료·고위험 요청 등 필요성이 입증된 때만 호출. 상시 멀티에이전트는 MVP 필수 아님 |

### 5.1 첫 Product Tool: SelectedJournalRead (제안 계약)

- 입력은 사용자가 선택하고 Finance가 검증한 Journal 참조 한 건, 허용된 필드 집합, 인증된 실행 Context 참조와 요청 Deadline으로 제한한다. 모델/사용자가 전달한 Principal·Tenant·Owner ID나 자유 검색 조건으로 Resource 범위를 넓히지 않는다. 정확한 wire DTO와 endpoint는 별도 승인 Gate다.
- Finance Adapter는 기존 owner-scoped Journal 상세 조회 경계를 통해 매 호출 시점의 Principal·Tenant·Journal 접근권과 선택 상태를 검증한다. 신뢰된 Tenant/Authority 공급원이 없으면 Fail-closed다. Shared AI는 Context 무결성, Tool capability allowlist와 결과 Scope 비확대를 검증한다. Resource ID나 과거 Context snapshot은 인가 증거가 아니다.
- 정상 응답은 typed `SelectedJournalReadResult`로, Journal 참조, Finance 전용 `sourceClass=USER_AUTHORED_JOURNAL`, 선택된 typed 내용, 기록/수정 시각, Tool 관측 시각, 원본이 제공하는 경우에만 revision, Core Evidence 참조를 담는다. 실제 필드 allowlist는 Finance User Data Policy와 Owner 검토 후 정한다. 이 source class는 Finance 제품 확장이고 Shared AI 공용 enum을 변경하지 않는다.
- Tool은 조회 전용이며 Journal·Portfolio·Analysis를 저장하거나 수정하지 않는다. 무인가·권한 불일치/철회·타인 Resource·Deadline 초과·Tool 실패·필수 데이터 누락은 fail-closed 처리하고, 원문이나 부분 응답은 노출하지 않는다. 존재 여부 노출을 막는 안전한 오류 범주를 사용한다.
- Timeout 수치와 재시도 계약은 미결정이다. Owner가 별도 승인하기 전 자동 재시도는 하지 않는다. 향후 재시도가 승인되더라도 매 시도마다 현재 권한을 재검증하고 전체 Deadline을 넘기지 않는다.

### 5.2 같은 세션 Context 경계 (제안 계약)

- Finance Product가 세션 의미, 사용자 선택, 데이터 최소화, 동의·철회·삭제 책임을 소유한다. Shared AI는 요청 단위 Context를 운반·검증하며 Finance의 영구 대화 저장소가 되지 않는다. 세션 참조는 현재 인증 사용자와 Product/Tenant Scope에 묶이지만, 참조값만으로 접근을 허용하지 않는다.
- 후속 Context는 승인된 최소 질문·가정·Evidence 참조로 제한한다. 원문 전체, 모든 Journal, embedding/index 저장은 기본 비활성이다. 명시적 사용자 요청의 장기 Memory는 §5.3의 **별도 Product Command** 경로로만 다룬다. 같은 세션 Context 자체를 영구 기억으로 자동 승격하지 않는다. 매 턴 PolicyGuard, 선택 Resource 재인가·재조회, Output Validator를 다시 수행하며 이전 판정이나 인증 snapshot을 승계하지 않는다.
- 로그아웃/세션 만료, 동의·선택 철회, Journal 삭제·권한 변경 시 Context와 파생 캐시·참조를 검색·실행에 사용할 수 없게 한다. 저장 위치, TTL, 크기/턴 상한, 삭제 전파, 감사 메타데이터는 Finance User Data Policy와 Owner 승인 Gate다. 값과 실행 책임이 정해지기 전 same-session follow-up은 구현 완료로 보지 않는다.


### 5.3 명시적 장기 Memory — MVP 추가 계약 (2026-11-02, 제안)

**사용자 경험:** “나는 실적 발표 전에 컨센서스를 확인하는 편이야”는 **현재 대화에서만 자동 반영**한다. “이 기준을 다음 대화에서도 기억해줘”라는 **명시적인 저장 명령**이 들어온 경우에만 영구 저장을 시도한다. AI가 먼저 “기억할까요?”를 제안하거나 발언을 자동 추출해 저장하는 UX는 V2 후보이며 MVP 필수가 아니다.

**저장 가능 범위:** 사용자가 직접 지정한 *선호·검토 기준* 중 Finance 정책이 허용한 정보만. Journal에 실제 매수·매도했다는 주장, 보유 수량·금액·계좌, 위험 Intent 누적·안전 로그 기반 사용자 성향 분석, 모델 추론/프로파일은 Memory에 원본 사실로 기록하지 않는다. 현재 User Data Policy P-08의 **프로파일링 금지**를 우회하지 않으며, 신규 저장 정보의 데이터 분류·사용 목적·허용 필드·동의/고지·보존 상태는 Product/Privacy Owner의 별도 결정 사항이다.

**읽기:** Finance가 현재 인증 사용자와 Product Scope로 승인된 유효 Memory만 조회해 요청별 최소 Context로 전달한다. Memory는 **제품 정본 Journal 사실이나 매수 여부의 증거가 아니다.** 현재 원본과 충돌하면 원본 조회 결과가 우선하며, 근거가 확인되지 않으면 “확인 불가”로 답한다. 승인하지 않은 다른 사용자·제품의 Memory는 이용하지 않는다.

**쓰기:** Shared AI LLM이 사용자 ID·권한·DB 쓰기 Tool을 생성하거나 정하지 않는다. Finance Application Service가 의도·정책·Actor를 검증한 **별도 명시적 저장 Command**만 처리한다. 같은 명령의 중복 전송/응답 유실은 요청 Scope에 묶인 멱등성으로 처리하되, 같은 내용을 다른 시점에 변경할 수 있으므로 무조건 내용 전체를 Dedup하지 않는다. 동시 수정·삭제 충돌 및 삭제 후 Stale Callback의 재생성 차단을 구현 계약에 명시한다.

**사용자 통제:** 저장된 Memory의 본인 조회·정정·삭제가 함께 제공되어야 한다. 삭제·권한 철회 즉시 이후 읽기에 부적격하도록 차단하고, 인덱스·파생 Cache·백업 정리는 승인된 보존/복구 정책에 따라 추적한다. 실제 물리 삭제의 완료를 검증하지 않고 전체 파기 완료라고 주장하지 않는다.

**Fail-closed / 운영 Gate:** 동의·허용 데이터 범위·Retention 기간·Backup·Audit·삭제 전파 및 Provider 전송 필드 계약이 승인되지 않으면 **영구 저장/재조회 Production 활성화를 차단**한다. Synthetic Test에서의 Write/Read PASS는 Production 허가가 아니다. Raw Conversation History·사용자 투자 성향 자동 프로파일링은 기본 비활성이다.

**관련 기술 설계:** [Finance Agent MVP 기술 설계 초안](finance-agent-mvp-technical-design-proposal.md). 승인된 API/DB Schema/기술 Stack은 아직 없으며 해당 문서의 Port·필드는 예시 논리 계약이다.

## 6. 근거·안전·품질 기준

- Provenance는 Core Evidence envelope의 `evidence_id/kind/source_refs/producer_ref/observed_at/execution_ref/scope_ref/schema_version`를 유지하고 Finance typed extension으로 `sourceClass`(사용자 작성 Journal 또는 승인된 외부 자료), Journal 참조, 기록/수정/조회 시각, 원본에 존재하는 경우에만 revision, 사용자 입력 라벨을 분리한다. 실제 Journal에 없는 revision을 만들어내지 않는다. 사용자 Journal은 ‘사용자가 해당 내용을 기록했다’는 근거이지 현재 시장 사실이나 투자 타당성의 외부 근거가 아니다. 외부 자료에는 해당하는 게시/유효 시각, 출처·라이선스·신선도 판정을 기록한다. 모델 해석은 근거 ID를 참조하지만 Source Evidence로 승격하지 않는다.
- Finance는 이 typed extension을 별도 Product 계약으로 정의·버전 관리하고 Core Evidence의 `domain_extension_ref`로 연결한다. 이 제안은 Shared AI Core Evidence envelope를 확장하거나 wire DTO를 확정하지 않는다.
- Provider로 전송할 수 있는 정확한 Journal 필드 목록은 User Data Policy §§8.2, 8.5 검토와 Owner 승인 뒤에만 정한다. 이 제안은 Journal 원문을 외부 Provider에 전송하도록 승인하지 않는다.
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

**B. 실제 Agent MVP:** 허용된 조회 Tool 최소 하나, 기존 사용 가능한 Lens 일부, 정책·근거 기반 구조화 답변, 세션 후속 대화, **사용자가 명시 요청한 장기 Memory의 저장·후속 조회·정정·삭제**를 합성 데이터의 승인된 테스트 경로에서 검증한다. 영구 저장 운영 활성화는 별도 Privacy·Retention·Backup·Audit Gate에 묶으며, 기존 RPL-126/127 Slice에 신규 Memory 구현을 임의 추가하지 않는다.

**C. 개인화 Research:** 사용자가 명시 저장하지 않은 선호를 AI가 추출·저장 제안하는 UX, 다양한 Memory 타입, 사용자 선택형 Portfolio, Market Provider 다중화는 후속 검토한다. **명시적 장기 Memory만 B의 MVP로 이동**하며 Data Policy/Privacy/Release Gate는 그대로 유지한다.

**D. 고급 Agent:** 조건부 전문가/Reviewer, 풍부한 시나리오, 더 많은 Tool·자료원. 품질·비용 측정으로 확장 여부를 판단한다.

**E. 운영:** Provider·보안·출시 Gate, 지속성·관측성·비용 최적화를 실측 기준으로 판단한다.

## 8. 아직 Owner가 별도로 결정해야 하는 것

1. MVP에 붙일 첫 시장 Evidence Source와 라이선스·최신성 정책
2. 운영 Finance Tenant/Authority 발급 근거와 보호된 Tool의 실행 시점 인가
3. 실제 HTTP wire schema·endpoint·timeout·서비스 인증
4. 같은 세션 Context의 위치·TTL·범위·철회/삭제 전파. 명시적 장기 Memory의 저장 허용 항목·데이터 등급·동의/고지·보존·백업·감사·정정·삭제·원본 무효화와 Feature Gate. 자동 장기 대화 원문 저장은 MVP 범위 밖이며 TTL·영구 저장·삭제 동작은 이 제안만으로 승인되지 않는다.
5. Portfolio 정본·계좌연동·개인 맞춤 투자자문 확장 여부와 정책/법무 검토
6. Draft Lens 활성화 여부 및 개별 회귀 검증

위 사항은 이 문서의 존재만으로 승인되지 않는다.

## 9. 정본 배치 및 참고

- [Confluence Finance — Backend·AI 실행 로드맵](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/18153473/Finance+Harness+Backend+AI): 제3자용 상위 제품 목표·개발 단계·현재 증거
- 본 문서: **제안된 Finance Agent 행동과 MVP 완료 기준**. 기존 계약을 대체하지 않음
- [Finance Routing Contract](finance-routing-contract.md), [Finance Lens Catalog](../0-b-lens-infra/finance-lens-catalog.md), [Finance Product Policy](../../service-policy/finance-product-policy.md), [Finance User Data Policy](../../05-product-legal-ops/finance-user-data-policy.md): 현재 의미/정책 정본
- [Shared AI ADR-0001](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0001-skill-lens-workflow-execution-ownership.md), [ADR-0004](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0004-execution-context-api-tenant-isolation.md): 공용 실행 책임 및 Scope 경계
- [Confluence 06. 외부 AI 기술 레퍼런스](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/26902529/06.+AI): Harvard 평가, Google EGM 설계 검수, Upstage 결정적 Tool 패턴의 **도입 후보**, 현재 구현 증거 아님
