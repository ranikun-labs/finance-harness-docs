# Finance Knowledge DB Implementation Handoff

## 0. Metadata

```text
status            : deferred implementation handoff
document type     : Future Implementation Handoff
                    (미래 구현자를 위한 실행 순서·검증 계약 — 구현 착수 문서 아님)
path              : 00-core/0-d-knowledge-architecture/
                    finance-knowledge-db-implementation-handoff.md

meaning :
  - 상위 아키텍처는 존재한다.
  - 실제 구현 착수는 아직 승인되지 않았다.
  - 도입 트리거와 구현 시작 조건이 충족되기 전에는 실행하지 않는다.
  - 본 문서는 미래 구현자를 위한 실행 순서와 검증 계약이다.

parent decisions  :
  - finance-knowledge-storage-decision.md        (decided — 저장 결정)
  - finance-document-exposure-policy.md          (decided — 노출 정책)
  - finance-knowledge-db-future-architecture.md  (proposed — 미래 목표 구조)
reference         :
  - finance-routing-contract.md                  (Runtime 실행 계약)
audience          : 향후 구현을 수행할 Claude Code / Codex / 사람 구현자
non-goals         :
  - 지금 PostgreSQL을 도입하는 것
  - DDL / CREATE TABLE / column / index / constraint / migration SQL
  - ingestion·Runtime DB reader·API endpoint 코드
  - RBAC/RLS 정책, cache/broker/cloud 제품 선택
  - Finance canonical 의미(Lens/PolicyGuard/Routing/Fixture/Validator) 수정
```

**경고 — 이 문서의 존재는 승인이 아니다.**

```text
이 문서가 존재한다는 사실은
PostgreSQL 도입 승인을 의미하지 않는다.

구현 시작 조건이 충족되지 않았다면
구현자는 작업을 중단하고 현재 MVP 구조를 유지해야 한다.
```

**상태 어휘** — 본 문서의 모든 항목은 다음 중 하나로 표시된다.

```text
- inherited contract            (상위 문서에서 확정 — 재검토 금지)
- implementation prerequisite   (구현 착수 전 반드시 충족할 선행 조건)
- proposed implementation step  (합의된 실행 순서 — 아직 수행 안 됨)
- verification required         (실제 repo 확인 전까지 판단 보류)
- deferred                      (본 문서에서 결정하지 않고 후속으로 미룸)
- not implemented               (존재하지 않음 — 존재한다고 가정 금지)
```

결정 완료와 구현 완료를 동일하게 취급하지 않는다.

---

## 1. 문서 목적

본 문서는 향후 구현 시작 조건이 충족됐을 때, 구현자(Claude Code / Codex / 사람)가 **무엇을 어떤 순서로 확인·설계·구현·검증해야 하는지** 전달하는 Implementation Handoff다.

0-D Knowledge Architecture 문서 4종의 역할 분담:

```text
finance-knowledge-storage-decision.md
= 현재 저장 결정 (Markdown + WAS private resource, DB 미도입)

finance-document-exposure-policy.md
= 노출 정책 (exposure_class 3종, distribution_boundary, Runtime output_class, operational-metadata 및 packaging/API/로그/Admin/LLM 전송 경계)

finance-knowledge-db-future-architecture.md
= 미래 목표 구조 (소유권, Projection, 상태 축 분리, Rollout)

finance-knowledge-db-implementation-handoff.md   ← 본 문서
= 실제 구현 조건 충족 후 실행할
  검증·설계·구현·테스트·롤아웃 순서
```

본 문서가 하는 것과 하지 않는 것:

| 하는 것 | 하지 않는 것 |
|---|---|
| Stop/Go Gate와 증거 기준 정의 | 도입 여부 결정 (상위 ADR 트리거 소유) |
| repo 검증 체크리스트 제공 | repo 현재 상태 추측 |
| 구현 불변식과 위반 위험 명시 | 상위 결론의 재설계 |
| Phase별 진입·종료·중단 조건 정의 | 코드·DDL·schema·migration 작성 |
| 테스트·관측·Runbook 요구사항 정의 | 테스트 코드 작성 |

---

## 2. Current Source of Truth

### 2.1 구현자가 먼저 읽어야 할 읽기 순서 (고정)

```text
1. finance-knowledge-storage-decision.md
2. finance-document-exposure-policy.md
3. finance-knowledge-artifact-metadata-standard.md
4. finance-lens-authoring-standard.md
5. finance-knowledge-db-future-architecture.md
6. 00-service-policy.md
7. finance-policyguard-rules.md
8. finance-output-validator.md
9. finance-routing-contract.md
10. finance-routing-fixtures.md
```

`finance-lens-authoring-standard.md`는 Lens 의미를 재정의하기 위한 문서가 아니라, Metadata Standard와 Lens extension의 책임 경계를 확인하기 위한 읽기 대상이다. 실제 구현 상태의 증거는 §4 Preflight에서 확인하는 repository와 runtime configuration뿐이다.

### 2.2 각 Source의 역할

```text
storage-decision
= 현재 저장 방식과 DB 도입 트리거

exposure-policy
= 원문·API·Frontend·로그·Admin·LLM 전송 경계

db-future-architecture
= 미래 소유권·Projection·상태 축 분리·Rollout 구조

metadata-standard
= 공통 Metadata envelope와 lifecycle_status 소유

lens-authoring-standard
= 공통 envelope 상속과 Lens extension 책임 경계 확인

policyguard / output-validator / routing-contract / routing-fixtures
= Finance canonical runtime 의미와 regression 기준. 본 handoff는 이를 재정의하지 않고 호출·검증 결과만 확인한다.

actual repository
= 실제 구현 상태의 유일한 증거
```

### 2.3 문서와 구현의 구분 (경고)

- **문서 계획이나 본 handoff에 적힌 내용을 실제 구현 완료로 간주하지 않는다.** 위 1~4번 문서는 계약·계획이며, 구현 상태의 증거는 5번(실제 repository)뿐이다.
- 상위 문서에 "MVP target contract"로 표시된 구조(Markdown → WAS private → Runtime → Sanitized Response)조차 **실제 packaging·loader·sanitization 구현 여부는 repo 검증 대상**이다 (future-architecture §3.1 계승).
- 존재 여부를 확인하지 않은 파일·모듈·테이블·설정을 있다고 쓰지 않는다. 확인 전에는 `verification required`다.

---

## 3. 구현 착수 Stop/Go Gate

구현자의 첫 행동은 코드가 아니라 **이 Gate의 판정**이다. (implementation prerequisite)

### 3.1 STOP 조건 — 하나라도 해당하면 구현하지 않는다

```text
- PostgreSQL 도입 트리거가 실제 운영 문제로 확인되지 않음
- 파일 기반 방식의 한계가 측정되지 않음
- 모든 Knowledge Artifact의 metadata 기준이 불안정함
- Exposure classification 적용이 완료되지 않음
- dependency / fixture relation이 정리되지 않음
- Validation Pipeline 요구사항이 합의되지 않음
- rollback 요구사항이 미확정
- audit 요구사항이 미확정
- 운영 책임자와 승인 주체가 정해지지 않음
- 현재 repo 구현 상태가 검증되지 않음
```

- STOP 판정 시의 행동은 하나다: **작업 중단, 현재 MVP 구조 유지, 미충족 항목을 기록해 반환.** "일단 Phase 1만 먼저" 같은 부분 착수도 하지 않는다 (단, Phase 1의 검증 강화가 MVP 자체 개선으로 별도 승인된 경우는 그 승인 범위에서만 수행).

### 3.2 GO 조건 — 전부 증거와 함께 확인되어야 한다

