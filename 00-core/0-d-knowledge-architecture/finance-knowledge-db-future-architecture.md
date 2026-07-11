# Finance Knowledge DB Future Architecture

## 0. Metadata

```text
status            : proposed v1.0 — 0-D Knowledge Architecture 세 번째 문서
document type     : Future Architecture Decision
                    (미래 아키텍처 문서 — 현재 구현 상태를 서술하지 않음)
path              : 00-core/0-d-knowledge-architecture/
                    finance-knowledge-db-future-architecture.md
scope             : 상위 ADR의 도입 트리거가 실제로 발생했을 때,
                    Markdown Knowledge Artifact를 검증된 PostgreSQL
                    Runtime Projection / Control Plane으로 전환하는
                    미래 구조와 책임 경계의 사전 합의.
activation        : 본 문서의 미래 구조는 §15 트리거의 실제 발생과
                    §20 구현 시작 조건 충족 이전에는 착수하지 않는다.
parent decisions  :
  - finance-knowledge-storage-decision.md   (decided v1.0 — 저장 결정)
  - finance-document-exposure-policy.md     (decided v1.0 — 노출 정책)
reference         :
  - finance-harness-knowledge-architecture-v2-handoff.md (개발 하네스 handoff)
  - finance-routing-contract.md              (0-C Runtime Contract)
non-goals         :
  - 지금 PostgreSQL을 도입하는 것
  - PostgreSQL DDL / table / column / index / migration 설계
  - ingestion 코드, API endpoint 상세, Auth/Billing, Frontend 설계
  - Finance Lens / PolicyGuard / Routing Contract / Fixture /
    Output Validator의 canonical 의미 수정
```

- **상태 선언**: 본 문서에서 `decided / inherited contract`로 표시된 것(§2, §18)을 제외한 모든 구조는 `proposed / future architecture` 또는 `not implemented`다. 어떤 절도 현재 구현 완료를 의미하지 않는다.
- 충돌 시 우선순위: 저장 결정은 상위 ADR이, 노출 등급과 금지 경계는 Exposure Policy가, intent/action/lens/redirect/validator의 의미는 각 canonical 문서가 소유한다. 본 문서는 **미래 Projection 구조와 소유권 경계만** 소유하며, 상위 계약을 재정의하거나 완화하지 않는다.

---

## 1. 문서 목적

본 문서는 **PostgreSQL을 지금 구현하는 문서가 아니다.** 상위 ADR이 확정한 도입 트리거가 실제 운영 문제로 발생했을 때, 탐색 비용과 임기응변 없이 전환할 수 있도록 미래 구조를 미리 합의해 두는 문서다.

이 문서가 답하는 핵심 질문은 다음 열 가지다.

```text
1.  Markdown과 PostgreSQL은 각각 무엇을 소유하는가?          (§5)
2.  Markdown → Projection은 어떤 검증·빌드 절차를 거치는가?   (§8, §9)
3.  여러 WAS가 동일 Knowledge 버전을 어떻게 사용하는가?       (§11)
4.  candidate / canary / active / disabled 상태 관리는?      (§7, §10)
5.  특정 Lens·Policy 버전의 활성화·비활성화·롤백은?           (§10)
6.  dependency / fixture / version / hash / audit의 논리 구조는? (§6)
7.  어떤 응답이 어떤 artifact 버전을 사용했는지 추적은?        (§12)
8.  이중 Source of Truth를 어떻게 방지하는가?                (§5.3)
9.  Projection build나 DB 실패 시 Runtime의 안전 동작은?      (§14)
10. PostgreSQL 도입을 실제로 시작할 조건은 무엇인가?          (§15, §20)
```

문서의 성격을 명확히 한다.

```text
finance-knowledge-storage-decision.md
= 지금 어디에 저장하는가 (MVP 결정 — DB 미도입)

finance-document-exposure-policy.md
= 무엇을 누구에게 어디까지 보여주는가 (등급·금지 경계)

finance-knowledge-db-future-architecture.md
= 트리거가 실제로 발생하면 어떤 구조로 전환하는가 (미래 합의)
```

---

## 2. 상위 결정과 변경 불가 원칙

### 2.1 계승하는 저장 결정 (decided / inherited contract)

다음 결정은 본 문서가 변경할 수 없으며, 뒤집지 않는다.

```text
Finance Harness MVP
= Markdown Authoring Source of Truth
+ WAS private resource
+ Finance Runtime 내부 로딩
+ Sanitized API Response

PostgreSQL Knowledge DB는 MVP에 도입하지 않는다.
```

- 본 문서의 존재가 "PostgreSQL 도입이 확정된 다음 단계"임을 의미하지 않는다. 도입은 §15 트리거의 **실제 발생**과 §20 조건 충족에만 의존한다.

### 2.2 계승하는 노출 정책 (decided / inherited contract)

노출 등급 5종과 경계는 이미 확정되어 있다.

```text
- raw-restricted
- internal-controlled
- public-safe
- sanitized-runtime-output
- operational-metadata
```

PostgreSQL이 도입되더라도 다음 원칙은 그대로 유지된다(MUST).

```text
- raw document API 금지
- Frontend 원문 노출 금지
- 로그·오류 redaction
- Admin/Debug 원문 자동 노출 금지
- 외부 모델 전송 최소화
- sanitized-runtime-output만 사용자에게 반환
```

- **PostgreSQL은 Exposure Policy를 대체하거나 완화하지 않는다**(MUST NOT). 저장 매체 전환은 노출 경계의 어떤 규칙도 바꾸지 않는다.

### 2.3 계승하는 canonical 의미 (decided / inherited contract)

본 문서는 다음 의미를 인용만 하며 수정하지 않는다(MUST NOT).

