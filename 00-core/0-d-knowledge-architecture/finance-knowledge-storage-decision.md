# Finance Knowledge Storage Decision

## 0. Metadata

```text
status            : decided v1.0 — 0-D Knowledge Architecture 첫 문서
document type     : Architecture Decision Record (ADR)
scope             : Finance Harness 금융 Knowledge Artifact
                    (Lens, PolicyGuard, Routing Contract, Runtime Contract,
                    Fixture, Output Validator, Internal Prompt Template)의
                    MVP 저장 위치, Runtime 로딩 경계, Frontend 비노출 원칙,
                    PostgreSQL 도입 유예 결정.
decision owner    : Finance Harness Knowledge Architecture
follow-up         : finance-document-exposure-policy.md
                    (노출 등급·금지 규칙 상세 — 본 문서에서 생성하지 않음, 후속 작업)
non-goals         :
  - Finance Lens 본문·Runtime Contract·PolicyGuard 의미의 수정
  - PostgreSQL schema / migration / ingestion 구현
  - Auth/Billing, Frontend, Hosted Control Plane 설계
```

- 충돌 시 우선순위: 본 문서는 저장 위치와 노출 경계만 결정한다. intent/action/lens/redirect/validator의 의미 정의는 각 canonical 문서(`00-service-policy.md`, `finance-policyguard-rules.md`, `finance-routing-contract.md` 등)가 소유하며, 본 문서는 그 의미를 재정의하거나 완화하지 않는다.

---

## 1. 문서 목적

본 문서는 Finance Harness의 금융 Knowledge Artifact를 **MVP에서 어디에 저장하고, Runtime이 어떻게 읽으며, Frontend에 무엇까지 노출할 것인가**를 잠그는 Architecture Decision Record다.

핵심 질문은 다음 하나다.

```text
Finance Harness의 Lens, PolicyGuard, Runtime Contract,
Fixture, Validator, Prompt 관련 Markdown 문서를

MVP에서 PostgreSQL Knowledge DB로 이전할 것인가,
아니면 WAS 내부 private resource로 유지할 것인가?
```

이 문서는 미래 가능성을 나열하는 탐색 문서가 아니다. **현재 MVP의 결정을 명확히 잠그고**, 그 결정의 근거·한계·재검토 조건을 함께 기록하는 결정 문서다.

### 1.1 이 문서가 결정하는 것

```text
- Finance Markdown의 MVP 저장 위치
- Authoring Source of Truth
- WAS 내부 로딩 경계
- Frontend 비노출 원칙
- PostgreSQL 도입을 미루는 이유
- 미래 DB 검토 조건 (도입 트리거)
```

### 1.2 이 문서가 수정하지 않는 것

본 문서는 Finance Lens 본문이나 Runtime Contract의 의미를 수정하지 않는다. 다음은 본 문서의 결정 대상이 아니며, 재정의하면 그 자체가 계약 위반이다.

```text
- lens key
- trigger 의미
- forbidden intent
- redirect 의미
- fixture expected behavior
- PolicyGuard action 의미
- Output Validator 의미
- 기존 Runtime Contract
```

---

## 2. 결정 요약

결론을 먼저 제시한다.

```text
Finance Harness MVP
= Markdown Authoring Source of Truth
+ WAS private resource
+ Finance Runtime 내부 로딩
+ Sanitized API Response
```

구체적으로 다음을 결정한다.

- Markdown은 사람이 작성하고 리뷰하는 **Authoring Source of Truth**다.
- MVP에서는 Markdown 문서를 **PostgreSQL로 이전하지 않는다**.
- 금융 Knowledge 문서는 Frontend가 아니라 **서버 내부에서만** 읽는다.
- Markdown 원문을 Frontend bundle, static/public resource, CDN, 사용자 다운로드 artifact에 **포함하지 않는다**.
- 일반 사용자에게 **raw Markdown 조회 API를 제공하지 않는다**.
- Frontend에는 최종 답변, 적용 Lens key, 정제된 체크리스트, 투자 일지 질문, 사용자 고지 등 **sanitized output만** 반환한다.
- PostgreSQL Knowledge DB는 **현재 MVP 필수 구성요소가 아니다**.
- 향후 운영 복잡도가 실제로 발생할 경우, PostgreSQL을 **Runtime Projection 또는 Control Plane**으로 검토한다 (§11).