| Gate | 필요한 증거 | 현재 상태 | 확인 주체 | 미충족 시 행동 |
|---|---|---|---|---|
| G-01 실제 발생한 도입 트리거 | 상위 ADR §11 트리거 중 실제 발생한 항목의 운영 기록 (장애·병목 티켓, 지표) | verification required | 운영 책임자 | STOP — MVP 유지 |
| G-02 측정된 현재 문제 | 파일 기반 방식 한계의 측정값 (배포 소요, 불일치 빈도 등 — 체감 아님) | verification required | 운영 책임자 | STOP — 측정 먼저 수행 |
| G-03 승인된 구현 범위 | 착수 Phase 범위와 승인 기록 | verification required | 승인 주체 | STOP — 승인 대기 |
| G-04 안정화된 metadata contract | 전체 artifact metadata 기준 문서와 적용 증거 | verification required | 아키텍처 소유자 | STOP — 기준 안정화 선행 |
| G-05 전체 artifact exposure classification | 모든 문서의 등급 지정 완료 증거 (Exposure Policy §4 기준) | verification required | 아키텍처 소유자 | STOP — 분류 완료 선행 |
| G-06 dependency closure 기준 | dependency·fixture relation 정리 결과물 | verification required | 아키텍처 소유자 | STOP — 관계 정리 선행 |
| G-07 Projection Release Unit 기준 | §9 결정 Gate의 질문에 대한 답과 승인 기록 | verification required | 아키텍처 소유자 + 운영 책임자 | STOP — §9 Gate 통과 선행 |
| G-08 rollback 정책 | rollback window·절차 확정 문서 | verification required | 운영 책임자 | STOP — 정책 확정 선행 |
| G-09 audit 정책 | audit 필수 수준(법적·계약 포함)과 durable buffer 요구 확정 | verification required | 운영 책임자 + 승인 주체 | STOP — 정책 확정 선행 |
| G-10 운영 책임자 | 지정 기록 | verification required | 승인 주체 | STOP — 지정 선행 |
| G-11 배포·장애 대응 책임자 | 지정 기록과 on-call 체계 | verification required | 승인 주체 | STOP — 지정 선행 |

- 본 표의 "현재 상태" 열은 실제 repo·운영 기록을 확인하기 전까지 전부 `verification required`로 유지한다. 본 문서 작성 시점에 채워 넣은 값은 없다.
- G-01~G-11이 **전부** 증거와 함께 GO여야 §7 Phase 0 이후로 진행할 수 있다.

---

## 4. Repository Preflight

코드를 수정하기 전에 실제 repository에서 확인해야 할 항목이다. (implementation prerequisite)

- 특정 파일명·모듈명이 존재한다고 추측하지 않는다. 아래는 **확인해야 할 책임과 증거 유형**이며, 실제 위치는 구현자가 repo에서 찾아 기록한다.
- 판정값: `verified present` / `verified absent` / `partially present` / `unknown` / `conflict with contract`
- 실제 repo 확인 전에는 결과를 채워 넣지 않는다. 아래 표의 판정은 전부 `unknown / verification required`다.

| # | 확인 대상 | 확인 목적 | 찾아야 할 증거 | 판정 | 구현 영향 |
|---|---|---|---|---|---|
| P-01 | Finance Markdown의 실제 저장 경로 | Authoring Source 위치 확정 | 문서 디렉터리와 배포 포함 경로 | unknown / verification required | Phase 1 입력 경로 |
| P-02 | WAS private resource packaging 여부 | 상위 계약 준수 확인 | 빌드 산출물 내 문서 포함 방식 | unknown / verification required | Phase 0 판정 A/B/C |
| P-03 | Runtime loader의 실제 존재 여부 | 로딩 계층 파악 | 문서를 읽는 코드 경로 | unknown / verification required | Phase 2 전환 지점 |
| P-04 | Runtime이 Markdown을 언제 로딩하는지 | 로딩 시점 파악 | 초기화/요청 처리 코드 | unknown / verification required | Phase 4 snapshot 설계 |
| P-05 | startup load인지 request-time load인지 | version snapshot 고정 방식 결정 | 로더 호출 시점 | unknown / verification required | I-14 검증 방법 |
| P-06 | 현재 cache 존재 여부 | last-known-good 후보 파악 | 메모리/파일 캐시 코드·설정 | unknown / verification required | §13 폴백 경로, Phase 4 |
| P-07 | 현재 artifact metadata 파싱 여부 | Phase 1 기반 존재 확인 | frontmatter/헤더 파싱 코드 | unknown / verification required | WP-1 신규/보강 판단 |
| P-08 | 현재 content hash/version 존재 여부 | 결정성 기반 확인 | hash 생성·비교 코드 | unknown / verification required | WP-12 신규/보강 판단 |
| P-09 | 현재 dependency validation 존재 여부 | 검증 공백 파악 | 관계 검사 도구·CI 단계 | unknown / verification required | WP-3~5 범위 |
| P-10 | 현재 fixture validation 존재 여부 | 검증 공백 파악 | fixture 대조 테스트 | unknown / verification required | WP-6 범위 |
| P-11 | 현재 exposure validation 존재 여부 | 노출 검증 공백 파악 | bundle/leak 검사 단계 | unknown / verification required | WP-9~10 범위 |
| P-12 | 현재 PolicyGuard 적용 위치 | 실행 순서 계약 확인 | intent 분류·guard 코드 경로 | unknown / verification required | I-10, Phase 0 판정 |
| P-13 | 현재 Output Validator 적용 위치 | 노출 직전 관문 확인 | validator 호출 경로 | unknown / verification required | I-10, Phase 0 판정 |
| P-14 | 현재 sanitized API response 생성 위치 | sanitization 경계 확인 | 응답 조립 코드 | unknown / verification required | I-11 검증 |
| P-15 | raw Markdown 조회 API 존재 여부 | 계약 위반 탐지 | route/endpoint 목록 | unknown / verification required | 발견 시 conflict — §7.3 |
| P-16 | Frontend/static bundle 혼입 가능성 | packaging 경계 확인 | 빌드 설정, 산출물 검사 | unknown / verification required | 발견 시 conflict — §7.3 |
| P-17 | 로그에 Prompt/Markdown이 기록되는지 | redaction 확인 | 로깅 코드·샘플 로그 | unknown / verification required | 발견 시 conflict — §7.3 |
| P-18 | debug/admin endpoint 존재 여부 | 원문 노출 경로 확인 | 관리 route, 비활성화 설정 | unknown / verification required | 발견 시 conflict — §7.3 |
| P-19 | PostgreSQL client/config/migration 체계 | Phase 3 기반 파악 | DB 클라이언트·설정·기존 migration 방식 | unknown / verification required | Phase 3 착수 방식 |
| P-20 | 현재 배포 방식과 다중 WAS 구조 | 일관성 요구 수준 파악 | 배포 파이프라인, 인스턴스 수 | unknown / verification required | Phase 4~5 설계 입력 |
| P-21 | 현재 health/readiness/metrics 체계 | 관측 기반 파악 | probe·지표 endpoint | unknown / verification required | §17 지표 연결 |
| P-22 | 현재 rollback 방식 | 기존 복구 수단 파악 | 배포 롤백 절차 | unknown / verification required | Phase 5 rollback 연결 |
| P-23 | 현재 request/response trace 구조 | provenance 연결점 파악 | trace id 생성·전파 코드 | unknown / verification required | §12(Phase 4) provenance |

- Preflight 완료 전에는 어떤 Phase의 구현 작업도 시작하지 않는다.
- `conflict with contract` 판정이 하나라도 나오면 §7.3의 규칙(충돌 우선 수정)을 따른다.

---

## 5. 변경 불가 구현 불변식

어떤 기술 선택을 하더라도 다음 불변식을 지킨다. 전부 `inherited contract`이며 구현자가 재협상할 수 없다.

