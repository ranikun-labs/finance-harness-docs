# Finance Agent MVP v1 — Finance ↔ Shared AI 기술 설계 초안

> 상태: **PROPOSED / NOT_EFFECTIVE / IMPLEMENTATION NOT_RUN** · 설계 리뷰 기준: 2026-11-02
>
> 소유: Finance Product (업무 의미·인가·Memory·Journal) / Shared AI (공통 실행·Evidence 전달). 기존 [Finance Agent 행동 계약 초안](finance-agent-behavior-mvp-proposal.md)의 후속 **구현 준비 설계**다. 본 문서만으로 Jira Scope·코드 Writer·Migration·API/Schema·Production 저장 활성화·PR Merge 권한을 부여하지 않는다.
>
> 정본 우선순위: Finance Service Policy → PolicyGuard / Output Validator / Routing Contract → Shared AI Accepted ADR-0001/0002/0003/0004/0006 + Foundation ADR-0018 → Finance User Data Policy의 내부 기준 → 본 초안. 충돌 시 기존 정책을 유지하고 Owner 결정으로 분리한다.

## 1. F1 — 목표와 MVP 경계

Finance Agent MVP의 사용자 가치 3개를 하나의 좁은 제품 흐름에서 증명한다.

1. **질문 → 승인된 Skill·Lens → 응답:** 기존 Guard / Routing Contract / Fixture를 따르고 Finance Validator를 통과한다.
2. **대화 + 명시적 장기 Memory:** 현재 대화의 관련 맥락을 사용하며 사용자가 “이 기준을 다음에도 기억해줘”라고 명시 요청하면 별도 Product Command로 저장·후속 조회·정정·삭제한다. **일반 발언은 영구 저장하지 않는다.**
3. **원본 사실 확인:** “내가 매수했다”는 주장은 사용자 기록의 존재·내용과 구분해서 검증한다. Journal에 없다고 실제 매수하지 않았다고 단정하지 않는다.

**기본 제품 MVP**(Learn→Checklist→Journal→Review)와 **Agent MVP**는 별도 Gate다. Agent MVP의 구현 시험은 합성 데이터 기반으로 진행할 수 있지만, 영구 Memory의 **Production 저장·조회 활성화는 보존·삭제·백업·감사·고지/동의 등 별도 승인 전 HOLD**다. RAG Vector/Hybrid, 고급 Ranking, 자동 Memory 제안, Multi-Agent, Redis, Graph DB는 필수가 아니다.

## 2. 실제 코드 기준과 물리 경계

- Finance BE의 `CurrentUserPort`, `JournalReadApplicationService`, owner-scoped Journal 조회 경계를 재사용한다. 원본 Journal의 정본은 **Finance DB**이고 매 호출의 actor/owner 인가는 Finance가 수행한다. 기존 Local 인증 Adapter를 Production으로 우회 활성화하지 않는다.
- Shared AI Phase 1은 **Platform Core 동일 JVM의 Kotlin/Spring AI 기반 논리 모듈**, Finance BE는 **별도 Product 프로세스**다. Spring Bean DI는 별도 프로세스 사이를 연결하지 못한다.
- Shared AI의 현재 `ProductToolPort.readFact(ExecutionContext)`와 Fixture Adapter는 **기초 실행 Slice**다. Finance의 운영 Journal Tool/서비스 인증/Memory API가 이미 연결됐다는 뜻이 아니다.
- Finance Markdown/Private Knowledge는 기존 Authoring SoT를 유지한다. 사용자별 Memory나 Journal을 **자동으로 Finance Knowledge Artifact 메타데이터/Vector Corpus로 승격하지 않는다.**

```text
User → Finance BE [Auth / PolicyGuard]
                ├─ Product Router (eligible Skill/Lens, 현재 활성화 Gate)
                ├─ Session Context Provider (요청 범위)
                ├─ Explicit Memory Reader (Product 소유, 최신 인가)
                └─ SelectedJournalRead (Product DB, 조회 시 재인가)
                      │ 승인된 최소 Business Input + Trusted Execution Context
                      ▼
          Platform Core / Shared AI [실행 / Tool allowlist / Model / Evidence]
                      │ model result + source refs
                      ▼
          Finance BE [Evidence admissibility + Output Validator] → Response

Explicit "기억해줘" → Finance Product Command [정책/동의/인가]
                      → Product Memory Store [독립적인 데이터 Lifecycle]
* 모델 생성 Tool 인수는 저장 권한이 아니며 범위를 확대할 수 없다.
```