```text
- Finance Lens 본문 / lens key / trigger
- PolicyGuard intent·action·redirect 의미
- Routing Contract 실행 파이프라인
- Fixture expected behavior
- Output Validator 의미
```

---

## 3. 현재 구조와 미래 도입 배경

### 3.1 현재 MVP 구조 (decided / current)

```text
[Private Finance Markdown]
        ↓
[WAS Private Resources]
        ↓
[Finance Runtime]
        ↓
[Sanitized API Response]
        ↓
[Frontend]
```

- 문서 변경은 Git 리뷰를 거쳐 애플리케이션 배포와 함께 반영된다.
- 원문은 어떤 단계에서도 Frontend로 직접 전달되지 않는다.

### 3.2 현재 구조의 한계 (상위 ADR §10.4 계승)

다음 한계는 현재 규모에서 수용 가능하며, 수용 불가능해지는 시점이 도입 트리거다.

- 다중 WAS 인스턴스 간 문서 버전 일관성을 구조적으로 보장하지 못한다 (배포 절차 의존).
- 특정 Lens·Policy만 runtime에서 교체·비활성화·롤백하기 어렵다 (전체 재배포 필요).
- Lens–PolicyGuard–Fixture–Validator 관계 검증이 파일 기반 도구와 리뷰에 의존한다.
- 어떤 응답이 어떤 문서 hash/version을 사용했는지 감사하기 어렵다.

### 3.3 이 문서가 지금 존재하는 이유

- 트리거는 예고 없이 발생하며, 발생 시점에 구조를 처음부터 설계하면 **임기응변 도입**(원문 DB 이전, 이중 Source of Truth)으로 퇴행할 위험이 크다.
- 본 문서는 그 시점에 참조할 **사전 합의된 구조와 금지선**을 기록한다. 기록 자체는 도입 착수가 아니다.

---

## 4. 목표 아키텍처

### 4.1 기준 구조 (proposed / future architecture)

미래 구조의 기준 흐름은 다음과 같다. **Markdown 원본 → 검증 → Projection → Runtime → Sanitized Response의 단방향 흐름은 어떤 단계에서도 변경하지 않는다**(MUST).

```text
[Private Markdown Authoring Source]          ← 의미의 Source of Truth

- Safety Core
- PolicyGuard
- Routing Contract
- Runtime Contract
- Lens Infrastructure
- Core / Asset / Long-term Lens
- Fixture
- Output Validator
- Prompt Template
- Exposure Metadata

             ↓

[Validation Pipeline]                        ← §8

- metadata validation
- unique key validation
- dependency validation
- redirect validation
- fixture reference validation
- PolicyGuard alignment validation
- exposure classification validation
- forbidden reference validation
- content hash generation

             ↓

[Projection Build]                           ← §9

- immutable artifact version 생성
- sanitized runtime block 생성
- dependency graph 생성
- routing index 생성
- provenance 기록
- build result 생성

             ↓

[PostgreSQL Knowledge Runtime / Control Plane]   ← §6

- artifact registry
- artifact version
- content hash
- dependency graph
- routing projection
- candidate / canary / active / disabled
- environment rollout state
- exposure classification
- validation status
- audit / provenance

             ↓

[Finance Runtime]                            ← §11

- active projection 조회
- intent 처리
- Lens 선택
- PolicyGuard 적용
- Output Validator 적용
- response provenance 기록
- Sanitization

             ↓

[Sanitized API Response]

             ↓

[Frontend]
```

### 4.2 구조 해석 원칙

- 위 흐름에서 화살표는 **데이터 생성의 방향**이다. 역방향(DB에서 Markdown으로 의미를 되쓰는 흐름, Runtime이 draft 원문을 직접 읽는 흐름)은 존재하지 않는다(MUST NOT).
- Finance Runtime의 intent 처리 → Lens 선택 → PolicyGuard → Output Validator 순서와 의미는 `finance-routing-contract.md` §2의 실행 파이프라인을 그대로 따른다. 본 문서는 Runtime이 **무엇을 읽는가**(active projection)만 바꾸며, **어떻게 판단하는가**는 바꾸지 않는다.
- PostgreSQL 도입 이후에도 문서를 DB로 "이전"하는 것이 아니라, Markdown에서 **검증을 통과한 projection을 생성**한다 (상위 ADR §12 계승).

---

## 5. Source of Truth와 소유권 경계

### 5.1 Markdown이 소유하는 의미 (decided / inherited contract)

Markdown Authoring Source가 소유하는 것은 **의미**다.

```text
- artifact key
- lens key
- 문서 본문
- trigger 의미
- forbidden intent
- redirect 의미
- related lens
- dependency 선언
- fixture 연결 의미
- PolicyGuard 연결 의미
- Output Validator 체크포인트 의미
- 원칙상 routable 여부
- 기본 exposure classification
- authoring status
```

- 본 절은 소유권 **범주**만 정리한다. 각 항목의 실제 canonical 의미는 기존 문서(`finance-policyguard-rules.md`, `finance-lens-catalog.md`, `finance-routing-contract.md` 등)가 소유하며 본 문서는 변경하지 않는다.

### 5.2 PostgreSQL이 소유하는 운영 상태 (proposed / future architecture)

PostgreSQL Runtime Projection / Control Plane이 소유할 수 있는 것은 **운영 상태**다.

```text
- immutable artifact version
- content hash
- validation result
- projection build id
- candidate / canary / active / disabled
- environment별 rollout 상태
- 실제 routing enabled 상태
- active version pointer
- rollback target
- access audit
- deployment audit
- response provenance
```

### 5.3 영구 금지 원칙 (decided / inherited contract)