| ID | 불변식 | 근거 상위 문서 | 구현 시 검증 방법 | 위반 시 위험 |
|---|---|---|---|---|
| I-01 | Markdown이 의미의 Source of Truth다 | storage-decision §6, future-arch §5.1 | 의미 변경이 Markdown diff 없이 발생하는 경로가 없는지 코드 리뷰 | 의미·실행 상태 분리, 감사 불능 |
| I-02 | PostgreSQL row를 사람이 직접 의미 편집하지 않는다 | future-arch §5.3 | projection 쓰기 경로가 build pipeline뿐인지 확인, 수동 update 경로 부재 테스트 | 이중 Source of Truth 발생 |
| I-03 | 동일 artifact 의미를 Markdown과 DB 양쪽에서 독립 수정하지 않는다 | storage-decision §4·§12, future-arch §5.3 | authoritative field 소유 매핑 검토 (의미=MD, 운영=DB) | 어느 쪽이 최신인지 판단 불능 |
| I-04 | Projection은 Validation을 통과한 source에서만 생성한다 | future-arch §8 | 미검증 입력의 build 차단 테스트 (T-계열) | 오염된 실행 데이터 서빙 |
| I-05 | Projection version은 immutable하다 | future-arch §9.1 | 생성 후 내용 변경 시도 차단 테스트, hash 재검증 | 버전 계보·rollback 신뢰 붕괴 |
| I-06 | active projection을 in-place 수정하지 않는다 | future-arch §9.1 | active 대상 update 경로 부재 확인 | 서빙 중 데이터 불일치 |
| I-07 | activation/rollback/disable은 상태 전환이다 | future-arch §10 | 상태 변경이 pointer/state 전이만 수행하는지 확인 | 긴급 수정 명목의 의미 오염 |
| I-08 | draft/candidate를 일반 사용자 요청에 사용하지 않는다 | future-arch §7.3·§11.1 | Runtime 조회 필터가 active(+제한 canary)만 허용하는지 테스트 | 미검증 콘텐츠 노출 |
| I-09 | raw Markdown을 장애 폴백으로 즉석 파싱하지 않는다 | future-arch §14.3 | 폴백 경로 코드 검사, DB 장애 주입 테스트 | 검증 파이프라인 우회 뒷문 |
| I-10 | PolicyGuard와 Output Validator를 우회하지 않는다 | routing-contract §2·§9, future-arch §14.1 | 모든 응답 경로의 관문 통과 계약 테스트 | 금지 콘텐츠 노출 (안전 사고) |
| I-11 | 사용자에게는 sanitized-runtime-output만 반환한다 | exposure-policy §6·§11 | API snapshot 검사 (내부 필드 부재) | 내부 정보 유출 |
| I-12 | raw-restricted/internal-controlled 원문을 Frontend에 전달하지 않는다 | exposure-policy §4·§5 | bundle/static/CDN leak scan | 정책·red-team 원문 유출 |
| I-13 | Projection 실패 시 기존 검증된 active를 보호한다 | future-arch §9.1·§14 | build 실패 주입 후 서빙 지속 테스트 | 장애가 사용자 장애로 전이 |
| I-14 | 동일 요청에서는 일관된 artifact version 집합을 사용한다 | future-arch §11.1 | 요청 시작 시 version snapshot 고정 확인 | 응답 내 규칙 혼합·재현 불능 |
| I-15 | 부분 Release Unit을 active로 승격하지 않는다 | future-arch §8.2 | Unit 일부 실패 시 전체 승격 차단 테스트 | dependency 정합 붕괴 |
| I-16 | Audit를 위해 raw 원문을 로그에 복제하지 않는다 | exposure-policy §8, future-arch §12.3 | 로그 스캔 (Markdown/Prompt/secret 부재) | 노출 표면 증가 |

---

## 6. 전체 구현 단계

future-architecture §16의 Phase를 그대로 계승한다. (inherited contract — 단계 구성 / proposed implementation step — 각 단계 실행 내용)

```text
Phase 0 — Baseline Verification      현재 MVP 구현의 계약 준수 검증
Phase 1 — Validation 강화            metadata / dependency / exposure / hash 검증
Phase 2 — File-based Projection      검증된 immutable runtime artifact 생성
Phase 3 — PostgreSQL Registry        version / relationship / deployment state / audit
Phase 4 — Runtime 전환               active Projection 조회
Phase 5 — Rollout 고도화             candidate / canary / rollback / multi-service
```

**Phase 건너뛰기 금지 (inherited contract)**

```text
Phase 1과 Phase 2가 성립하지 않은 상태에서
Phase 3 PostgreSQL Registry를 먼저 구현하지 않는다.

검증되지 않은 Markdown을 그대로 DB에 적재하는 것은
Runtime Projection이 아니라 저장소 이동일 뿐이다.
```

**Promotion Gate 순서 (상위 Gate 명칭 — 기존 ID 대체 아님)**

```text
Global Gate
→ Phase 0
→ Phase 0 완료
→ PG-1
→ Phase 1
→ Phase 1 완료
→ PG-2
→ Phase 2 production deployment
→ Phase 2 완료
→ PG-3
→ Phase 3
```

- PG-1 통과 전 Phase 1을 시작하지 않는다.
- PG-2 통과 전 Phase 2 production deployment를 수행하지 않는다.
- PG-3 통과 전 Phase 3을 시작하지 않는다.
- Phase 0과 Phase 1은 병행하지 않는다. Phase 0 결과와 PG-1 승인 기록이 Phase 1의 입력이다.

**Phase 공통 기술 형식** — §7~§14의 각 Phase는 다음 11개 항목을 동일한 형식으로 기술한다.

```text
목적 / 진입 조건 / repository 선확인 / 구현 작업 / 산출물 종류 /
테스트 요구사항 / 관측성 요구사항 / rollback 방법 /
종료 조건 / 다음 Phase 진입 조건 / 중단 조건
```

- 구체적인 파일명·테이블명은 실제 repo 확인 전에는 만들지 않는다. 산출물은 **종류** 수준으로만 기술한다.

---

## 7. Phase 0 — Baseline Verification

현재 MVP 구현이 상위 계약과 일치하는지 먼저 검증하는 단계다. (proposed implementation step)

### 7.1 Phase 정의

| 항목 | 내용 |
|---|---|
| 목적 | DB 이전에, 현재 MVP가 storage-decision·exposure-policy 계약을 실제로 지키는지 증거로 확인 |
| 진입 조건 | §3 Stop/Go Gate 통과 (G-01~G-11 전부 GO) |
| repository 선확인 | §4 Preflight 전체 (P-01~P-23) 수행 및 판정 기록 |
| 구현 작업 | 코드 변경 없음 — 검증과 판정 기록만. Markdown 위치, WAS private packaging, Frontend/static 비포함, Runtime loader, PolicyGuard/Validator 실제 적용, sanitized response, 로그·오류 redaction, 현재 배포 단위, multi-WAS 여부 확인 |
| 산출물 종류 | Preflight 판정표(P-01~P-23 채움), 계약 준수 판정 보고 (A/B/C 분류) |
| 테스트 요구사항 | leak scan(수동 가능): bundle 내 원문 검색, API 응답 snapshot 검사, 로그 샘플 검사 |
| 관측성 요구사항 | 현재 지표 체계 목록화 (P-21) — 신규 지표 추가는 이후 Phase |
| rollback 방법 | 해당 없음 (읽기 전용 단계) |
| 종료 조건 | 전 항목 판정 완료 + C(충돌) 항목 0건 또는 C 항목의 수정 완료 |
| 다음 Phase 진입 조건 | 판정 결과가 전부 A 또는 (수정 완료된) B, Phase 1 작업 범위 승인 |
| 중단 조건 | Gate 재판정 결과 STOP 전환, 또는 C 항목 수정이 승인되지 않음 |

### 7.2 판정 분류

```text
A. 상위 계약과 일치        — 그대로 진행 가능
B. 부분 구현              — 격차 목록화, Phase 1~2에서 보강
C. 상위 계약과 충돌        — 아래 §7.3 적용
```

### 7.3 충돌 발견 시 규칙 (inherited contract)

- **C(충돌)가 발견되면 DB 구현으로 넘어가지 않는다. 현재 MVP 계약 위반을 먼저 수정한다.**
- 대표 충돌 예: raw Markdown 조회 API 존재(P-15), Frontend bundle 원문 혼입(P-16), Prompt/Markdown 로그 기록(P-17), debug endpoint 원문 노출(P-18).
- 충돌 수정은 Exposure Policy를 기준으로 하며, "DB로 옮기면 해결된다"는 논리로 수정을 미루지 않는다 (storage-decision §10.2 — 저장 매체 변경은 노출 문제를 해결하지 않는다).

---

## 8. Phase 1 — Validation Pipeline

### 8.1 Phase 정의

| 항목 | 내용 |
|---|---|
| 목적 | Projection의 전제인 검증 체계 구축 — metadata / key / dependency / redirect / fixture / alignment / exposure / hash |
| 진입 조건 | Phase 0 종료 조건 충족 |
| repository 선확인 | P-07~P-11 판정 결과 (기존 검증 자산의 존재·범위) — 신규 작성 vs 보강 판단 |
| 구현 작업 | WP-1~WP-12 (§8.2) |
| 산출물 종류 | 검증 도구(빌드/CI 단계), 검증 보고 형식, 실패 기록 형식 |
| 테스트 요구사항 | §15 계층 1(Static validation test) 전체 — 각 WP의 실패 케이스가 실제로 차단되는지 |
| 관측성 요구사항 | validation success/failure 지표, 실패 원인 코드 |
| rollback 방법 | 검증 도구 자체의 롤백은 일반 코드 배포 절차. 검증 도입이 기존 서빙 경로를 변경하지 않으므로 서빙 영향 없음이 원칙 |
| 종료 조건 | 전체 Knowledge Artifact가 WP-1~WP-12를 통과하거나, 실패 항목이 전부 기록·수정됨. 동일 입력의 hash 재현 확인 |
| 다음 Phase 진입 조건 | 종료 조건 + §9 Release Unit 결정 Gate 통과 |
| 중단 조건 | metadata 기준이 검증 도중 흔들려 재정의가 필요해짐 (G-04 재판정), 또는 canonical 문서 의미 수정이 필요해 보이는 경우 (즉시 중단 — 의미 수정은 본 handoff 범위 밖) |