이 결정은 PostgreSQL을 포기한다는 뜻이 아니다. Frontend·일반 API로부터의 원문 격리는 WAS private resource만으로 달성 가능하므로, PostgreSQL 도입을 **운영 복잡도 해결이라는 본래 문제**가 실제로 나타나는 시점으로 미루는 것이다.

---

## 3. 현재 문제

Finance Harness의 문서는 단순 설명 문서가 아니라 **실행 의미를 가진 Knowledge Artifact**로 성장했다. 각 문서는 lens key, trigger, forbidden intent, redirect, fixture 연결, PolicyGuard 연결, Output Validator 체크포인트 같은 실행 계약을 담는다. 이 중 상당수는 문서 자체에 internal-only가 명시되어 있다 (예: Red-team 케이스의 user_input/unsafe_draft, 출력 예시의 나쁜 응답 예시, 정합성 검토 보고서).

이 상황에서 세 가지 질문이 뒤섞여 제기되었다.

1. **저장 질문** — 이 문서들을 파일로 둘 것인가, DB로 옮길 것인가?
2. **노출 질문** — Frontend와 사용자에게 원문이 새어 나가는 것을 어떻게 막을 것인가?
3. **운영 질문** — 버전 일관성, 부분 롤백, 상태 관리, 감사는 어떻게 할 것인가?

문제의 핵심은, 2번(노출)을 해결하기 위한 수단으로 1번(DB 이전)이 검토되었다는 점이다. 본 문서는 이 둘을 분리한다. **노출 통제는 저장 매체가 아니라 경계 설계의 문제**이며(§9, §10), DB는 3번(운영) 문제가 실제로 발생했을 때의 수단이다.

---

## 4. 검토한 저장 방식

| 대안 | 역할 | 판단 |
|------|------|------|
| A. Markdown + WAS private resource | 사람이 작성·리뷰·diff하는 원본을 서버 내부 리소스로 패키징 | **채택 (MVP)**. Frontend 격리 요구를 충족하며 배포·리뷰가 단순하다. 관계 질의·부분 롤백·감사에는 한계가 있으나 현재 규모에서는 문제가 아니다. |
| B. PostgreSQL Knowledge DB 즉시 이전 | 문서 원문을 DB로 옮겨 저장·조회 | **기각 (MVP)**. Frontend 비노출은 A만으로 달성되므로 도입 동기가 성립하지 않는다. 지금 도입하면 schema·migration·ingestion·권한 관리 비용만 추가되고, 노출 통제는 자동으로 개선되지 않는다 (§9, §10.2). |
| C. Obsidian / file graph 도구 | 문서 관계 시각화, authoring 보조 | **보조 도구로만**. runtime 저장소나 운영 control plane으로는 부적합하다. |
| D. SQLite / local index | 로컬 검증·검색용 소형 인덱스 | **보류**. 단일 로컬 인덱스로는 다중 WAS 운영·rollout·audit 요구를 처리하지 못한다. 향후 validator 보조 수단으로는 검토 가능하다. |
| E. Markdown Source + PostgreSQL Runtime Projection | Markdown을 의미의 원본으로 유지하고, 검증된 runtime projection만 DB에 생성 | **연기 (향후 방향)**. §11 트리거가 실제로 발생하면 이 구조로 도입한다. 이중 Source of Truth 위험을 피하는 유일한 DB 도입 형태다 (§12). |

명시적으로 기각한 구조가 하나 더 있다. **사람이 Markdown과 PostgreSQL 데이터를 각각 수정하는 이중 Source of Truth** 구조다. 이 구조에서는 어느 쪽이 최신인지 알 수 없어 문서의 의미와 실행 상태가 분리되며, 어떤 트리거가 발생하더라도 채택하지 않는다.

---

## 5. MVP 최종 결정

### 5.1 결정