다음 구조는 도입 트리거·도입 단계와 무관하게 **영구적으로 금지**한다(MUST NOT).

```text
Markdown에서 artifact 의미 수정
+
PostgreSQL에서 같은 artifact 의미를 사람이 별도로 수정
```

이를 방지하는 원칙:

| 원칙 | 수준 |
|---|---|
| PostgreSQL row는 Authoring Source에서 생성된 Projection이다 | MUST |
| Projection row를 사람이 직접 편집하지 않는다 | MUST NOT (편집) |
| 의미 변경은 반드시 Markdown 변경으로 시작한다 | MUST |
| 운영 상태(§5.2)만 Control Plane에서 변경할 수 있다 | MUST |
| 의미와 운영 상태의 소유자를 혼합하지 않는다 | MUST NOT (혼합) |

**의미/운영 분리의 대표 예시** (개발 하네스 handoff §9 계승):

```text
Markdown (의미 소유):
  runtime.routable = true      ← 원칙상 라우팅 가능하다고 선언
  default_enabled  = false

PostgreSQL (운영 상태 소유):
  deployment_status = candidate
  routing_enabled   = false     ← 운영에서는 아직 활성화하지 않음
```

문서는 라우팅 가능하다고 선언할 수 있지만, 운영 활성화 여부는 별개의 축이다. 두 축을 한 저장소·한 소유자로 합치는 순간 이중 Source of Truth로 퇴행한다.

---

## 6. 논리 구성요소

본 절은 **논리 구성요소의 역할만** 정의한다. 구체적인 PostgreSQL schema, 테이블명, column, index는 정의하지 않으며, 도입 결정 시점의 별도 설계 문서가 소유한다. 전체가 `proposed / future architecture`다.

### 6.1 Artifact Registry

Knowledge Artifact의 **정체성(identity)** 을 관리하는 구성요소.

```text
- artifact identity        (전역 유일 식별)
- artifact type            (Lens / PolicyGuard / Fixture / Template 등)
- canonical key            (lens key 등 — 의미는 Markdown 소유, 등록만 보관)
- exposure class           (Exposure Policy 5등급 중 하나 — 재분류 권한 없음)
- current lifecycle state  (§7 상태의 현재값)
- provenance               (어느 Authoring Source에서 왔는가)
```

- Registry는 key의 **등록부**이지 의미의 소유자가 아니다. canonical key의 의미 변경은 Markdown에서만 시작한다.

### 6.2 Artifact Version

버전의 **불변성(immutability)** 을 보장하는 구성요소.

```text
- immutable version        (생성 후 내용 변경 불가)
- content hash             (동일 입력 → 동일 hash, §9 결정성)
- source revision          (어느 Git revision에서 생성되었는가)
- validation result        (§8 통과 기록)
- build timestamp
- previous version relation (버전 계보 — rollback target 추적)
```

### 6.3 Dependency Graph

문서 간 **관계의 투영**을 보관하는 구성요소.

```text
- depends_on
- redirects_to
- related_to
- validated_by_fixture
- governed_by_policy
- checked_by_validator
```

- 각 관계의 canonical 의미(redirect가 무엇을 뜻하는지, fixture가 무엇을 검증하는지)는 기존 문서가 소유한다. 본 구성요소는 **선언된 관계의 질의 가능 형태**만 보관하며, 새로운 Finance 의미를 만들지 않는다(MUST NOT).

### 6.4 Runtime Projection

Finance Runtime이 **실제로 읽는** 정규화된 실행 데이터.

```text
- Runtime이 실제 읽는 정규화된 실행 데이터
- 필요한 최소 content block
- routing index
- validated relationship
- exposure-safe runtime representation
```

**용어 경계 — `sanitized runtime block`의 구분**

```text
sanitized runtime block
= Runtime 내부용 정제 projection
  (내부 실행을 위해 정규화·최소화된 데이터 — 여전히 서버 내부 전용)

sanitized-runtime-output
= Output Validator를 통과해 사용자에게 반환되는 응답
  (Exposure Policy §11 계약의 대상)
```

- 이 둘을 혼동하면 "projection이니까 공개 가능"이라는 오독이 생긴다. `sanitized runtime block`은 사용자 공개 콘텐츠가 아니며, 원본 artifact의 exposure class를 그대로 상속한다(MUST, §13).

### 6.5 Deployment State

버전의 **배포 상태**를 관리하는 구성요소.

```text
- candidate
- canary
- active
- disabled
- deprecated
- rollback target
- environment           (환경별로 active version이 다를 수 있음)
```

### 6.6 Audit / Provenance

**추적 가능성**을 담당하는 구성요소.

```text
- 누가 어떤 Authoring Source 변경을 승인했는가
- 어떤 source revision에서 projection이 생성되었는가
- 어떤 validation 결과를 통과했는가
- 언제 어떤 version이 활성화되었는가
- 어떤 응답이 어떤 active version을 사용했는가
```

- Audit 가능성은 **원문 보관과 동일하지 않다**(§12). Audit는 식별자·hash·상태 전이를 기록하는 것이지, raw 원문을 복제 보관하는 것이 아니다.

---

## 7. Artifact Lifecycle

본 lifecycle 전체는 `proposed / future architecture`이며 **현재 구현되지 않았다**.

### 7.1 기본 전이 흐름

```text
draft
  → validated
  → projected
  → candidate
  → canary
  → active
  → deprecated
  → archived

(분기 상태: rejected / disabled / rollback-ready)
```

### 7.2 상태 정의