Phase 1 canonical validation source는 다음 세 문서를 명시적으로 포함한다.

```text
- finance-policyguard-rules.md
- finance-routing-fixtures.md
- finance-output-validator.md
```

책임 경계:

```text
PolicyGuard intent/action 의미
= finance-policyguard-rules.md 소유

Routing fixture expected behavior
= finance-routing-fixtures.md 소유

Output Validator pass/rewrite/fail 의미
= finance-output-validator.md 소유
```

0-D Handoff는 위 의미를 재정의하지 않고, 기존 canonical regression을 호출하고 통과 여부만 확인한다.

### 8.2 Work Package

각 WP는 `proposed implementation step`이다.

| WP | 입력 | 출력 | 실패 조건 | 의존 WP | 테스트 항목 | 완료 기준 |
|---|---|---|---|---|---|---|
| WP-1 Metadata validation | 전체 Markdown | 필수 metadata 충족 보고 | 필수 필드 누락·형식 오류 | — | 누락 문서 주입 시 실패 | 전 문서 통과 또는 실패 전수 기록 |
| WP-2 Artifact/lens key uniqueness | WP-1 통과 metadata | key 유일성 보고 | artifact/lens key 중복 | WP-1 | 중복 key 주입 시 실패 | 중복 0건 |
| WP-3 Dependency·dangling reference | 선언된 dependency | 관계 유효성 보고 | 누락 선언, 존재하지 않는 참조 | WP-1, WP-2 | dangling 참조 주입 시 실패 | dangling 0건 |
| WP-4 Cycle validation | WP-3 관계 그래프 | cycle 검사 보고 | 순환 dependency·금지 cycle | WP-3 | 순환 주입 시 실패 | 금지 cycle 0건 |
| WP-5 Redirect target validation | redirect 선언 | 대상 존재 보고 | 존재하지 않는 redirect 대상 | WP-2, WP-3 | 부재 대상 주입 시 실패 | 부재 0건 (redirect 의미는 canonical 소유 — 존재만 검사) |
| WP-6 Fixture reference validation | fixture 연결 선언 | 참조 유효성 보고 | 존재하지 않는 fixture 참조 | WP-2 | 부재 fixture 주입 시 실패 | 부재 0건 (expected behavior 불변) |
| WP-7 PolicyGuard alignment | intent/action/redirect 사용처 | 정합성 보고 | canonical과 불일치 사용 | WP-1~6 | 불일치 사용 주입 시 실패 | 불일치 0건 또는 canonical 세션으로 이관 기록 |
| WP-8 Output Validator reference | validator 체크포인트 연결 | 참조 유효성 보고 | 부재·불일치 참조 | WP-1 | 부재 참조 주입 시 실패 | 부재 0건 (validator 의미 불변) |
| WP-9 Exposure classification | 전체 artifact | 등급 지정 완전성 보고 | 등급 미지정 문서 존재 | WP-1 | 미지정 문서 주입 시 실패 | 전 문서 등급 지정 (등급 체계는 exposure-policy 소유) |
| WP-10 Forbidden public projection | WP-9 결과 + projection 대상 목록 | 혼입 검사 보고 | raw-restricted의 공개 대상 혼입 | WP-9 | 혼입 주입 시 실패 | 혼입 0건 |
| WP-11 Canonical path/provenance | 파일 경로, Git 정보 | 출처 확인 보고 | 경로 불일치, provenance 부재 | WP-1 | 출처 불명 문서 주입 시 실패 | 전 문서 provenance 확인 |
| WP-12 Deterministic content hash | WP-1~11 통과 source | artifact별 content hash | 동일 입력의 hash 불일치 | WP-1~11 | 동일 입력 반복 build hash 비교 | 재현성 100% |

### 8.3 Validation 실패 시 원칙 (inherited contract)

```text
- Projection 생성 금지
- candidate 등록 금지
- 기존 active 유지
- 실패 원인 기록
- 부분 성공분 승격 금지 (원자성 단위는 §9의 Release Unit)
```

---

## 9. Projection Release Unit 결정 Gate

상위 문서(future-architecture §8.2)가 **의도적으로 미확정**으로 남긴 항목이다. 본 문서도 확정하지 않으며, 구현 전 결정 절차만 정의한다. (deferred — 결정 자체 / implementation prerequisite — 결정 행위)

### 9.1 정의 (inherited contract)

```text
Projection Release Unit
= 함께 검증·빌드·배포되는 artifact 집합
+ 그 집합의 transitive dependency closure
```

### 9.2 본 문서가 확정하지 않는 것

```text
- 구체적으로 어떤 Lens를 한 Unit으로 묶는지
- Unit의 실제 식별자
- Unit별 배포 전략
```

### 9.3 구현 전에 답해야 할 질문

| 질문 | 왜 답해야 하는가 |
|---|---|
| 전체 Knowledge Pack 단위인가? | Unit이 하나면 부분 롤백·독립 교체 트리거(G-01)를 충족하지 못할 수 있음 |
| Lens family 단위인가? | family 간 shared dependency 처리 방식이 달라짐 |
| PolicyGuard와 Validator는 공통 Unit인가? | 안전 계층 변경이 전체 재빌드를 요구하는지 결정 |
| redirect 관계는 같은 Unit을 요구하는가? | redirect 대상이 다른 Unit이면 활성 버전 조합 검증이 필요해짐 |
| shared dependency 변경 시 어떤 Unit이 재빌드되는가? | closure 계산 규칙의 핵심 — 미정 시 부분 승격 사고 위험 |
| 독립 Unit의 범위를 어떻게 증명하는가? | "서로 의존하지 않음"을 선언이 아니라 그래프로 증명해야 독립 승격 가능 |

### 9.4 Gate 규칙

- **Release Unit 기준이 결정·승인되기 전에는 Phase 2 immutable projection을 운영 배포하지 않는다.** (Phase 2의 build·검증 개발 자체는 가능하나, 운영 활성화는 Gate 통과 후)
- 결정 결과는 G-07의 증거로 기록한다.

### 9.5 PG-2 — Phase 2 production deployment Promotion Gate

PG-2는 기존 Gate ID를 대체하지 않는 상위 Promotion Gate 명칭이다. PG-2 통과 전에는 Phase 2 production deployment를 수행하지 않는다.

최소 통과 조건:

```text
- Projection Release Unit 검증 완료
- metadata validation 완료
- dependency resolution 완료
- content hash 검증 완료
- PolicyGuard regression 완료
- Routing Fixture regression 완료
- Output Validator regression 완료
- fixture regression 미해결 항목 0건
- rollback target 존재
- canary 진입 조건 충족
```

fixture regression이 미완료이거나 실패 항목이 남아 있으면 PG-2를 통과할 수 없다. PG-2는 기존 fixture expected behavior를 새로 정의하지 않고, canonical fixture regression 결과의 closure만 요구한다.

PG-1은 Phase 0 완료 후 Phase 1 착수를 승인하는 상위 Promotion Gate이며, PG-3은 Phase 2 완료 후 Phase 3 착수를 승인하는 상위 Promotion Gate다.

---

## 10. Phase 2 — File-based Immutable Projection

아직 PostgreSQL을 사용하지 않는 단계다. (proposed implementation step)

### 10.1 Phase 정의