대안 A를 채택한다. MVP에서 Markdown은 다음 두 역할을 함께 수행한다.

```text
- Authoring Source of Truth
- WAS 내부 Knowledge Source
```

문서 변경은 Git diff와 리뷰로 관리하고, 애플리케이션 배포와 함께 반영한다.

### 5.2 MVP 런타임 구조

```text
[Private Finance Markdown]

- Lens
- PolicyGuard
- Routing Contract
- Runtime Contract
- Fixture
- Output Validator
- Internal Prompt Template

            ↓

[WAS Private Resources]

            ↓

[Finance Runtime]

- Intent 판단
- Lens 선택
- Policy 적용
- Output Validation
- Sanitization

            ↓

[Public API Response]

- answer
- applied lens keys
- sanitized checklist
- journal questions
- notices

            ↓

[Frontend]
```

이 구조에서 **원문 문서는 어떤 단계에서도 Frontend로 직접 전달되지 않는다**. Frontend에 도달하는 것은 Finance Runtime의 Sanitization을 거친 Public API Response뿐이다.

---

## 6. Markdown Authoring Source 원칙

저장 결정을 정확히 이해하려면 다음 세 개념을 구분해야 한다. 이 셋을 혼합하는 것이 이번 검토에서 혼선의 원인이었다.

```text
1. Authoring Source
   사람이 문서의 의미와 본문을 작성하는 원본

2. Runtime Storage / Projection
   실행 시 조회하기 위한 배포본 또는 투영본

3. Exposure Boundary
   Frontend와 사용자에게 어떤 정보까지 전달하는지에 대한 경계
```

MVP에서는 Markdown이 1과 2를 겸한다(§5.1). 그러나 이는 편의상의 겸직이지 개념의 통합이 아니다.

향후 PostgreSQL이 도입되더라도 다음 원칙은 변하지 않는다.

- Markdown은 **의미의 Authoring Source**로 유지한다. lens key, trigger 의미, forbidden intent, redirect 관계, fixture 연결 같은 실행 의미는 문서가 소유한다.
- PostgreSQL은 **검증된 Runtime Projection 또는 운영 Control Plane**으로만 사용한다. 배포 버전, content hash, candidate/canary/active 상태, routing enable 여부 같은 운영 상태는 DB가 소유한다.
- 의미를 DB에서 사람이 직접 수정하는 순간, 이중 Source of Truth가 되어 §4에서 기각한 구조로 퇴행한다.

그리고 3(Exposure Boundary)은 1·2와 독립적인 축이다. 원본이 파일이든 DB든, 노출 경계는 별도로 설계하고 별도로 검증해야 한다 (§8, §10).

---

## 7. WAS Private Resource 경계

MVP에서 Finance Markdown은 WAS의 private resource로 패키징한다. 이 경계의 의미는 다음과 같다.

```text
WAS private resource는
Frontend, 일반 사용자, 일반 API로부터
Markdown 원문을 격리하는 MVP 경계다.
```

패키징 규칙:

- Markdown은 서버 애플리케이션 내부 리소스로만 포함하며, Finance Runtime만 읽는다.
- 웹 서버의 static/public 경로, Frontend build 산출물, CDN 배포 대상에 포함하지 않는다.
- 문서 파일의 서버 내 경로를 응답·오류 메시지·클라이언트 로그에 노출하지 않는다.
- 문서 변경은 코드와 동일하게 Git 리뷰를 거쳐 애플리케이션 배포로 반영한다.

이 경계는 §10에서 기술하듯 완전한 침해 방어 수단이 아니다. 그러나 MVP가 실제로 막아야 하는 노출 경로 — Frontend bundle 유출, 공개 API 조회, 정적 리소스 크롤링 — 에 대해서는 충분하고 검증 가능한 경계다.

---

## 8. Frontend와 API 노출 경계

### 8.1 Frontend에 전달 가능한 것

Frontend에는 Finance Runtime의 Sanitization을 통과한 출력만 전달한다.

```text
- 최종 답변 (answer)
- 적용 Lens key (applied lens keys)
- 정제된 체크리스트 (sanitized checklist)
- 투자 일지 질문 (journal questions)
- 사용자 고지 문구 (notices)
```