| 상태 | 의미 | Runtime 사용 | 다음 상태 전이 조건 | 변경 주체 |
|---|---|---|---|---|
| `draft` | 작성·수정 중인 Markdown | ❌ 불가 | Git 리뷰 승인 + §8 Validation Pipeline 전체 통과 → `validated` | 작성자(사람) + 리뷰 절차 |
| `validated` | 검증을 통과한 source revision | ❌ 불가 (아직 projection 아님) | §9 Projection Build 성공 → `projected` | Validation Pipeline (자동) |
| `projected` | immutable projection 생성 완료 | ❌ 불가 (배포 상태 아님) | 운영자 배포 후보 등록 → `candidate` | Build Pipeline (자동 생성) + 운영자(등록) |
| `candidate` | 배포 후보로 등록된 버전 | ❌ 불가 (일반 트래픽 미노출) | 운영자 승인 → `canary`. 검증 문제 발견 → `rejected` | 운영자 (Control Plane) |
| `canary` | 일부 환경·일부 트래픽만 사용 | ⚠️ 제한적 사용 | canary 기준 통과 → `active`. failure threshold 초과 → `rejected` 또는 이전 `active` 유지 | 운영자 또는 자동 중단 프로세스 |
| `active` | 정식 서빙 버전 | ✅ 사용 | 새 버전 `active` 전환 시 → `deprecated`. 운영 차단 필요 시 → `disabled` | Control Plane pointer 전환 |
| `deprecated` | 직전 활성 버전 (rollback target 유지) | ❌ 기본 미사용 (rollback 시 재활성 가능) | rollback window 경과 → `archived`. rollback 실행 → `active` 복귀 | 운영 정책 (자동) + 운영자(rollback) |
| `archived` | 계보 보존용 종단 상태 | ❌ 불가 | 종단 (전이 없음) | 운영 정책 (자동) |
| `rejected` | validation 또는 canary 실패로 폐기 | ❌ 불가 | 종단. 재시도는 Markdown 수정 → 새 `draft`부터 | Pipeline (자동) 또는 운영자 |
| `disabled` | 원칙상 routable하지만 운영상 차단 | ❌ 불가 (라우팅 제외) | 운영자 해제 → `active` 복귀 | 운영자 (Control Plane) |
| `rollback-ready` | `deprecated` 중 즉시 전환 가능함을 표시하는 부가 상태 | ❌ 기본 미사용 | rollback 실행 시 pointer 전환 대상 | 운영 정책 |

### 7.3 lifecycle 공통 원칙

- Runtime이 사용할 수 있는 상태는 `active`와 (제한 범위의) `canary`뿐이다(MUST). `draft`/`candidate` 원문을 요청 처리에 사용하는 구현은 계약 위반이다.
- 상태 전이는 **한 방향으로만 승격**한다. 실패는 항상 `rejected` 또는 기존 `active` 유지로 귀결되며, 실패한 버전을 부분 수정해 그대로 승격하지 않는다(MUST NOT).
- `disabled`는 Markdown의 `routable` 선언을 바꾸지 않는다. 의미(routable)와 운영 상태(enabled)는 §5.3의 분리 원칙을 따른다.
- 구체적인 승인 시스템·RBAC는 본 문서의 범위가 아니다(not implemented).

---

## 8. Validation Pipeline

본 절 전체는 `future implementation requirement`다. 현재 구현되어 있지 않으며, Projection 도입 시 반드시 선행 구축해야 하는 검증 계약이다.

### 8.1 검증 항목

Projection 생성 전에 다음을 검증해야 한다(MUST — future implementation requirement).

| # | 검증 항목 | 실패 의미 |
|---|---|---|
| C-01 | metadata 필수값 존재 | 의미 불완전 문서의 projection 방지 |
| C-02 | artifact key 중복 | identity 충돌 방지 |
| C-03 | lens key 중복 | catalog 등록 이름 충돌 방지 |
| C-04 | dependency 누락 | 선언 없는 참조 방지 |
| C-05 | dangling reference | 존재하지 않는 문서 참조 방지 |
| C-06 | redirect 대상 존재 여부 | redirect 계약 무결성 (의미는 canonical 문서 소유) |
| C-07 | 순환 dependency 또는 금지된 cycle | 로딩·검증 불능 구조 방지 |
| C-08 | fixture reference 유효성 | 검증 근거 없는 artifact 방지 |
| C-09 | PolicyGuard alignment | intent/action/redirect 사용의 문서 간 정합 |
| C-10 | Output Validator reference | validator 체크포인트 연결 확인 |
| C-11 | exposure classification 존재 여부 | 미분류 artifact의 projection 방지 |
| C-12 | raw-restricted artifact의 공개 projection 혼입 여부 | Exposure Policy 위반 projection 차단 |
| C-13 | content hash 생성 | 결정성·버전 식별의 전제 |
| C-14 | canonical file path와 provenance 확인 | 출처 불명 artifact 방지 |

### 8.2 검증 실패 시 원칙

```text
- Projection 생성 금지               (MUST NOT 생성)
- candidate 등록 금지                (MUST NOT 등록)
- 기존 active version 유지           (MUST)
- 실패 원인 기록                     (MUST)
- 부분 성공 artifact를 active로 승격 금지 (MUST NOT)
```

- 검증은 **all-or-nothing**이다. 한 artifact 집합에서 일부만 통과한 경우, 통과분만 골라 승격하는 부분 승격은 dependency 정합을 깨뜨리므로 금지한다.

---

## 9. Build / Projection 원칙

본 절 전체는 `proposed / future architecture`다.

### 9.1 결정성과 불변성

| 원칙 | 수준 |
|---|---|
| Projection은 deterministic해야 한다 | MUST |
| 동일한 source revision과 입력은 동일한 content hash를 생성해야 한다 | MUST |
| Projection은 immutable version 단위로 생성한다 | MUST |
| active projection을 in-place 수정하지 않는다 | MUST NOT (수정) |
| 새 버전을 생성한 뒤 pointer 또는 deployment state를 전환한다 | MUST |
| build 중간 산출물은 active로 사용하지 않는다 | MUST NOT |
| build가 실패해도 기존 active version은 유지한다 | MUST |