| 항목 | 내용 |
|---|---|
| 목적 | `Private Markdown → Validation → Immutable File Projection → Finance Runtime` 성립. **PostgreSQL 없이도 Validation, 결정성, 불변성, 버전 전환, last-known-good 보호가 성립하는지 먼저 증명** |
| 진입 조건 | Phase 1 종료 + §9 Release Unit Gate 통과 (운영 배포 기준) |
| repository 선확인 | P-03~P-06 (loader·로딩 시점·cache) — projection reader가 대체할 지점 확정 |
| 구현 작업 | validation 통과 source로부터 immutable file projection build, version pointer/manifest 전환 메커니즘, Runtime의 projection reader 전환 |
| 산출물 종류 | artifact manifest, immutable version identity, content hash, validated metadata, dependency graph projection, routing projection, exposure classification, build result, provenance — **논리 산출물 종류만이며 구체적 JSON/YAML/schema는 작성하지 않음 (deferred)** |
| 테스트 요구사항 | §15 계층 2(Projection build test)·4(reader test) — 결정성, 불변성, 중간 산출물 비활성, build 실패 시 기존 유지 |
| 관측성 요구사항 | build success/failure·duration, active projection version/hash 지표 |
| rollback 방법 | version pointer/manifest를 직전 검증 projection으로 전환. projection 내용 수정 금지 |
| 종료 조건 | Runtime이 raw Markdown 직접 파싱 대신 검증된 projection만 사용, 운영 기간 동안 pointer 전환·복구 검증 완료 |
| 다음 Phase 진입 조건 | 종료 조건 + Phase 3 트리거(다중 WAS·rollout 계열)의 실측 확인 + §11 상세 설계 Gate 통과 |
| 중단 조건 | 결정성 미달(hash 불일치 반복), Release Unit 기준 재검토 필요 발생 |

### 10.2 구현 원칙 (inherited contract)

```text
- 동일 입력은 동일 hash 생성
- immutable version 생성
- build 중간 산출물 비활성
- active file projection in-place 수정 금지
- version pointer 또는 manifest로 전환
- Runtime은 raw Markdown 대신 검증된 projection 사용
- build 실패 시 기존 projection 유지
```

### 10.3 Phase 2가 필요한 이유 (inherited contract)

```text
PostgreSQL 없이도
Validation, 결정성, 불변성, 버전 전환,
last-known-good 보호가 성립하는지 먼저 증명하기 위함.

이것이 성립하지 않으면 Phase 3은
"검증 안 된 데이터의 저장소 이동"이 된다.
```

---

## 11. Phase 3 진입 전 상세 설계 Gate

PostgreSQL 구현 전에 **별도의 상세 설계 리뷰**가 필요하다. 본 handoff는 설계 항목의 목록과 상태만 정의하며, 설계 자체를 수행하지 않는다. DDL·실제 schema는 본 문서에 작성하지 않는다.

| 설계 항목 | 상태 |
|---|---|
| Registry 논리 모델 | inherited from architecture (future-arch §6) — 상세화는 must decide before implementation |
| Artifact identity | inherited from architecture (§6.1) |
| Artifact immutable version | inherited from architecture (§6.2) |
| Projection Release Unit | must decide before implementation (§9 Gate) |
| Dependency relation | inherited from architecture (§6.3) — 저장 형태는 implementation-specific |
| Deployment state | inherited from architecture (§6.5) |
| Active pointer | must decide before implementation (저장·전환 방식) |
| Environment별 상태 | inherited from architecture — 매핑은 implementation-specific |
| Audit/provenance | inherited from architecture (§6.6, §12) — 저장 구조는 must decide before implementation |
| Response provenance | inherited from architecture (§12) — trace 연결(P-23)은 implementation-specific |
| Access boundary | must decide before implementation (exposure-policy 준수 방식 — RBAC/RLS 상세는 deferred) |
| Retention | must decide before implementation |
| Backup/restore | must decide before implementation |
| HA 요구사항 | must decide before implementation |
| Transaction boundary | must decide before implementation |
| Concurrency control | must decide before implementation |
| Idempotency | must decide before implementation |

- **Gate 규칙**: 위 `must decide before implementation` 항목의 설계 리뷰·승인 없이 Phase 3 구현을 시작하지 않는다.

---

## 12. Phase 3 — PostgreSQL Registry / Control Plane

### 12.1 Phase 정의

| 항목 | 내용 |
|---|---|
| 목적 | version / relationship / deployment state / audit의 Registry 저장 — Runtime 서빙 전환(Phase 4) 이전의 기록 계층 구축 |
| 진입 조건 | Phase 2 종료 + §11 상세 설계 Gate 통과 |
| repository 선확인 | P-19 (기존 DB client/config/migration 체계), P-20 (배포·인스턴스 구조) |
| 구현 작업 | §12.2 구현 순서 1~10 |
| 산출물 종류 | Registry 쓰기 경로, ingestion 절차, file/DB projection 비교 도구, audit 기록 경로 — 구체 table/column/index는 §11 설계 리뷰 산출물이 소유 (본 문서 범위 아님) |
| 테스트 요구사항 | §15 계층 3(persistence integration) — idempotency, 부분 적재 차단, hash 검증 |
| 관측성 요구사항 | ingestion success/failure, DB read failure, provenance/audit write failure 지표 |
| rollback 방법 | 이 단계에서 Runtime은 아직 file projection 사용 — Registry 문제 시 Registry 쓰기만 중단, 서빙 무영향이 원칙 |
| 종료 조건 | projection·version·상태가 Registry에 idempotent하게 기록되고 file projection과 hash 일치 |
| 다음 Phase 진입 조건 | 종료 조건 + 비교 도구가 file/DB 동등성을 지속 증명 |
| 중단 조건 | idempotency 실패 반복, transaction boundary 설계 결함 발견 (→ §11 재리뷰) |

### 12.2 구현 순서 (proposed implementation step)

```text
1.  Registry write path
2.  immutable version persistence
3.  content hash verification
4.  dependency relation persistence
5.  deployment state persistence
6.  active pointer management
7.  audit/provenance persistence
8.  idempotent projection ingestion
9.  file projection과 DB projection 비교
10. 실패 시 기존 active 보호
```

- 각 순서의 책임: 쓰기 경로는 build pipeline 전용이며(I-02), ingestion은 재실행해도 결과가 동일해야 하고(idempotent), active pointer 전환은 완결된 Release Unit에 대해서만 원자적으로 수행한다. **transaction boundary는 "한 Release Unit의 적재·전환이 부분 상태로 관측되지 않는 범위"를 최소 요구로 하며**, 구체 구현은 §11 설계 리뷰가 소유한다.

### 12.3 구현자 금지 사항 (inherited contract)

```text
- Markdown 의미를 DB 관리 화면에서 수정
- active row 직접 update로 내용 교체
- validation 실패 artifact 적재 후 수동 승인
- 일부 dependency만 적재하고 active 전환
- raw Markdown 전체를 무조건 모든 row에 복제
```

---

## 13. Phase 4 — Runtime 전환

즉시 cutover하지 않는다. (proposed implementation step)

### 13.1 Phase 정의

| 항목 | 내용 |
|---|---|
| 목적 | Finance Runtime의 조회 원천을 검증된 file projection에서 DB active projection으로 안전하게 전환 |
| 진입 조건 | Phase 3 종료 + file/DB 동등성 증명 지속 |
| repository 선확인 | P-04~P-06 (로딩 시점·cache — snapshot 고정과 last-known-good 경로 확정), P-23 (trace 구조) |
| 구현 작업 | §13.2 전환 순서 1~9 |
| 산출물 종류 | Runtime DB reader(제한 환경부터), shadow read 비교 보고, response provenance 기록 경로 |
| 테스트 요구사항 | §15 계층 4·7·8 — snapshot 고정, version 혼합 금지, DB 장애 주입(last-known-good 유/무) |
| 관측성 요구사항 | WAS별 loaded version/hash, stale instance count, DB read failure, last-known-good fallback 발생 |
| rollback 방법 | 조회 원천을 file projection으로 즉시 복귀 (전환 스위치는 상태 전환 — 코드 재배포 불필요가 목표) |
| 종료 조건 | 전 인스턴스 동일 active version 조회 + response provenance 기록 + 차이 검출 0건 유지 |
| 다음 Phase 진입 조건 | 종료 조건 + 전체 전환 승인 기록 |
| 중단 조건 | shadow read 차이 원인 미해결, snapshot 고정 실패, provenance 누락 반복 |

### 13.2 전환 순서 (proposed implementation step)

```text
1. DB Projection 적재
2. 기존 File Projection과 hash/semantic comparison
3. Runtime shadow read
4. 실제 응답에는 기존 검증 경로 사용
5. 차이 검출 및 원인 분석
6. 제한 환경에서 DB active projection 사용
7. 다중 WAS version 일치 확인
8. response provenance 확인
9. 전체 전환 승인
```