### 8.2 금지 항목

다음은 어떤 경로로도 Frontend·일반 사용자에게 전달하지 않는다.

- Markdown 원문 전체 또는 부분 (Frontend bundle, static/public resource, CDN, 다운로드 artifact 포함)
- raw Markdown 조회 API
- PolicyGuard 규칙 원문, Routing Contract 원문, Output Validator 규칙 원문
- Red-team 케이스 (user_input, unsafe_draft, forbidden_output 포함)
- 내부 Prompt Template 전체
- 오류 메시지·일반 로그를 통한 문서 원문 또는 서버 파일 경로
- debug/admin endpoint를 통한 raw document·internal metadata 전체 노출

### 8.3 후속 문서 연결

노출 등급의 상세 분류(예: raw / internal / private-resource / public-safe / sanitized-runtime-output)와 등급별 금지 규칙은 후속 문서 `finance-document-exposure-policy.md`에서 다룬다. 본 문서는 그 문서의 상위 결정 — "sanitized output만 나간다" — 만 잠근다. 해당 후속 문서는 본 작업 범위에 포함되지 않으며 아직 작성되지 않았다.

---

## 9. PostgreSQL을 지금 도입하지 않는 이유

기각 논리의 핵심은 **문제와 수단의 매칭**이다. MVP가 지금 풀어야 하는 문제와 PostgreSQL이 실제로 푸는 문제는 서로 다른 집합이다.

### 9.1 PostgreSQL이 없어도 해결 가능한 MVP 문제

```text
- Frontend bundle로부터 Markdown 원문 분리
- 일반 API에서 raw document 비노출
- 서버 내부 Runtime만 문서 접근
- 문서 변경을 애플리케이션 배포와 함께 관리
- 소규모 문서 집합의 Git diff와 review
```

이 목록이 MVP의 실제 요구 전부이며, 전부 WAS private resource + sanitized API로 해결된다.

### 9.2 PostgreSQL이 해결하는 별도 문제

```text
- 다중 WAS 간 버전 일관성
- 특정 Knowledge Artifact만 교체 또는 롤백
- candidate / canary / active 상태
- runtime enable / disable
- dependency graph 질의
- content hash와 배포 버전 관리
- 어떤 응답이 어떤 문서 버전을 사용했는지 감사
- 여러 서비스가 같은 Knowledge Pack을 공유
```

이 목록은 운영 규모가 커졌을 때의 문제이며, 현재 MVP에서는 아직 발생하지 않았다. 발생하지 않은 문제를 위해 schema 설계, migration, ingestion, DB 권한 관리라는 실비용을 지금 지불할 이유가 없다.

### 9.3 결론

```text
Frontend 노출을 막기 위해 PostgreSQL을 도입할 필요는 없다.

PostgreSQL은 보안 은닉 수단이 아니라,
향후 버전·배포·관계·감사·운영 상태를 관리하기 위한
Runtime Projection / Control Plane 후보이다.
```

---

## 10. 현재 구조의 한계

채택한 구조의 한계를 과장 없이 기록한다. 이 절은 §11 도입 트리거와 후속 노출 정책 문서의 근거가 된다.

### 10.1 WAS private resource는 침해 방어 경계가 아니다

```text
WAS 자체가 완전히 침해된 경우에도
Markdown 원문을 보호하는 독립 보안 경계는 아니다.
```

WAS private resource는 Frontend·일반 사용자·일반 API로부터의 격리 경계일 뿐이다. 서버가 장악되면 서버가 읽을 수 있는 모든 리소스는 함께 노출된다. 이 한계를 "private resource니까 안전하다"로 과장하지 않는다.

### 10.2 DB 저장이 자동으로 보안이 되는 것도 아니다

```text
문서를 PostgreSQL에 저장한다고 자동으로 안전해지는 것은 아니다.
```

다음 조건 중 하나라도 잘못되면, DB를 사용해도 원문은 동일하게 노출된다.