### 9.2 Projection 최소 포함 원칙

Projection은 다음 중 **필요한 항목만** 포함한다(MUST).

```text
- Runtime에 필요한 최소 content
- validated metadata
- dependency relation
- routing relation
- exposure class
- source/version provenance
```

### 9.3 raw-restricted 복제 금지

- Red-team 원문, unsafe draft, 전체 Prompt 등 **Runtime 실행에 불필요한 raw-restricted 데이터를 모든 Projection에 무조건 복제하지 않는다**(MUST NOT).
- Projection 대상 선별의 기준은 "저장소에 있는가"가 아니라 "**Runtime이 이 요청 유형을 처리하는 데 필요한가**"다.
- Red-team corpus는 검증(regression) 단계의 입력이지 서빙 projection의 구성요소가 아니다. Exposure Policy §9(production prompt 기본 제외)와 동일한 원칙이 projection 단계에도 적용된다.

---

## 10. Activation / Rollout / Rollback

본 절 전체는 `proposed / future architecture`다.

### 10.1 Activation

```text
- validated projection만 candidate가 될 수 있다.        (MUST)
- candidate는 즉시 active가 되지 않는다.                (MUST NOT)
- activation은 immutable version 단위로 수행한다.       (MUST)
- active version은 환경별로 다를 수 있다.               (허용)
```

### 10.2 Canary

```text
- 일부 환경 또는 일부 트래픽에서만 새 version을 사용한다.
- canary 결과와 validation 상태를 기록한다.             (MUST)
- failure threshold 초과 시 자동 또는 수동 중단이 가능해야 한다. (MUST)
```

- 구체적인 트래픽 비율·중단 알고리즘은 본 문서에서 정하지 않는다(not implemented).
- canary 중 실패한 버전은 `rejected`로 종료하며, 부분 수정 후 재승격하지 않는다 (§7.3).

### 10.3 Rollback

```text
- 이전 immutable active version을 rollback target으로 유지한다. (MUST)
- rollback 시 artifact 의미를 DB에서 수정하지 않는다.          (MUST NOT)
- pointer 또는 deployment state만 이전 version으로 전환한다.    (MUST)
- rollback 이유와 실행 주체를 기록한다.                        (MUST)
```

- rollback은 **상태 전환이지 내용 수정이 아니다.** "DB에서 문제 부분만 고쳐서 계속 서빙"하는 방식은 §5.3 영구 금지 구조로의 퇴행이며 어떤 긴급 상황에서도 허용하지 않는다.

### 10.4 Disabled

```text
- artifact가 원칙상 routable해도 운영 상태에서 disabled할 수 있다.
- Markdown 의미(routable 선언)와 runtime enable 상태를 구분한다. (MUST)
```

---

## 11. Finance Runtime 조회 모델

본 절 전체는 `proposed / future architecture`다.

### 11.1 Runtime 책임 원칙

| 원칙 | 수준 |
|---|---|
| active 상태의 Projection만 사용한다 | MUST |
| 요청 처리 중 임의의 draft/candidate 원문을 사용하지 않는다 | MUST NOT |
| 동일 요청에서는 일관된 artifact version 집합을 사용한다 (요청 중간에 버전 혼합 금지) | MUST |
| Projection 조회 실패 시 raw Markdown을 Public API로 반환하지 않는다 | MUST NOT |
| 실패 시 안전한 오류 또는 기존 검증된 active version을 사용한다 | MUST |
| 적용 artifact version을 provenance로 기록한다 | MUST |

- Runtime의 판단 순서(Input Guard → Intent → Action → Lens/Redirect → Template → Validator)는 `finance-routing-contract.md`가 소유하며, 조회 모델 전환이 이 순서를 생략·역전시키지 않는다(MUST NOT).

### 11.2 다중 WAS 일관성

다중 WAS 환경에서는 다음을 고려해야 한다.

```text
- 모든 인스턴스가 동일 active version을 조회한다.
- cache 사용 시 version/hash로 무효화한다.
- deployment state 변경 시 일관된 cache refresh를 수행한다.
- 오래된 projection을 사용 중인 인스턴스를 탐지한다.
```

- 일관성 판단의 기준값은 content hash와 active version pointer다. "배포 시각"이나 "인스턴스 기동 순서" 같은 간접 지표로 판단하지 않는다(SHOULD NOT).
- 구체적인 cache 제품·무효화 구현·전파 메커니즘은 본 문서의 범위가 아니다(not implemented).

---

## 12. Response Provenance와 Audit

본 절 전체는 `proposed / future architecture`다.

### 12.1 원칙

**어떤 사용자 응답이 어떤 Knowledge Artifact 버전을 사용했는지 추적 가능해야 한다**(MUST — 도입 시). 이는 상위 ADR §9.2가 정의한 "PostgreSQL이 해결하는 별도 문제" 중 감사 요구의 구체화다.

### 12.2 최소 기록 후보

```text
- response/request trace id
- applied lens key
- artifact version
- content hash 또는 식별 가능한 hash
- PolicyGuard action code
- validator pass/fail
- Runtime build/projection version
- 처리 시각
```

### 12.3 Audit 기록 금지 항목 (Exposure Policy 계승)

다음은 Audit에 기록하지 않는다(MUST NOT — decided / inherited contract).

```text
- raw Markdown 전체
- full Prompt
- unsafe draft
- red-team 원문
- 사용자 투자 일지 원문 전체
- secret
```

**Audit 가능성 ≠ 원문 보관**