**shadow read의 정의** — 사용자 응답을 두 번 생성하라는 뜻이 아니다. Runtime이 사용할 projection의 **identity / hash / 관계 결과**를 기존 file projection과 비교하는 검증 단계다. 사용자에게 나가는 응답은 4번 원칙대로 기존 검증 경로가 생성한다.

### 13.3 전환 중 필수 준수 (inherited contract)

```text
- 요청 하나에서 file/DB version 혼합 금지
- Runtime request 시작 시 version snapshot 고정
- pointer 전환 중 부분 version 사용 금지
- DB 실패 시 raw Markdown fallback 금지
- 검증된 file projection 또는 DB cache만 last-known-good 후보
```

---

## 14. Phase 5 — Activation / Canary / Rollback

### 14.1 Phase 정의

| 항목 | 내용 |
|---|---|
| 목적 | candidate / canary / rollback / disable 운영 절차 정착과 multi-service 공유 기반 |
| 진입 조건 | Phase 4 종료 (전체 전환 승인) |
| repository 선확인 | P-20~P-22 (배포·지표·기존 rollback 절차와의 연결) |
| 구현 작업 | §14.3 Activation / Canary / Rollback / Disable 운영 동작 |
| 산출물 종류 | 상태 전이 절차, canary 판정 기록 형식, rollback 절차 (§20 checklist 충족) |
| 테스트 요구사항 | §15 계층 9(rollout/rollback) — candidate 격리, canary 중단, rollback rehearsal |
| 관측성 요구사항 | pointer transition, canary failure, rollback execution 지표 |
| rollback 방법 | 그 자체가 rollback 절차의 정착 단계 — §20 checklist가 실행 기준 |
| 종료 조건 | canary·rollback rehearsal 통과, 운영 Runbook(§18) 승인 |
| 다음 Phase 진입 조건 | — (최종 단계, §23 DoD로 연결) |
| 중단 조건 | rollback rehearsal 실패, audit fail-closed 규칙과 충돌하는 운영 요구 발생 |

### 14.2 계승하는 상태 축 모델 (inherited contract — future-arch §7)

```text
lifecycle_status (Markdown authoring document lifecycle):
- draft
- candidate
- active
- deprecated
- retired

projection_status (validation / projection build):
- validated
- projected
- rejected

deployment_status (Runtime / Control Plane):
- candidate
- canary
- active
- disabled
- deprecated
- archived

rollback-ready:
- 독립 status enum이 아니라 이전 active projection, projection hash, 복구 가능 deployment, dependency 보존 여부로 계산되는 derived capability 또는 flag
```

### 14.3 운영 동작 handoff

**Activation**

```text
- validated immutable version만 candidate 가능
- candidate 즉시 active 금지
- Release Unit 단위 활성화
- 상태 변경 audit 필수 (audit 실패 시 fail-closed — §17)
```

**Canary**

```text
- 일부 환경 또는 제한된 트래픽
- 기존 active와 명확히 분리
- 실패 기준과 중단 주체 사전 확정 (구체 비율·알고리즘은 deferred)
- 실패 시 트래픽은 이전 active deployment로 복귀한다. 실패한 deployment_status는 disabled가 되며, projection_status는 projected를 유지한다.
```

**Rollback**

```text
- 이전 immutable version으로 pointer 전환
- DB 내용 직접 수정 금지
- rollback 이유와 실행 주체 기록
- rollback 전후 version/hash 확인 (§20 checklist)
```

**Disable**

```text
- Markdown routable 의미는 유지
- 운영 routing_enabled만 false
- 의미와 운영 상태를 혼합하지 않음 (I-03)
```

---

## 15. 테스트 전략

전 계층 `proposed implementation step`이며, 테스트 코드는 본 문서에서 작성하지 않는다.

### 15.1 테스트 계층

```text
1.  Static validation test          (Phase 1)
2.  Projection build test           (Phase 2)
3.  Repository/persistence integration test (Phase 3)
4.  Runtime projection reader test  (Phase 2·4)
5.  Routing/PolicyGuard/Validator contract test (전 Phase)
6.  Exposure/leak test              (전 Phase)
7.  Multi-WAS consistency test      (Phase 4~5)
8.  Failure/recovery test           (Phase 2~5)
9.  Rollout/rollback test           (Phase 5)
10. End-to-end provenance test      (Phase 4~5)
```

### 15.2 필수 테스트 케이스

모든 케이스의 실패는 **배포 차단** 사유다 (예외 없음 — 각 케이스는 불변식 또는 상위 계약의 직접 검증이다).

| ID | 케이스 | 검증 목적 | 기대 결과 | 실패 시 배포 차단 |
|---|---|---|---|---|
| T-01 | metadata 누락 | WP-1 | validation 실패, projection 미생성 | 예 |
| T-02 | artifact key 중복 | WP-2 / I-01 | validation 실패 | 예 |
| T-03 | lens key 중복 | WP-2 | validation 실패 | 예 |
| T-04 | dangling dependency | WP-3 | validation 실패 | 예 |
| T-05 | 금지된 cycle | WP-4 | validation 실패 | 예 |
| T-06 | 존재하지 않는 redirect | WP-5 | validation 실패 | 예 |
| T-07 | 존재하지 않는 fixture | WP-6 | validation 실패 | 예 |
| T-08 | PolicyGuard alignment 실패 | WP-7 | validation 실패 | 예 |
| T-09 | Output Validator reference 실패 | WP-8 | validation 실패 | 예 |
| T-10 | exposure classification 누락 | WP-9 | validation 실패 | 예 |
| T-11 | raw-restricted의 public projection 혼입 | WP-10 / I-12 | build 차단 | 예 |
| T-12 | 동일 입력의 hash 불일치 | WP-12 / I-05 | 결정성 위반 검출 | 예 |
| T-13 | build 중간 실패 | I-13 | 중간 산출물 비활성, 기존 active 유지 | 예 |
| T-14 | Release Unit 일부만 성공 | I-15 | Unit 전체 승격 차단 | 예 |
| T-15 | candidate가 일반 Runtime에 노출됨 | I-08 | 조회 필터가 차단 | 예 |
| T-16 | active pointer 없음 | §18 Runbook | 안전한 오류 + 경보, 임의 버전 선택 없음 | 예 |
| T-17 | version/hash 불일치 | I-05 / I-14 | 해당 artifact 사용 중단, last-known-good 또는 안전 오류 | 예 |
| T-18 | DB 연결 실패 + last-known-good 존재 | I-09 / I-13 | 검증된 last-known-good으로 서빙 지속 + 경보 | 예 |
| T-19 | DB 연결 실패 + last-known-good 부재 | I-09 | 안전한 오류 (raw 파싱 없음) | 예 |
| T-20 | raw Markdown fallback 시도 | I-09 | 해당 경로 부재 — 시도 자체가 실패해야 함 | 예 |
| T-21 | 다중 WAS version 불일치 | I-14 / §13 | hash 기준 탐지 + refresh + 경보 | 예 |
| T-22 | cache stale | §13 | version/hash 무효화로 교정 | 예 |
| T-23 | canary 실패 | §14 | 트래픽은 이전 active deployment로 복귀, 실패한 deployment_status=disabled, projection_status=projected 유지 | 예 |
| T-24 | rollback 실패 | §18/§20 | 부분 전환 방치 없음, 복원 + 최고 경보 | 예 |
| T-25 | audit 기록 실패 (서빙) | §17 | durable buffer 있으면 조건부 지속, 없거나 법적 필수면 fail-closed | 예 |
| T-26 | audit 기록 실패 (Control Plane) | §17 | 상태 변경 fail-closed | 예 |
| T-27 | PolicyGuard 우회 시도 | I-10 | 모든 경로에서 우회 불가 | 예 |
| T-28 | Output Validator 우회 시도 | I-10 | 통과 전 노출 불가 | 예 |
| T-29 | Public API 내부 field 유출 | I-11 | snapshot에 금지 필드 부재 | 예 |
| T-30 | Frontend bundle 원문 혼입 | I-12 | leak scan 0건 | 예 |
| T-31 | 로그에 Prompt/Markdown/secret 기록 | I-16 | 로그 스캔 0건 | 예 |
| T-32 | error response에 stack/path/fragment 노출 | exposure-policy §8.3 | 안전한 error code만 반환 | 예 |
| T-33 | response provenance의 version/hash 누락 | §12(미래구조) | provenance 필수 필드 기록 | 예 |