- WAS 계정이 모든 원문을 무제한 조회할 수 있는 경우
- raw document API를 제공하는 경우
- 오류나 로그에 원문을 출력하는 경우
- debug/admin endpoint에서 전체 문서를 노출하는 경우
- Prompt 또는 Policy 원문을 최종 응답에 포함하는 경우
- DB 권한이 과도하게 부여된 경우

즉, 저장 매체를 파일에서 DB로 바꾸는 것은 노출 문제를 이동시킬 뿐 해결하지 않는다.

### 10.3 저장 매체보다 중요한 것

노출 통제의 실질은 다음 다섯 가지에 있다.

```text
- packaging boundary
- access control
- API response contract
- logging/redaction
- debug/admin exposure
```

이 다섯 축의 상세 노출 등급과 금지 규칙은 후속 문서 `finance-document-exposure-policy.md`가 소유한다 (§8.3).

### 10.4 운영 측면의 한계

- 다중 WAS 인스턴스 사이의 문서 버전 일관성을 구조적으로 보장하지 못한다 (배포 절차에 의존).
- 특정 Lens 또는 Policy만 runtime에서 교체·비활성화·롤백하기 어렵다 (전체 재배포 필요).
- Lens–PolicyGuard–Fixture–Validator 관계 검증이 파일 기반 도구와 리뷰에 의존한다.
- 어떤 응답이 어떤 문서 hash/version을 사용했는지 감사하기 어렵다.

이 한계들은 현재 규모에서는 수용 가능하며, 수용 불가능해지는 시점이 §11의 도입 트리거다.

---

## 11. PostgreSQL 도입 트리거

다음 조건이 **실제 운영 문제로 발생**할 때 PostgreSQL Runtime Projection 또는 Control Plane 도입을 검토한다. 예측이 아니라 발생이 기준이다.

```text
- 다중 WAS 간 Knowledge 버전 불일치가 발생
- 문서 변경마다 WAS 전체 배포가 운영 병목이 됨
- 특정 Lens 또는 Policy만 독립 교체·롤백 필요
- candidate / canary / active 상태 필요
- 운영 중 routing enable / disable 필요
- dependency와 fixture 관계 검증이 파일 도구만으로 불안정
- content hash와 배포 버전 추적 필요
- 응답별 사용 문서 버전 감사 필요
- 여러 서비스가 같은 Knowledge Pack 공유
- 작성자, 검수자, 배포자 역할 분리
```

요약 기준은 다음 하나다.

```text
파일 배포만으로
버전 일관성, 롤백, 감사, 관계 검증을
안전하게 유지하기 어려워지는 시점에 도입한다.
```

트리거가 발생하더라도 도입 형태는 §12의 원칙을 따르는 Runtime Projection이며, 문서 원본의 DB 이전(§4 대안 B)이나 이중 Source of Truth로의 전환은 트리거와 무관하게 채택하지 않는다.

---

## 12. 미래 Runtime Projection 원칙

미래 구조는 원칙 수준으로만 기록한다. 구체적인 DB schema, migration, ingestion 설계는 본 문서의 범위가 아니며, 도입 결정 시점의 별도 설계 문서가 소유한다.

```text
Private Markdown Authoring Source
→ Metadata / Dependency / Exposure Validation
→ Content Hash
→ Build / Projection
→ Runtime Registry 또는 PostgreSQL Control Plane
→ Finance Runtime
→ Sanitized API
→ Frontend
```

핵심 원칙:

```text
Markdown
= 의미의 Source of Truth

PostgreSQL
= 검증된 Runtime Projection과 운영 상태
```

즉, 문서를 DB로 "이전"하는 것이 아니라 Markdown에서 **검증을 통과한 projection을 생성**한다. 문서가 소유하는 것은 의미(lens key, trigger, forbidden intent, redirect, fixture 연결)이고, DB가 소유하는 것은 운영 상태(배포 버전, content hash, candidate/canary/active, routing enabled, audit)다.

금지 구조:

```text
Markdown에서 문서 의미를 수정
+
PostgreSQL에서 같은 의미를 사람이 별도로 수정
```