```text
Audit가 요구하는 것
= "이 응답은 version X, hash Y의 artifact 집합으로 생성되었다"는 사실

Audit가 요구하지 않는 것
= version X 원문의 로그·감사 저장소 복제
```

원문 재확인이 필요하면 hash로 식별된 immutable version을 **Control Plane의 통제된 경로**(Exposure Policy §7 break-glass)로 조회한다. 감사 저장소에 원문을 중복 보관하는 순간 노출 표면이 하나 늘어난다.

---

## 13. Exposure Policy 적용

PostgreSQL Projection에도 기존 Exposure Policy가 **그대로** 적용된다(decided / inherited contract). 저장 매체 전환은 등급을 재분류할 권한을 만들지 않는다.

### 13.1 등급별 Projection 적용

| 등급 | Projection 단계 적용 원칙 |
|---|---|
| `raw-restricted` | Runtime에 반드시 필요한 최소 내부 block만 projection 가능(MUST). Public API·Frontend 비노출(MUST NOT). 운영 UI 원문 자동 노출 금지(MUST NOT). Red-team·unsafe draft·전체 Prompt는 서빙 projection에 기본 미포함(§9.3) |
| `internal-controlled` | Runtime 또는 승인된 운영 도구만 접근(MUST). 사용자 응답 비노출(MUST NOT) |
| `public-safe` | 별도 승인된 public artifact만 공개(MUST). projection 존재가 공개 승인을 대체하지 않음 |
| `sanitized-runtime-output` | Runtime **결과**이며 Knowledge 원문 Projection과 구분(MUST). projection row가 아니라 응답 파이프라인의 산출물 |
| `operational-metadata` | 버전·hash·rollout·audit 상태. 본문과 분리 관리(MUST), 사용자 응답 기본 비노출(MUST NOT) |

### 13.2 저장소 단위 등급 금지

- **하나의 PostgreSQL 저장소에 있다고 해서 모든 row가 동일한 접근 등급을 갖는다고 가정하지 않는다**(MUST NOT). 접근 등급은 저장소가 아니라 **artifact의 exposure class**를 따른다.
- projection이 원본 artifact에서 파생되면 원본의 exposure class를 **상속**하며, 파생 과정에서 등급이 낮아지지 않는다(MUST NOT — Exposure Policy §12의 등급 하향 금지 계승).
- WAS의 DB 계정이 모든 원문 row를 무제한 조회 가능한 구조는 상위 ADR §10.2가 경고한 "DB가 자동으로 보안이 되지 않는" 대표 사례이므로, 도입 시 접근 범위 최소화를 요구사항으로 둔다(future implementation requirement).
- 구체적인 RLS(row-level security)·DB role 설계는 본 문서의 범위가 아니다(not implemented).

---

## 14. 실패 모드와 안전 동작

본 절 전체는 `proposed / future architecture`다.

### 14.1 공통 안전 원칙

모든 실패에 다음 원칙을 적용한다.

```text
- 잘못된 새 version을 사용하지 않는다.                  (MUST NOT)
- 기존 검증된 active version을 우선 유지한다.           (MUST)
- raw source를 사용자 응답으로 대체 반환하지 않는다.     (MUST NOT)
- 부분 projection을 active로 승격하지 않는다.           (MUST NOT)
- 안전한 오류(사용자용)와 운영 경보(내부용)를 분리한다.  (MUST)
- PolicyGuard와 Output Validator를 우회하지 않는다.     (MUST NOT — 예외 없음)
```

### 14.2 fail-open / fail-closed 구분 원칙

축을 먼저 정의한다.

```text
fail-open  (가용성 우선)
= 실패에도 불구하고 "이미 검증된 기존 상태"로 서비스를 계속한다

fail-closed (안전 우선)
= 실패 시 해당 동작을 중단하고 안전한 오류로 종결한다
```

구분 기준:

```text
새 버전 승격·배포 상태 변경   → 항상 fail-closed
사용자 서빙 (검증된 active 존재) → last-known-good으로 fail-open
사용자 서빙 (검증된 active 부재) → fail-closed (안전한 오류)
원문 노출·안전 검사 관련        → 항상 fail-closed
```

- fail-open은 **검증된 과거 상태의 재사용**만 의미한다. 미검증 데이터로 계속 서빙하는 것은 fail-open이 아니라 정책 위반이다.

### 14.3 실패 모드별 동작

| 실패 상황 | 분류 | 안전 동작 |
|---|---|---|
| Markdown validation 실패 | fail-closed (신규 버전) | Projection 생성·candidate 등록 금지. 기존 active 유지. 실패 원인 기록 |
| dependency graph 불완전 | fail-closed (신규 버전) | 해당 artifact 집합 전체의 승격 중단 (부분 승격 금지). 기존 active 유지 |
| Projection build 실패 | fail-closed (신규 버전) | 중간 산출물 폐기. 기존 active 유지. build 실패는 서빙에 영향을 주지 않아야 함 |
| PostgreSQL 연결 실패 | 서빙: fail-open (조건부) | 검증된 last-known-good active projection(로컬 캐시 등)이 있으면 그것으로 서빙 지속 + 경보. 없으면 안전한 오류. **raw Markdown 임의 파싱으로의 즉석 폴백 금지** |
| active pointer 없음 | fail-closed | 안전한 오류 + 운영 경보. 임의 버전 추정 선택 금지 |
| version/hash 불일치 | fail-closed (해당 artifact) | 불일치 artifact 사용 중단. 일치하는 last-known-good 있으면 사용, 없으면 안전한 오류 |
| 다중 WAS cache 불일치 | 서빙: fail-open + 교정 | 각 인스턴스는 자신이 가진 검증본으로 서빙 지속. hash 기준 탐지 → cache refresh → 미교정 인스턴스 경보 |
| canary 실패 | fail-closed (신규 버전) | canary 중단, 트래픽을 기존 active로 복귀. 실패 버전은 `rejected` |
| rollback 실패 | fail-closed (상태 변경) | 부분 전환 상태로 방치 금지. 전환 전 상태로 복원 시도 + 최고 수준 경보. pointer가 미검증 상태를 가리키게 두지 않음 |
| audit 기록 실패 | 이원화 | **서빙 경로**: 응답 반환은 지속(fail-open) + 경보 (감사 실패가 사용자 장애로 전이되지 않게). **Control Plane 상태 변경**: audit 기록 불가 시 변경 자체를 중단(fail-closed — 감사 없는 배포 상태 변경 금지) |