---

## 16. Exposure와 보안 검증

`finance-document-exposure-policy.md`를 전 Phase에 그대로 계승한다. (inherited contract)

노출 구조는 다음 네 축으로 사용한다.

```text
- exposure_class 3종
- distribution_boundary 별도 축
- sanitized-runtime-output = Runtime output_class
- operational-metadata = 별도 운영 정보
```

필수 검증 (구현·배포 파이프라인 반영은 proposed implementation step):

```text
- raw-restricted 원문 Frontend 비포함
- internal-controlled 문서 Public API 비노출
- public-safe는 별도 승인 artifact만 공개
- sanitized-runtime-output만 사용자 반환
- operational-metadata 기본 비노출
- source map 서버 경로 비노출
- debug/admin 원문 자동 노출 금지
- Prompt logging 금지
- raw Markdown logging 금지
- red-team/unsafe draft production projection 비포함
- 외부 LLM 전송 최소화
```

추가 원칙:

- **PostgreSQL 계정이나 DB 저장 자체를 보안 통제로 간주하지 않는다** (storage-decision §10.2). 접근 등급은 저장소가 아니라 artifact의 exposure class를 따른다.
- projection row는 원본 artifact의 exposure class를 상속하며, 파생 과정에서 등급이 낮아지지 않는다.
- 구체적인 RBAC/RLS 구현은 후속 상세 설계(§11 Gate, §22) 대상으로 남긴다. (deferred)

---

## 17. Observability와 Audit

### 17.1 최소 관측 지표 후보 (proposed implementation step — 논리 수준)

```text
- validation success/failure
- projection build success/failure
- build duration
- active projection version
- active content hash
- WAS별 loaded version
- stale instance count
- pointer transition success/failure
- canary failure
- rollback execution
- DB read failure
- last-known-good fallback
- PolicyGuard action
- validator pass/fail
- provenance write failure
- audit write failure
```

### 17.2 로그 경계 (inherited contract)

| 로그 허용 | 로그 금지 |
|---|---|
| artifact key, version, hash, action code, validation result, trace id, latency | raw Markdown, full Prompt, unsafe draft, red-team 원문, secret, 사용자 투자 일지 원문 전체 |

### 17.3 Audit 실패 정책 (inherited contract — future-arch §14.3)

```text
사용자 서빙:
- local durable buffer 또는 재전송 가능한 audit 기록 경로가
  존재할 때만 조건부로 서빙을 일시 지속 (조건부 fail-open) + 경보
- 법적·계약상 audit가 필수인 환경이거나
  기록 복구 수단이 없으면 fail-closed

Control Plane 상태 변경:
- audit 기록 실패 시 항상 fail-closed
  (감사 없는 배포 상태 변경 금지)
```

- durable buffer의 구체 구현은 §22 Open Decision이다. 구현 전까지 "audit 실패 = 무조건 서빙 지속"으로 단순화하지 않는다.

---

## 18. 실패와 복구 Runbook 요구사항

운영 Runbook은 아래 13개 장애 각각에 대해 다음 8개 항목을 **전부** 답해야 한다. (implementation prerequisite — Runbook 승인은 §23 DoD 조건)

```text
공통 8항목:
- 사용자 영향
- 자동 안전 동작
- 운영 경보
- 즉시 확인할 지표
- 허용되는 fallback
- 금지되는 fallback
- 복구 절차
- 정상화 확인
```

**전 장애 공통 금지**: raw Markdown 즉석 파싱은 어떤 장애에서도 금지한다 (I-09).

장애별 핵심 방향 (Runbook 작성 시 최소 기준 — 세부는 Runbook이 채움):

| 장애 | 자동 안전 동작 | 허용되는 fallback | 금지되는 fallback |
|---|---|---|---|
| Validation 실패 | projection 미생성, 기존 active 유지 | 기존 active 지속 | 실패분 수동 승인 승격 |
| Projection build 실패 | 중간 산출물 폐기, 기존 유지 | 기존 active 지속 | 중간 산출물 활성화 |
| DB ingestion 실패 | 적재 중단, 부분 상태 미노출 | 재시도(idempotent) | 부분 적재 후 전환 |
| DB 연결 실패 | last-known-good 전환 시도 | 검증된 file projection / DB cache | raw Markdown 파싱 |
| active pointer 없음 | 안전한 오류 + 최고 경보 | 없음 (복구 절차로만) | 임의 버전 추정 선택 |
| hash/version 불일치 | 해당 artifact 사용 중단 | 일치하는 last-known-good | 불일치 상태로 서빙 |
| dependency graph 불완전 | 해당 Unit 승격 중단 | 기존 active 지속 | 부분 closure 승격 |
| multi-WAS version drift | hash 탐지 + refresh + 경보 | 각 인스턴스의 검증본 유지 | 미검증 강제 동기화 |
| stale cache | version/hash 무효화 | 재조회 | stale 데이터 무기한 서빙 |
| canary 실패 | canary 중단, 이전 active deployment로 복귀. 실패한 deployment_status=disabled, projection_status=projected 유지 | 기존 active | 실패 버전 부분 수정 재투입 또는 projection_status 변경 |
| rollback 실패 | 전환 전 상태 복원 시도, 최고 경보 | 없음 (부분 상태 방치 금지) | pointer를 미검증 상태로 방치 |
| audit 기록 실패 | §17.3 정책 적용 | durable buffer 경유 지속 (조건부) | audit 없는 Control Plane 변경 |
| exposure leak 탐지 | 해당 경로 즉시 차단 + 경보 | 없음 | "일단 서비스 우선" 방치 |

---

## 19. 배포 순서

특정 CI/CD 제품과 무관한 논리 순서다. (proposed implementation step)

```text
1.  Source revision 고정
2.  Validation 수행                     (WP-1~12)
3.  Projection Build
4.  content hash 생성·검증
5.  Release Unit 완결성 확인             (closure 완결, 부분 없음)
6.  candidate 등록
7.  기존 active 보호 확인                (pointer 미변경 상태 확인)
8.  제한 환경 검증
9.  canary
10. active pointer 전환
11. WAS별 version/hash 확인
12. response provenance 확인
13. exposure/log/API leak 검사
14. rollback-ready 확인                 (직전 버전 rollback 가능 상태)
15. 배포 완료 선언
```

**배포 완료 조건** — 다음이 전부 충족되어야 15번을 선언할 수 있다.

```text
- 전 인스턴스가 동일 active version/hash 조회 (11)
- provenance에 신규 version 기록 확인 (12)
- leak 검사 0건 (13)
- 직전 검증 버전이 rollback-ready 상태 (14)
- 배포 audit 기록 존재 (audit 실패 시 완료 선언 불가 — §17.3)
```

- 1~15 중 어느 단계든 실패하면 다음 단계로 진행하지 않고, 기존 active를 유지한 채 실패를 기록한다.

---

## 20. Rollback Checklist

rollback은 **내용 수정이 아니라 pointer/state 전환**이다. 실행 전 다음을 전부 확인한다. (proposed implementation step)

```text
[ ] 직전 검증 active version 존재
[ ] rollback target hash 확인
[ ] target의 dependency closure 완결성 확인
[ ] target exposure classification 확인
[ ] pointer 전환 범위 확인 (환경·Release Unit)
[ ] 다중 WAS 갱신 확인
[ ] PolicyGuard/Validator 적용 확인 (rollback 후에도 우회 없음)
[ ] response provenance 확인 (rollback version 기록)
[ ] rollback audit 기록
[ ] 새 candidate 격리 또는 deployment_status=disabled 처리
```

- 다시 강조한다: **rollback에서 DB 내용을 수정하지 않는다.** 전환 대상은 이미 존재하는 immutable version이며, "문제 부분만 고쳐서 되돌리기"는 rollback이 아니라 I-02/I-06 위반이다.

---

## 21. 구현 금지 사항

구현자가 하지 말아야 할 것의 전체 목록이다. (inherited contract)