이 구조는 어느 쪽이 최신인지 판단할 수 없게 만들며, §4에서 이미 기각했다. 미래의 어떤 단계에서도 허용하지 않는다.

---

## 13. 개발 하네스와의 차이

본 결정을 다른 제품 구조로 일반화하지 않도록 배포 모델의 차이를 기록한다.

```text
Finance Harness
= 웹 서비스
= 서버 내부에 Knowledge 원문 유지 가능
= 결과만 Frontend에 전달

Development Harness
= CLI 배포형
= 로컬에 배포된 Artifact는 사용자가 추출 가능
= 보호가 필요한 Premium Capability는 별도 Hosted 실행 필요
```

Finance Harness는 원문을 서버 안에 남기고 sanitized output만 내보낼 수 있는 구조이므로 "WAS private resource" 경계가 성립한다. 반면 CLI 배포형 제품은 artifact가 사용자 로컬에 존재하므로 같은 경계가 성립하지 않으며, 본 문서의 저장 결정을 그대로 적용할 수 없다.

본 문서는 **Finance Harness의 저장 결정만** 다룬다. 개발 하네스의 Local/Remote 실행 계약, Premium Skill, Auth/Billing, Entitlement는 본 문서의 범위가 아니다.

---

## 14. 현재 상태와 미래 제안 구분

계획을 현재 구현으로 오독하지 않도록 상태를 명시한다.

```text
decided / current
- MVP Markdown Authoring Source
- WAS private resource
- 서버 내부 로딩
- sanitized API response
- PostgreSQL 미도입

proposed / deferred
- PostgreSQL Runtime Projection
- Knowledge Registry
- candidate / canary / active
- runtime rollout
- dependency graph
- version audit

not implemented
- PostgreSQL schema
- migration
- ingestion pipeline
- Hosted Knowledge API
- runtime registry adapter
```

`proposed / deferred`와 `not implemented` 항목은 본 문서 어디에서도 완료된 것으로 취급하지 않는다. §12의 미래 구조 역시 원칙 기록일 뿐 구현 상태가 아니다.

---

## 15. 비목표

본 문서는 다음을 하지 않는다.

```text
- Finance Lens 본문 수정
- Runtime Contract 수정
- PolicyGuard 의미 수정
- Fixture expected behavior 수정
- PostgreSQL schema 설계
- DB migration 작성
- ingestion 구현
- Auth/Billing 설계
- Frontend 구현
- Hosted Control Plane 구현
- 모든 Markdown을 DB로 이전
- WAS private resource를 완전한 침해 방어 수단으로 과장
```

---

## 16. 최종 결정

```text
Finance Harness MVP
= Markdown Authoring Source of Truth
+ WAS private resource
+ Finance Runtime 내부 로딩
+ Sanitized API Response

PostgreSQL Knowledge DB는 MVP에 도입하지 않는다.
```

- Markdown은 의미의 Authoring Source of Truth이며, MVP에서는 WAS 내부 Knowledge Source를 겸한다.
- 금융 Knowledge 원문은 서버 내부에서만 읽으며, Frontend·일반 API·정적 리소스·다운로드 artifact 어디에도 포함하지 않는다.
- Frontend에는 sanitized output(최종 답변, 적용 Lens key, 정제된 체크리스트, 일지 질문, 고지)만 반환한다.
- WAS private resource는 Frontend·일반 사용자·일반 API로부터의 격리 경계이며, WAS 침해까지 방어하는 독립 보안 경계로 과장하지 않는다.
- PostgreSQL은 보안 은닉 수단이 아니라 향후 버전·배포·관계·감사·운영 상태 관리를 위한 Runtime Projection / Control Plane 후보다.
- §11의 도입 트리거가 실제로 발생하는 시점에, §12의 원칙(Markdown = 의미의 Source of Truth, PostgreSQL = 검증된 Projection)에 따라 재검토한다.
- 이중 Source of Truth 구조는 어떤 경우에도 채택하지 않는다.

이 결정은 §11 트리거 발생 또는 노출 사고 발생 시 재검토하며, 그 전까지 Finance Harness의 Knowledge 저장 구조에 대한 유효한 결정으로 유지된다.