---

## 15. PostgreSQL 도입 트리거

상위 Storage ADR §11의 트리거를 **그대로 계승**한다(decided / inherited contract). 본 문서는 트리거를 추가·완화하지 않는다.

```text
- 다중 WAS 간 Knowledge version 불일치
- 문서 변경마다 전체 WAS 재배포가 병목
- 특정 Lens/Policy 독립 교체·롤백 필요
- candidate/canary/active 상태 필요
- 운영 중 routing enable/disable 필요
- dependency/fixture 관계 검증이 파일 방식으로 불안정
- content hash와 배포 version 추적 필요
- 응답별 Knowledge version audit 필요
- 여러 서비스가 동일 Knowledge Pack 공유
- 작성자·검수자·배포자 역할 분리
```

판단 원칙:

```text
트리거가 문서에 존재한다는 이유만으로 구현을 시작하지 않는다.

트리거가 실제 운영 문제로 발생하고,
파일 기반 방식의 비용과 위험이
DB 도입 복잡도보다 커지는 시점에 시작한다.
```

- 기준은 **예측이 아니라 발생**이다 (상위 ADR §11). 본 미래 문서의 존재 자체도 착수 근거가 아니다.
- 트리거가 발생하더라도 도입 형태는 본 문서의 Runtime Projection 구조뿐이며, 원문 DB 이전이나 이중 Source of Truth 전환은 트리거와 무관하게 채택하지 않는다(MUST NOT).

---

## 16. 단계적 도입 계획

Phase 1~5 전체는 `proposed / deferred`이며 현재 구현 완료가 아니다. 각 단계는 아키텍처 단계이지 구현 상세가 아니다.

| Phase | 내용 | 진입 조건 | 종료 조건 | 상태 |
|---|---|---|---|---|
| **Phase 0** — 현재 MVP | Markdown + WAS private resource + sanitized API | — (현재 상태) | §15 트리거의 실제 발생 + §20 조건 충족 | decided / current |
| **Phase 1** — Validation 강화 | metadata / dependency / exposure 검증과 content hash 생성 (파일 기반) | 트리거 발생 확인, metadata 기준 안정화 착수 | 전체 문서가 §8 검증을 통과하고 content hash가 재현 가능하게 생성됨 | proposed / deferred |
| **Phase 2** — File-based Projection | 검증된 immutable runtime artifact 생성 (아직 DB 없음) | Phase 1 종료 | Runtime이 검증된 file projection만 로딩 (원문 직접 파싱 경로 제거) | proposed / deferred |
| **Phase 3** — PostgreSQL Registry 도입 | version / relationship / deployment state / audit 저장 | Phase 2 종료 + 다중 WAS·rollout 계열 트리거의 실측 확인 | projection·버전·상태가 Registry에 기록되고 적재가 자동화됨 | proposed / deferred |
| **Phase 4** — Runtime 전환 | Finance Runtime이 active Projection을 Registry 기준으로 조회 | Phase 3의 Registry가 안정 운영됨 | 전 인스턴스가 동일 active version 조회 + response provenance 기록 | proposed / deferred |
| **Phase 5** — Rollout 고도화 | candidate / canary / rollback / multi-service 공유 | Phase 4 종료 | canary·rollback 운영 절차 정착, Knowledge Pack 공유 검증 | proposed / deferred |

단계 설계의 의도:

- **Phase 1~2는 PostgreSQL 없이 수행 가능하다.** 검증·hash·immutable projection은 파일 기반으로 먼저 성립해야 하며, 이것이 없으면 DB 도입은 "검증 안 된 데이터의 저장소 이동"에 불과하다.
- 각 Phase는 이전 Phase의 종료 조건을 만족해야만 진입한다. Phase 건너뛰기(예: 검증 없이 Phase 3 직행)는 §5.3 금지 구조로 퇴행할 위험이 크므로 허용하지 않는다(SHOULD NOT).
- Phase 진행 중에도 §2의 상위 결정과 Exposure Policy는 동일하게 적용된다.

---

## 17. 개발 하네스와의 관계

`finance-harness-knowledge-architecture-v2-handoff.md`를 참고 문서로 사용하되, **Finance 구조와 개발 하네스 구조를 합치지 않는다**(MUST NOT).

### 17.1 일반화 가능한 원칙 (개발 하네스와 공유 가능한 패턴)

```text
- Authoring Source / Runtime Projection 분리
- generated artifact 직접 수정 금지
- metadata validation
- dependency validation
- exposure contract
- optional runtime registry adapter
```

### 17.2 Finance 전용으로 유지하는 것 (Core 일반화 금지)

```text
- Lens
- PolicyGuard
- Finance intent/action
- Finance Fixture
- Finance Output Validator
- Finance PostgreSQL Projection
```

### 17.3 구조적 유사성의 한계

개발 하네스의 `SKILL.md → metadata → skill-index.json → routing hook` 흐름은 Knowledge Artifact projection의 작은 선례지만, 성격이 다르다.