## 3. 계약과 책임 — 신규 Port는 설계 후보

| 경계 | 책임 | 현실·결정 |
| --- | --- | --- |
| `FinanceAgentApplicationService` (논리 후보) | Guard → Skill/Lens → Context 준비 → Shared AI 호출 → 검증 | Finance 소유 YOU-CORE. 현재 실제 클래스 존재 주장 금지 |
| `SelectedJournalRead` (기존 PR #20 후보) | 사용자 선택 Journal만 현재 권한으로 읽기 | 기존 Read Service/owner predicate 재사용, 정확한 wire 미확정 |
| `ConversationContextProvider` | 세션의 관련 대화 최소 내용 제공·철회 | Product-owned, retention/TTL·세션 귀속 미확정 |
| `ExplicitMemoryCommandPort` | 명시적인 저장·정정·삭제 요청을 Finance 정책으로 처리 | **Product write**; Shared AI의 임의 LLM Tool 권한 없음 |
| `ExplicitMemoryQueryPort` | 현재 actor/scope에서 유효한 Memory만 조회 | Memory는 Evidence의 원본 사실이 아님 |
| `SharedAiExecuteClient` | 신뢰된 Context + 승인된 Skill/Tool 실행 요청 전달 | Finance→Platform HTTP/JSON 등 승인된 transport, wire/서비스 Auth 미확정 |
| Evidence/Output Validator | source refs와 Product 의미·안전·불확실성 검증 | ADR-0002 + 기존 Finance Validator 우선 |

**중요:** 위 이름은 설계 검토용 Port 후보이며 구현 경로·공개 API·DTO/Enum·Spring AI 버전을 확정하는 선언이 아니다. 공유 JSON은 Core Evidence와 Product typed extension을 분리한다. `domain_metadata` catch-all을 도입하지 않는다.

## 4. 세 흐름의 요청·응답 의미

### 4.1 질문 / 다중 턴 (Read)

1. Finance Backend는 인증된 현재 actor·Product·허용 Resource를 확정하고 Input Guard를 먼저 실행한다.
2. 위험 Intent는 기존 Redirect/Refusal 경로. SAFE에 한해 활성 Lens 1~3개(기존 Fixture/조합 규칙), 승인된 Tool allowlist를 결정한다.
3. 같은 세션 Context는 **관련 최소 내용**만 사용한다. 사용자가 명시 저장한 Memory가 있다면 별도로 현재 인가·삭제 상태·사용 허용 목적을 확인하여 선택적으로 주입한다.
4. 정확한 Journal 사실값은 Finance Product의 현재 DB/Tool에서 조회한다. 대화 원문·Memory·RAG 요약을 매수 사실의 정본으로 취급하지 않는다.
5. Shared AI는 Scope 비확대·Allowlist·타임아웃·Evidence reference integrity를 검사하고 실행한다. Provider에 보낼 필드는 기존 User Data Policy §8 및 별도 승인된 allowlist를 따른다.
6. Finance는 Evidence sufficiency, 과거/현재 시점, 사용자 입력 vs 외부 검증 자료 구분, 정책/Output Validator를 검사하고 응답한다.

**결과:** 기존 Shared AI Execution Model의 `SUCCESS`, `ABSTAIN/UNKNOWN`, `REJECTED`, `FAILED` 구분을 준수한다. RAG/Memory가 없으면 가능한 승인된 Product 데이터만으로 축소 응답하거나 근거 부족을 표시한다. Unauthorized는 무근거/빈 검색과 구분하여 안전하게 처리하되 ID 존재 여부 노출 금지.

### 4.2 “기억해줘” (Write)

1. Product가 **명시적인 사용자 저장 의도**인지 구분한다. 일반적 진술/위험 Intent 분류 로그/LLM 추론으로 자동 저장하지 않는다.
2. 저장 문구는 Finance가 허용한 제한적 선호·검토 기준에 해당해야 한다. 매수/수량·계좌 사실, 금융 행동 위험 프로파일은 저장 대상으로 확정하지 않는다.
3. 현재 Principal·Memory 저장 활성 Feature Gate·허용 목적·동의/고지·보존 정책·해당 요청의 Actor Scope를 검증한다.
4. Finance Application Service가 **명시적 쓰기 Command**를 처리한다. 응답 유실·중복 전송에 대한 요청 단위 멱등성, 동일 Key의 다른 Payload 거절, 동시 갱신 시 버전 충돌 방지가 필요하다.
5. 저장 성공은 **DB Transaction Commit이 확인된 경우**에만 반환한다. 모델의 “기억했어” 문장을 저장 성공 증거로 사용하지 않는다. 재시도·클라이언트 Disconnect 시 상태를 재확인한다.

**기본 Gate:** 영구 Memory 저장 정책 미승인 또는 Runtime 기능 OFF이면 안전하게 거절한다. 테스트 Fixture의 저장 성공은 Production 저장 승인으로 승격할 수 없다.

### 4.3 “그 기억 삭제해줘” / 원본 Journal 삭제

- 사용자는 **본인 Memory**를 조회하고 수정·삭제할 수 있어야 한다. 단순 모호한 대명사 기반 DELETE는 명확한 대상 확인 후 처리한다.
- 삭제 확정 후 Query에서 **즉시 조회 부적격**으로 처리한다. 파생 Prompt/Session Context, RAG Projection, Cache도 더는 재사용하면 안 된다. 비동기 물리 정리와 백업 만료/복구 증빙은 별개다.
- Memory가 특정 Journal을 근거로 삼았다면 **Source ID/Version 관계**로 무효화를 추적한다. Journal 삭제/권한 철회 직후 인덱스 이벤트가 지연되더라도 최신 Product 검증으로 재노출을 차단한다.
- **Stale Callback/Race:** Memory 삭제와 과거 저장 명령 완료가 경합하는 경우, 이전 요청이 삭제 이후 기억을 되살리지 못해야 한다. 버전·Tombstone·현재 상태 비교/Transaction 경계는 구현 전 결정한다.
- Repeated DELETE, Soft/Hard 삭제, 삭제 응답/복구 시점, Backup/Retention 수치는 Owner 승인 전 임의로 확정하지 않는다.

### 4.4 권한·동시성·철회 불변식 (논리 계약 후보)

- **Command origin:** typed Write/Update/Delete/Withdraw Command는 명시적 현재 사용자 의도를 담은 인증 Finance 애플리케이션 요청에서만 생성될 수 있다. Actor와 Tenant는 신뢰된 Finance `CurrentUserPort`/Authority Source에서 가져온다. Prompt·LLM·Tool·검색 결과가 제시한 Actor, Tenant, Resource, 승인 또는 완성된 command payload는 신뢰하지 않는다. 모델이 반환한 내용은 검증 전까지 입력 데이터일 뿐이다.
- **Application authorization:** Finance 애플리케이션은 대상·허용 범위·정책·필요한 사용자 확인을 검증하고, 각 쓰기 시점에 현재 Actor/Resource 권한과 동의 상태를 재확인한다. 확인·인가가 불가능하거나 상태가 바뀌면 쓰기를 거부한다. Shared AI에는 Finance 데이터 쓰기 권한을 부여하지 않는다.
- **Expected version:** 신규 생성은 대상이 아직 유효한 Memory로 존재하지 않는다는 기대 조건을 검증한다. 수정·삭제·철회는 요청이 관찰한 논리 버전에 대한 조건부 compare-and-set를 요구한다. 버전/존재 조건 불일치는 stale command로 보고 fail-closed 처리한다. 실제 필드, DDL, 잠금 또는 저장소 알고리즘은 확정하지 않는다.
- **Revocation generation:** 동의 철회·삭제는 관련 논리 revocation generation을 단조 증가시키고 무효화/tombstone 경계와 결속한다. generation의 scope와 물리적 원자성 방법은 Owner 결정으로 남긴다. 이전 version/generation을 가진 지연 작업·재시도·콜백은 쓰기 전에 현재 상태와 다시 대조하고, 이미 철회되었거나 삭제된 Memory를 되살리지 못하게 거부한다.
- **Read/exposure:** 모든 조회는 현재 요청 Actor/Resource, 동의·철회·삭제 상태를 재인가한다. 결과 반환 직전에 버전과 권한·동의·revocation generation을 마지막으로 확인한다. 불일치·조회 실패는 출력 노출 없이 fail-closed다.
- **Streaming:** Memory가 영향을 준 응답은 첫 바이트 전에 최종 검증을 완료할 수 있도록 버퍼링한다. 스트리밍 중 철회를 막거나 첫 바이트 전 검증할 수 없는 경로에서는 Memory 의존 스트리밍을 지원하지 않고 결과를 폐기한다. 이미 보낸 바이트를 회수할 수 있다는 보장은 하지 않는다.

이는 요구되는 안전 결과를 정의하는 논리 불변식이다. 구체적인 API/DTO, 동의 UX, generation key, tombstone 저장, 트랜잭션 순서, retry policy, retention·backup·audit 항목은 Owner 승인 없이 확정하지 않는다.

## 5. Logical Memory Data Model — 구현 전 구체화가 필요한 초안

| 논리 항목 | 의미 / 제약 |
| --- | --- |
| `memory_ref` | Finance 범위에서 유일한 불투명 식별자. 타인 ID로 존재 여부 추측 금지 |
| `actor_scope_ref` | 현재 Principal/Finance Product 범위의 Owner 격리 |
| `kind` | 명시 선호·검토 기준 등 **승인된 최소 유형**. 전체 AI 성향 프로파일링 금지 |
| `content` | 사용자가 직접 요청한 정규화된 최소 내용. 원문 대화 덤프 금지 |
| `origin` | `EXPLICIT_USER_COMMAND` 등의 출처 구분(후보), AI 해석을 사실로 승격 금지 |
| `source_refs` | 원본 Journal과 관계가 있을 때 참조·버전. 없으면 임의 생성 금지 |
| `consent_policy_ref` | 유효한 고지/동의·처리 목적·보존 정책에 연결(실제 식별 체계 미확정) |
| `state` / `version` | 활성/조회 차단/삭제 및 동시 수정·Stale Callback 방지 |
| `created_at` / `updated_at` | 생성·수정 감사에 필요한 최소 시각, 내용 원문 로그 금지 |
| `retention_ref` | 승인된 정책으로부터 도출. 기간·법적 근거 임의 기재 금지 |

- **저장 후보:** Finance-owned PostgreSQL의 별도 사용자 Memory 저장 모델. 기존 Finance Knowledge Markdown의 SoT를 PostgreSQL로 전환하는 결정이 아니다.
- **먼저 확정:** 사용자 데이터 분류, 보존기간, 정정 의미(수정 vs 새 버전), 고지/동의, 삭제 완료 응답, Backups, 감사 로그 허용 항목, 저장/조회 처리량.
- **물리 데이터 모델:** 실제 테이블·DDL·인덱스·Constraint/Unique Scope·Migration·Repository Query/Locking은 Owner Gate 이후 구현 전 확정. 적용된 Migration 수정 금지.
- **멱등성 범위:** Product + Actor + Operation + 요청 Key를 후보로 검토. 중복 요청이 같은 항목을 두 번 생성하지 않는지, 다른 사용자 Key가 충돌하지 않는지, 같은 Key/다른 Payload가 차단되는지 확인한다.

## 6. 반드시 선행할 운영·보안 결정

| Gate | Owner 확인 항목 | 미확정일 때 처리 |
| --- | --- | --- |
| **G1 Product** | 저장 가능한 정보·명시 요청 UX·거부/정정/삭제 의미 | Memory Command 개발 승인 보류 |
| **G2 Privacy/Legal** | 개인화 Memory 데이터 유형/등급, 고지/동의, 사용 목적, retention 기간, 백업·계정 삭제 | Production Persistent Memory OFF |
| **G3 Security** | CurrentUserPort 운영 연결, Actor/Scope isolation, Provider 전송 allowlist, 접근 로그 민감정보 마스킹 | 보호된 Journal Tool/Memory Prod OFF |
| **G4 Platform Contract** | Finance↔Shared AI 서비스 Auth·wire DTO·error/timeout·Correlation·Evidence typed extension | 실사용 HTTP E2E Gate HOLD |
| **G5 Runtime Safety** | 기존 Guard 순서, 조회 시 재인가, Version mismatch, Memory 삭제/철회, Context 요청 크기·Tool budget | Agent Production 활성화 보류 |
| **G6 Verification** | 합성 데이터의 실제 Product/Platform E2E, 독립 Reviewer, Release Combined Tree와 운영 점검 | 기능 출시 주장 금지 |

**Policy 충돌 유의:** Finance User Data Policy P-08은 로그 기반 사용자 프로파일링을 금지한다. 본 설계의 **사용자 명시 저장 선호**가 허용 가능한 새로운 사용자 데이터 항목인지, Profile과 어떻게 구분하고 고지·관리할지는 **Product/Privacy Owner Decision**이 필요하다. 기존 P-08을 본 제안으로 완화하지 않는다.

### 6.1 검증 및 출시 Gate — 순서와 현재 증거

| 순서 | Gate | 요구되는 결과 | 현재 상태 |
| --- | --- | --- | --- |
| 1 | Docs 독립 검토 | 최신 고정 HEAD에서 M1/M2/M3/m1 정책·계약 검토 및 Finding Closure | PR #20의 문서 검수 상태는 고정 HEAD별 Review 기록에서 확인한다. Review는 검수한 SHA와 문서 범위에만 적용되며 Runtime 검증이나 Release 승인이 아니다 |
| 2 | 합성 결정적 E2E | 합성 Context와 고정 Fake Model을 쓰는 비운영 Finance↔Shared AI 계약 검증 | General Analysis bridge에 한해 L2/L3 PASS: [Shared AI PR #40 Actions #41758867244](https://github.com/ranikun-labs/platform-services/actions/runs/41758867244), Finance source `d8fae6be7b497cf5afbe153a06bb19214d67ec35`, Shared AI source `31f68b5437bee8de889b9e704db0a35d57045ffd`. 별도 Finance/Platform JVM, 실제 HTTP; `test` profile, synthetic Context/Fake Model, local test auth disabled. Memory 기능 검증은 NOT_RUN |
| 3 | Production Auth/Security | 운영 대상 인증·인가·Actor/Tenant/Resource 격리, 로그·권한·보안 경계의 실환경 검증 | NOT_RUN. 합성 Identity·Local fixture 결과로 대체 불가 |
| 4 | Memory Release/Privacy | 허용 필드·분류·고지/동의·목적·Retention/만료·삭제·backup/restore·audit·Provider 전송에 대한 승인 및 릴리스 결정 | NOT_APPROVED. Production Memory Read/Write OFF |

Finance PR #12 Actions #41754777853은 해당 Finance SHA의 Native test PASS를 보인다. Shared AI PR #40의 위 Actions는 이 설계의 Memory Read/Write, Production Auth, live Provider/LLM 실행 또는 외부 사용자 E2E를 검증하지 않는다. 기존 검증은 정확한 PR source 조합과 General Analysis bridge 범위에서만 재사용한다.

## 7. 검증 계약 — 최소 Golden Set (전부 아직 NOT_RUN)

| 분류 | Given | 기대 |
| --- | --- | --- |
| Skill/Guard | 위험 Intent가 안전한 Lens 표현에 섞임 | Guard 우선, 허용 없는 Tool 실행 0 |
| Skill/Registry | 문서상 active이나 Runtime 비활성 Lens | 자동 실행하지 않음 |
| Memory Read | 같은 대화의 “나는 ~하는 편” | 현재 요청에서만 반영, 영구 생성 0 |
| Memory Write | “다음에도 기억해줘” + 승인·Feature ON | Product Command 1건, 재조회 가능 |
| Memory Write | 같은 요청 중복/응답 유실 | 이중 저장 없음, 결과 재조회 가능 |
| Memory Write | 같은 Key + 다른 내용 / 다른 사용자 같은 Key | Scope 격리, 충돌 거부 |
| Memory Gate | Policy/Retention 승인 없거나 Feature OFF | Production 영구 저장 0 |
| Memory Race | DELETE 중 기존 저장 재시도·지연 Callback | 삭제된 Memory 재생성 0 |
| Memory Update | 다른 요청의 동시 정정 | Version 충돌 처리 및 최신 상태 보존 |
| Evidence | 사용자가 “매수했다”라고 말함, Journal에 기록 없음 | ‘매수 확인 불가’, ‘매수 안 함’ 단정 금지 |
| Evidence | Journal 저장 사실 vs 시장 현재가 | Source 종류·기준 시각 구분 |
| Authorization | A의 Memory ID/Journal ID로 B가 조회 | 0건 노출, 존재 여부 누출 금지 |
| Authorization | 삭제·권한 철회 직후 오래된 Cache/Index 참조 | 즉시 조회 차단(물리 정리 별도) |
| Partial Failure | Memory Store/Retriever 실패, Journal Tool 성공 | 승인된 축소 응답 또는 FAILED/UNKNOWN 계약 준수 |
| Tool Failure | Provider Timeout/도중 취소/반환 Scope 확장 | Fail closed, 안전한 Retry만 |
| Observability | 일반·에러·감사 로그 | 민감 원문 로그 0, 실제 실행/비용/근거 Ref 검증 |

**검증 수준:** 정적 Source/문서 검토 ≠ 컴파일 테스트 ≠ 통합 HTTP E2E ≠ Production Auth/Policy E2E. 각 단계의 Commit/SHA·명령·CI 결과·Actor Scope·Evidence는 별도 기록한다. 비용·Latency 목표값은 실측과 Owner 계약 없이 고정하지 않는다.

## 8. 구현 순서 및 R&R

1. **YOU-CORE / Finance Owner:** Product Privacy·Memory Lifecycle·확정/미확정 UX, Invariant, Transaction/Locking·Read Predicate·Actor Isolation 설계. Product Tool/Memory Command의 의미를 승인.
2. **AI-WRITE / 승인된 기존 Writer:** 승인된 DTO/Mapper/Controller/Adapter·Repository Wiring, 구조화된 Validator와 Test Fixture, 컴파일/테스트·기계적 수정. 새로운 Owner Decision을 코드로 대신 확정하지 않음.
3. **Shared AI Owner/Writer:** 기존 Port·Execution Scope·Tool Allowlist·Evidence Correlation 기반으로 Product가 승인한 minimal inbound/outbound bridge만 구현. Finance DB 직접 접근 없음.
4. **독립 Reviewer READ-ONLY:** 정책 Drift, HTTP 호환성, IDOR/권한 재조회, Save/Delete Race, 중복/응답 유실, Provider 분리, 장애/Trace/Evidence 및 CI Delta 검증.
5. **Owner Merge/Release Gate:** 문서 Draft의 Accepted 승격, Repo/PR Source Merge, Schema Freeze, Production Feature ON은 각각 별도 승인.

**금지:** 신규 Agent Runtime·Vector/Graph DB·Redis를 MVP 선행 필수로 만들기, LLM의 자유 장기 기억 쓰기, Runtime으로 Finance Data SoT 이전, User 행동 로그 프로파일링, Apply된 Migration 수정, 증거 없는 Source/CI/Prod PASS 주장.

## 9. 결정할 항목 — Owner가 지금 당장 선택할 것과 후속 작업

**제품 방향 합의(이미 완료, 미구현):** 3기능 MVP 목표, 일반 진술은 단기 Context, 명시 명령만 장기 저장, 원본 사실/인가 우선, 자동 저장 제안은 V2.

**Owner 추가 결정 필수:** Memory 허용 데이터의 최소 분류 및 P-08 경계; 별도 고지/동의·보존/삭제/백업/감사; 정정/DELETE의 제품적 의미; 장애 시 사용자 표시/기능 비활성화의 의미. 세부 기간/값은 미정 유지.

**엔지니어링 결정(Owner 제품 계약 후):** 실제 API DTO/Wire/Version, actor·scope source, Memory read/write consistency 및 idempotency scope, Session 저장 방식/TTL, LLM 전송 allowlist, Evidence typed extension과 Golden Set. 코드 증거 없이는 기존 구현 완료로 표기 금지.

## 10. 관련 문서

- [Finance Agent Behavior Proposal](finance-agent-behavior-mvp-proposal.md) — MVP 사용자 경험과 기존 PolicyGuard/Tool 계약
- [Finance User Data Policy](../../05-product-legal-ops/finance-user-data-policy.md) — 데이터/Retention/Privacy Gate
- [Finance Routing Contract](finance-routing-contract.md) — Guard 선행·Risk Redirect·Lens 조합
- [Shared AI ADR-0001](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0001-skill-lens-workflow-execution-ownership.md) — Product 결정 / Runtime 실행
- [Shared AI ADR-0002](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0002-evidence-provenance-core-domain-boundary.md) — Evidence / Provenance
- [Shared AI ADR-0003](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0003-retrieval-knowledge-runtime-ownership-lifecycle.md) — 원본, Projection, Scope, Invalidation
- [Shared AI ADR-0004](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0004-execution-context-api-tenant-isolation.md) — Trusted Scope와 Fail-closed
- [Shared AI Confluence](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/458754/Shared+AI) · [Finance Agent Roadmap](https://ranikunlabs.atlassian.net/wiki/spaces/RLAB/pages/18153473/Finance+Harness+Backend+AI)