```text
- 트리거 확인 없이 PostgreSQL부터 구축
- Phase 1~2 생략
- Markdown 전체를 그대로 DB에 복제
- DB를 새로운 Authoring Source로 사용
- 운영자가 projection 본문 직접 수정
- active version in-place 수정
- validation 실패를 수동 승인으로 우회
- dependency 일부만 승격
- raw Markdown fallback
- PolicyGuard 우회
- Output Validator 우회
- Admin 화면에 원문 전체 노출
- 디버그 편의를 위한 Prompt logging
- Audit를 이유로 사용자 원문 전체 저장
- 서로 다른 version을 한 요청에서 혼합
- DB 연결 성공을 readiness의 유일한 기준으로 사용
  (검증된 projection 로딩 여부가 readiness의 실질 기준)
- 개발 하네스 CLI 구조를 Finance Runtime에 복사
```

---

## 22. Open Decisions

본 문서에서 결정하지 않지만, 구현 전에 반드시 별도 결정해야 하는 항목이다. **구체적인 답을 이 문서에서 만들지 않는다.** (deferred)

| 항목 | 결정 시점 | 선행 조건 | 결정 주체 | 잘못 결정 시 위험 |
|---|---|---|---|---|
| Projection Release Unit 분할 기준 | Phase 1 종료 후, Phase 2 운영 배포 전 (§9 Gate) | dependency graph 완성 (WP-3~4) | 아키텍처 소유자 + 운영 책임자 | 부분 승격 사고 또는 전체 재배포 병목 재현 |
| 상세 Registry schema | §11 설계 Gate | 논리 모델 확정 | 설계 리뷰 | 재설계 migration 비용 |
| transaction boundary | §11 설계 Gate | Release Unit 기준 | 설계 리뷰 | 부분 상태 관측, 정합 붕괴 |
| concurrency control | §11 설계 Gate | transaction boundary | 설계 리뷰 | 동시 배포 race, pointer 오염 |
| idempotency key | §11 설계 Gate | version identity 확정 | 설계 리뷰 | 중복 적재, 재시도 불능 |
| active pointer 저장 방식 | §11 설계 Gate | deployment state 모델 | 설계 리뷰 | 전환 원자성 상실 |
| cache strategy | Phase 4 착수 전 | P-06 결과 | 설계 리뷰 + 운영 | stale 서빙, 무효화 폭주 |
| cache invalidation/refresh 방식 | Phase 4 착수 전 | cache strategy | 설계 리뷰 + 운영 | multi-WAS drift 상시화 |
| PostgreSQL HA/backup/restore | Phase 3 착수 전 | HA 요구 확정 (§11) | 운영 책임자 | 단일 장애점, 복구 불능 |
| retention | Phase 3 착수 전 | audit 정책 (G-09) | 운영 책임자 + 승인 주체 | 법적 요건 위반 또는 무한 증적 |
| audit 저장 구조 | §11 설계 Gate | audit 정책 | 설계 리뷰 | 기록 누락·과잉 기록(원문 복제) |
| authorization/RBAC/RLS | Phase 3 착수 전 (상세는 후속) | access boundary (§11) | 설계 리뷰 + 보안 | 과권한 계정 = 노출 표면 |
| canary 기준 | Phase 5 착수 전 | 관측 지표 가동 | 운영 책임자 | 실패 감지 불능 또는 과민 중단 |
| rollback window | Phase 5 착수 전 | retention | 운영 책임자 | rollback target 소실 |
| durable audit buffer | Phase 4 착수 전 | audit 정책 (G-09) | 설계 리뷰 + 운영 | audit 실패 시 fail-open/closed 판정 불능 |
| multi-service Knowledge Pack 공유 방식 | 해당 트리거 실측 후 | Phase 5 안정화 | 아키텍처 소유자 | domain 의미의 Core 유출 (handoff 문서 경고) |

---

## 23. Definition of Done

전체 구현 완료는 다음이 **전부** 충족될 때만 선언한다.

```text
- 실제 도입 트리거와 승인 증거 존재                    (G-01~G-03)
- Phase 0 baseline 검증 완료                          (충돌 0건)
- 모든 artifact metadata/exposure 검증                (WP-1~11)
- deterministic hash 재현                             (WP-12, T-12)
- immutable file projection 운영 검증                  (Phase 2 종료)
- Release Unit 기준 확정                              (§9 Gate)
- PostgreSQL Registry 상세 설계 승인                   (§11 Gate)
- idempotent projection ingestion                     (Phase 3)
- active/candidate/canary 상태 전이 검증               (Phase 5)
- multi-WAS 동일 version 확인                         (T-21)
- last-known-good 복구 검증                           (T-18~19)
- raw Markdown fallback 부재 확인                     (T-20)
- PolicyGuard/Validator 우회 경로 부재                 (T-27~28)
- API/Frontend/log/error leak test 통과               (T-29~32)
- response provenance 추적 가능                       (T-33)
- rollback rehearsal 통과                             (T-24, §20)
- 운영 Runbook 승인                                   (§18)
- 책임자와 승인 체계 확정                              (G-10~11)
```

다음은 완료로 인정하지 않는다.

```text
- DB에 문서가 들어감
- 테이블이 생성됨
- 한 인스턴스에서 조회 성공
- happy path 테스트만 통과
```

---

## 24. 구현자 최종 실행 순서

압축 실행 순서. **각 단계가 실패하면 다음 단계로 넘어가지 않는다.** 실패 시 행동은 항상 같다: 중단 → 기존 상태 유지 → 실패 기록 → 해당 Gate/조건 재충족 후 재개.

```text
1.  Stop/Go Gate 확인                  (§3 — STOP이면 여기서 종료, MVP 유지)
2.  실제 repo 검증                     (§4 Preflight, §7 Phase 0)
3.  MVP 계약 위반 우선 수정             (§7.3 — C 항목 0건 될 때까지)
4.  Phase 0 완료 및 PG-1 승인           (§6, §7 종료 조건)
5.  PG-1 승인 후 Phase 1 Validation 착수 (§8 WP-1~12)
6.  Phase 1 Validation 완료 및 Release Unit 결정 (§8 종료 조건, §9 Gate)
7.  PG-2 승인 후 Phase 2 production deployment (§9.5, §10)
8.  Phase 2 완료                        (§10 종료 조건)
9.  Phase 2 완료 후 PG-3 승인 및 Phase 3 착수 (§6, §11)
10. PostgreSQL 상세 설계 리뷰           (§11 Gate)
11. Phase 3 Registry 구현              (§12)
12. File/DB projection 비교            (§12.2-9)
13. Phase 4 shadow read                (§13.2-3~5)
14. 제한 환경 전환                     (§13.2-6)
15. Phase 5 canary                     (§14)
16. active 전환                        (§19 배포 순서)
17. provenance/leak/consistency 검증    (§19 완료 조건)
18. rollback rehearsal                 (§20)
19. 운영 인수                          (§18 Runbook 승인, §23 DoD)
```

---

## 25. 최종 Handoff

미래 구현자에게 전달하는 계약을 요약한다.

```text
1. 이 문서는 승인이 아니다.
   §3 Gate가 STOP이면 아무것도 구현하지 말고 MVP를 유지하라.

2. repo가 유일한 증거다.
   문서에 적힌 구조를 구현 완료로 믿지 말고, §4 Preflight로 확인하라.
   확인 전 모든 판정은 verification required다.

3. 순서를 지켜라.
   Global Gate → Phase 0 → Phase 0 완료 → PG-1 → Phase 1
   → Phase 1 완료 → PG-2 → Phase 2 production deployment
   → Phase 2 완료 → PG-3 → Phase 3. 건너뛰기는 없다.
   검증 없는 DB 적재는 Projection이 아니라 저장소 이동이다.

4. 불변식은 협상 대상이 아니다.
   I-01~I-16, 특히: Markdown = 의미의 Source of Truth,
   raw Markdown 즉석 폴백 금지, PolicyGuard/Validator 우회 금지,
   sanitized-runtime-output만 사용자 반환.

5. 실패의 기본값은 기존 검증 상태의 보호다.
   새 버전 승격은 fail-closed,
   서빙은 검증된 last-known-good에 한한 조건부 fail-open,
   Control Plane 상태 변경은 audit 없이는 fail-closed.

6. 미확정은 미확정으로 다뤄라.
   §22 Open Decisions를 이 문서나 코드에서 즉석 결정하지 말고,
   각 결정 시점과 결정 주체를 따르라.

7. 완료 선언은 §23 DoD로만 한다.
   "DB에 들어갔다"는 완료가 아니다.
```

본 handoff는 상위 세 문서(storage-decision, exposure-policy, db-future-architecture)가 개정되는 경우 그에 맞춰 재검토하며, 그 전까지 미래 구현의 유효한 실행 계약으로 유지된다.