```text
Development Harness projection
= 로컬 skill routing discovery용 file projection

Finance v2 projection
= 운영 runtime / control plane projection
  (버전·배포 상태·감사·다중 인스턴스 일관성)
```

- 두 구조를 지금 통합하면 Finance domain 의미(lens key, intent, fixture)가 개발 하네스 Core로 새어 들어간다 (handoff §10~§11). 통합하지 않는다.
- 개발 하네스 CLI의 Local/Remote Premium 실행 구조는 본 문서의 범위가 아니다. 배포 모델 차이(웹 서비스 vs CLI 배포형)는 상위 ADR §13이 소유한다.

---

## 18. 현재 상태와 미래 제안 구분

결정 완료와 구현 완료를 동일하게 취급하지 않는다.

```text
decided / inherited contract        (확정 — 본 문서가 계승)
- Markdown이 의미의 Source of Truth
- PostgreSQL은 Projection / 운영 상태만 소유
- 이중 Source of Truth 금지 (§5.3 — 영구 금지)
- Exposure Policy 계승 (§2.2, §13)
- PostgreSQL은 MVP 미도입

proposed / future architecture      (합의된 미래 구조 — 구현 아님)
- Validation Pipeline               (§8)
- Projection Build                  (§9)
- Artifact Registry                 (§6.1)
- Version / Hash                    (§6.2)
- Dependency Graph                  (§6.3)
- Candidate / Canary / Active       (§7, §10)
- Response Provenance               (§12)
- Rollback / Audit                  (§10.3, §12)

not implemented                     (본 문서가 설계하지 않는 것)
- PostgreSQL schema
- migration
- ingestion pipeline
- registry adapter
- runtime DB reader
- rollout controller
- cache invalidation
- RBAC / RLS
```

- `proposed / future architecture` 항목은 본 문서 어디에서도 완료된 것으로 취급하지 않는다.
- `not implemented` 항목은 §15 트리거 발생 후 도입 결정 시점의 별도 설계 문서가 소유한다.

---

## 19. 비목표

본 문서는 다음을 하지 않는다.

```text
- 지금 PostgreSQL을 도입
- Markdown 원본을 DB로 완전히 이전
- Markdown과 DB를 동시 편집
- Finance Lens 의미 변경
- PolicyGuard 의미 변경
- Runtime Contract 변경
- Fixture expected behavior 변경
- 구체적인 PostgreSQL table/column/index 설계
- migration SQL
- API endpoint 설계
- Auth/Billing
- Frontend 화면
- 특정 cache 또는 message broker 선택
- 특정 cloud provider 선택
- 개발 하네스 Hosted Premium 구현
```

---

## 20. 구현 시작 조건

다음 조건이 **전부** 충족되기 전에는 구현을 시작하지 않는다.

```text
- 상위 ADR의 도입 트리거 중 하나 이상이 실제 운영 문제로 확인됨
- 현재 파일 기반 방식의 한계가 측정됨 (체감이 아니라 측정)
- Knowledge Artifact metadata 기준이 안정화됨
- Exposure classification이 모든 문서에 적용됨
- dependency와 fixture relation이 정리됨
- validation pipeline 요구사항이 합의됨
- rollback과 audit 요구사항이 확정됨
- 운영 책임자와 승인 절차가 정해짐
```

- **조건이 충족되지 않으면 현재 MVP 구조(Markdown + WAS private resource + sanitized API)를 유지한다.**
- 조건 일부만 충족된 상태의 부분 착수(예: metadata 기준 없이 Registry 먼저 구축)는 §16의 Phase 건너뛰기와 동일한 위험을 만들므로 시작하지 않는다.

---

## 21. 최종 아키텍처 결정

```text
Markdown
= 의미의 Source of Truth (영구)

PostgreSQL
= 검증된 Runtime Projection과 운영 상태의 소유자 (도입 시)

전환 흐름
= Markdown 원본 → Validation → Immutable Projection
  → Registry / Control Plane → Finance Runtime
  → Sanitized API Response → Frontend (단방향, 역류 없음)
```

1. **소유권은 영구적으로 분리된다.** Markdown은 의미를, PostgreSQL은 운영 상태만 소유한다. 사람이 DB에서 의미를 수정하는 이중 Source of Truth 구조는 어떤 단계·어떤 긴급 상황에서도 채택하지 않는다(MUST NOT).
2. **Projection은 검증된 불변 버전이다.** deterministic build, content hash, immutable version이 성립하지 않으면 candidate조차 될 수 없으며, 실패한 빌드는 항상 기존 active version 유지로 귀결된다.
3. **배포는 상태 전환이다.** activation·canary·rollback·disable은 전부 pointer와 deployment state의 전환이며, projection 내용의 수정이 아니다.
4. **Exposure Policy는 저장소와 무관하게 적용된다.** projection row는 원본 artifact의 exposure class를 상속하고, raw document API 금지·redaction·break-glass 통제는 DB 도입 이후에도 동일하다.
5. **실패의 기본값은 기존 검증 상태의 보호다.** 새 버전 승격은 fail-closed, 사용자 서빙은 검증된 last-known-good에 한한 fail-open이며, PolicyGuard와 Output Validator는 어떤 실패 모드에서도 우회하지 않는다.
6. **도입은 트리거의 실제 발생에만 의존한다.** 본 문서의 존재는 착수 근거가 아니며, §20 조건이 전부 충족되기 전까지 Finance Harness는 현재 MVP 구조를 유지한다.

본 문서는 §15 트리거 발생 시 도입 설계의 기준 문서로 사용하며, 상위 ADR 또는 Exposure Policy가 개정되는 경우 그에 맞춰 재검토한다.
