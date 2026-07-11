# Finance Knowledge Artifact Metadata Standard

## 0. Metadata

```text
status                : decided v1.0 — MVP target contract
document type         : Metadata Standard / Knowledge Contract
scope                 : Finance Harness Knowledge Artifact 전체가 공통으로
                        사용할 Metadata 필드, 식별자, 버전, 상태, 의존성,
                        노출 등급 표기, Runtime 사용 가능 여부와 검증 규칙.
decision owner        : Finance Harness Knowledge Architecture
parent decisions      :
  - 00-core/0-d-knowledge-architecture/finance-knowledge-storage-decision.md
  - 00-core/0-d-knowledge-architecture/finance-document-exposure-policy.md
visibility            : internal-only — 본 문서는 exposure policy §4의 보수
                        분류 원칙에 따라 internal-controlled로 취급한다.
implementation status : contract only / repository migration not performed
non-goals             :
  - 기존 문서의 실제 수정, 기존 파일 rename
  - intent/action/lens key/fixture 기대값 변경
  - Runtime 코드, Metadata parser/validator, DB schema, migration, ingestion 구현
```

- 파일명에는 버전을 포함하지 않는다. 내용 버전은 본 Metadata의 status로만 관리한다.
- 충돌 시 우선순위: 저장·로딩 경계는 storage decision이, 노출 등급의 의미와 문서별 기본 등급은 exposure policy가, intent/action/lens/fixture의 의미는 각 canonical 문서(`finance-policyguard-rules.md`, `finance-routing-contract.md`, `finance-routing-fixtures.md`, `finance-lens-catalog.md` 등)가 소유한다. 본 문서는 **표기 방법과 검증 규칙만** 소유하며, 상위 결정과 canonical 의미를 재정의하거나 완화하지 않는다.

---

## 1. 문서 목적

본 문서는 Finance Harness의 모든 Knowledge Artifact가 공통으로 사용할 Metadata 표준을 정의한다. 필드 집합, 영구 식별자, 내용 버전, 생명주기 상태, 구현 상태, 의존성 표기, 노출 등급 표기, Runtime 사용 가능 여부, 그리고 이를 검증하는 규칙을 하나의 계약으로 잠근다.

이 표준이 막아야 하는 혼선은 다음이다. 각 항목은 실제로 현재 문서 집합에서 발생했거나 발생 직전인 문제다.

```text
- 파일명 버전과 내용 버전 혼용
  (예: finance-routing-contract-v1.md 파일명 vs "> 상태: 초안 v0.1" 본문 표기)
- 파일 rename 시 문서 참조 연쇄 파손
  (예: -v1/-v2 suffix 제거 후에도 타 문서가 versioned 파일명을 계속 인용)
- draft와 active 혼용 ("초안"이면서 실제로는 canonical로 사용되는 문서)
- candidate와 routable 혼용 (후보 fixture/lens가 곧바로 라우팅 가능으로 오독)
- 결정 완료와 구현 완료 혼용 (decided 문서를 구현 완료로 오독)
- internal-only 표기와 exposure class 혼용 (자유 서술 표기 vs 등급 체계)
- 문서 경로를 논리 식별자로 사용 (디렉터리 이동 = 식별자 파손)
- 존재하지 않는 파일명 직접 참조 (rename 이후 dangling reference)
- Markdown과 미래 DB projection의 식별자 불일치
```

---

## 2. 상위 결정과 범위

### 2.1 변경 불가한 상위 결정

다음은 parent decisions에서 이미 확정된 결정이며, 본 문서는 이를 다시 논의하거나 변경하지 않는다.

```text
- Markdown은 의미의 Authoring Source of Truth
- MVP는 WAS private resource로 로딩
- Frontend에는 sanitized runtime output만 전달
- PostgreSQL은 MVP에서 미도입
- 파일명은 안정적인 고정 이름 사용
- 내용 버전은 Metadata로 관리
- 변경 이력은 Git으로 관리
- 결정 완료와 실제 구현 완료는 다름
- 기존 intent/action/lens key/fixture 의미는 변경 금지
```

### 2.2 적용 범위

본 표준은 Finance Harness repo의 모든 Knowledge Artifact(정책, 계약, 렌즈, fixture, 템플릿, validator, 아키텍처 문서, 보고서, handoff, 참고 문서, index)에 적용되는 **target standard**다. 기존 문서의 실제 전환(migration)은 §17의 별도 단계 작업이며, 본 문서 작성으로 완료되지 않는다.

### 2.3 다른 식별자 체계와의 관계

본 표준이 정의하는 것은 **문서 단위의 식별·상태·의존성 표기**다. 다음 식별자 체계는 각 canonical 문서가 소유하며, 본 표준은 그 값을 담는 필드만 정의한다.

```text
lens key    : finance-lens-catalog.md 소유 (예: market-context)
intent      : finance-policyguard-rules.md §3 소유 (예: RISK_BUY_SELL)
action      : finance-routing-fixtures.md §3 + policyguard §6 소유
              (예: REDIRECT_TO_CHECKLIST)
fixture ID  : fixture 문서 내부 케이스 식별자 (예: SF-01, RF-04, PI-02)
```

---

## 3. 핵심 원칙

각 개념의 역할을 다음과 같이 고정한다.

```text
filename
= 안정적인 저장 경로

document_id
= 경로와 무관한 영구 논리 식별자

version
= 문서 내용 계약의 버전

Git history
= 변경 이력과 과거 상태

lifecycle_status
= 문서 생명주기

implementation_status
= 실제 코드·Runtime 구현 상태

exposure_class
= 노출 제한 등급

routable
= Router가 선택 가능한지

runtime_loadable
= Runtime이 로딩 가능한지
```

특히 다음을 혼동하지 않는다.

- `candidate`라고 해서 자동으로 routable하지 않다.
- `active`라고 해서 실제 코드 구현이 완료된 것은 아니다 (`lifecycle_status: active` + `implementation_status: contract-only` 조합이 정상적으로 존재한다, §9).
- `decided`라고 해서 loader나 sanitizer가 구현된 것은 아니다.
- `runtime_loadable=true`와 `routable=true`는 다른 개념이다 (§11).
- 파일 경로 변경이 document_id 변경을 의미하지 않는다.
- 문서 내용의 minor 수정이 파일명 변경을 의미하지 않는다.
- 과거 상태의 조회는 Git history의 책임이며, 과거 버전 복사 파일을 만드는 방식이 아니다.

---

## 4. 파일명 규칙

### 4.1 규칙

```text
- lowercase
- kebab-case
- 확장자 .md
- 파일명에 버전 suffix 사용 금지
- `_`(underscore) 사용 금지
- README.md는 대문자 예외
- 정렬 목적의 숫자 prefix는 예외 (예: 00-service-policy.md)
- 현재 문서와 과거 버전 복사본을 동일 폴더에 함께 두지 않음
```

허용 예:

```text
finance-routing-contract.md
finance-routing-fixtures.md
finance-policyguard-alignment.md
finance-lens-market-context.md
```

금지 예:

```text
finance-routing-contract-v1.md
finance-prompt-templates-v2.1.md
finance_harness_document_report.md
finance-routing-contract-final-final.md
```

### 4.2 Release snapshot

고정 release snapshot이 필요한 경우 현재 문서와 분리된 `releases/` 또는 `archive/` 경로를 사용한다. 일반 문서는 snapshot 파일을 현재 계약으로 참조하지 않는다. snapshot은 조회용 사본이며, 의미 수정의 대상이 아니다.

### 4.3 참조 규칙

사람이 읽는 navigation 링크는 파일 경로(상대경로)를 사용할 수 있으나, 계약 의존성은 §12에 따라 document_id로 표기한다. 파일명 참조는 rename 시 patch 갱신 대상이다 (§6).

---

## 5. document_id

### 5.1 형식

경로와 버전에 독립적인 영구 논리 식별자를 다음 형식으로 확정한다.

```text
finance.<type-segment>.<name>
```

- lowercase, 구분자는 `.`(체계)와 `-`(name 내부).
- version, 파일 확장자, 파일 경로를 포함하지 않는다.
- `<type-segment>`는 §7 artifact_type의 지정 segment와 일치해야 한다 (불일치는 validation error).
- `<name>`은 문서의 의미가 드러나는 안정적인 이름이다.

확정 예:

```text
finance.policy.service                    (00-service-policy.md)
finance.policy.policyguard                (finance-policyguard-rules.md)
finance.contract.routing                  (finance-routing-contract.md)
finance.contract.lens-catalog             (finance-lens-catalog.md)
finance.contract.lens-authoring-standard  (finance-lens-authoring-standard.md)
finance.validator.output                  (finance-output-validator.md)
finance.template.prompt                   (finance-prompt-templates.md)
finance.fixture.routing                   (finance-routing-fixtures.md)
finance.fixture.red-team                  (finance-red-team-cases.md)
finance.lens.market-context               (finance-lens-market-context.md)
finance.lens.risk-checklist               (finance-lens-risk-checklist.md)
finance.adr.knowledge-storage             (finance-knowledge-storage-decision.md)
finance.security.document-exposure        (finance-document-exposure-policy.md)
finance.report.policyguard-alignment      (finance-policyguard-alignment.md)
finance.handoff.mvp-implementation        (finance-harness-mvp-implementation-handoff.md)
```

### 5.2 불변 규칙

- 한번 active가 된 document_id는 재사용하지 않는다.
- 파일 rename 또는 디렉터리 이동 시 document_id는 유지된다.
- 의미적으로 다른 문서는 새 document_id를 사용한다.
- deprecated/retired 문서의 document_id를 새 문서에 재사용하지 않는다. 대체 문서는 새 document_id를 갖고 `supersedes`로 관계를 표기한다 (§12).

### 5.3 다른 식별자와의 구분

```text
document_id: finance.lens.market-context   ← 문서의 영구 식별자
lens_key:    market-context                ← catalog가 소유하는 runtime lens 식별자
```

두 값은 연결되지만 같은 식별자 체계가 아니다. lens_key, intent, action, fixture ID는 §2.3의 canonical 문서가 소유하며, document_id의 변경·부여가 이 값들을 변경하지 않는다. 반대로 lens_key가 바뀌는 것은 major 변경(§6)이지 document_id 변경 사유가 아니다.

---

## 6. 내용 버전

### 6.1 형식

Semantic Versioning을 문서 계약에 맞게 사용한다.

```text
version: major.minor.patch
```

- Metadata 값에는 **`v` prefix를 사용하지 않는다** (`version: 1.0.0`, `version: v1.0.0` 금지). 문서 전체에서 이 규칙을 일관되게 사용한다.
- `draft-v0.1`, `v1.0`, `1.0` 같은 혼용 표기는 금지한다. "초안" 여부는 version이 아니라 lifecycle_status(§8)가 표현한다.

### 6.2 변경 등급

**Patch** (호환, 의미 불변):

```text
- 오탈자
- 링크 정정
- 설명 명확화
- 의미 변화 없는 표현 수정
- 파일 경로 참조 갱신 (rename 반영 등)
```

**Minor** (하위 호환 확장):

```text
- 하위 호환되는 절 추가
- 검증 규칙 추가
- 신규 candidate fixture 추가
- 새로운 optional metadata 추가
- 기존 의미를 완화하거나 깨지 않는 보강
```

**Major** (호환 파괴):

```text
- intent/action 의미 변경
- 기존 routing expectation 변경
- 필수 Metadata 필드 제거 또는 의미 변경
- 기존 consumer가 해석할 수 없는 구조 변경
- 기존 lens contract와 호환되지 않는 변경
```

### 6.3 버전과 파일명·참조의 관계

- 파일명에는 version을 넣지 않는다.
- version 변경 시 참조 문서들의 파일명을 바꾸지 않는다. 참조는 document_id 기준이므로 version 변경만으로 깨지지 않는다.
- 다른 문서가 특정 version 계약을 요구해야 하는 경우, 그 요구는 §12 dependency의 version constraint로 명시한다.

### 6.4 Git과의 역할 구분

```text
Metadata version = 계약의 호환성 선언 (consumer가 해석하는 값)
Git commit/tag   = 변경 이력, 과거 상태 조회, release 지점 기록
```

Git tag는 version과 대응시킬 수 있으나(예: tag `finance.contract.routing/1.1.0`), tag 생성 여부가 version의 유효성을 결정하지 않는다. 과거 버전 열람은 Git으로 하며, 과거 버전 복사 파일을 만들지 않는다 (§4.1).

---

## 7. Artifact Type

문서 분류용 표준 enum을 다음 12종으로 정의한다. 이 enum은 문서 분류용이며, Runtime의 intent/action enum이 아니고 그것을 대체하지도 않는다.

기본 exposure class는 exposure policy §4의 문서 유형별 기본 등급표를 따른 제안값이다. **개별 문서의 등급은 exposure policy가 소유**하며, 아래 기본값과 exposure policy §4가 다르면 exposure policy가 이긴다.

| artifact_type | segment | 의미 | runtime_loadable 기본 | routable 가능 | 기본 exposure_class | 필수 추가 필드 | 예시 |
|---|---|---|---|---|---|---|---|
| policy | policy | 서비스·안전 정책 원문 (0-A) | true | 불가 | raw-restricted | authority_level, governed_artifacts, breaking_change_policy | 00-service-policy.md, finance-policyguard-rules.md |
| contract | contract | 실행·작성 계약 (routing, catalog, 표준) | true | 불가 | raw-restricted (0-C) / internal-controlled (0-B lens infra) | authority_level, governed_artifacts, breaking_change_policy | finance-routing-contract.md, finance-lens-catalog.md |
| lens | lens | 질문을 확인 관점 체크리스트로 구조화하는 단위 문서 | true | **가능** (active 시) | internal-controlled | lens_key, trigger, forbidden_intents, redirect_to, fixture_refs, activation_gate | finance-lens-market-context.md |
| fixture | fixture | routing/guard 회귀 기준 케이스 집합 | false (regression 목적 한정 true 가능) | 불가 (항상 false) | raw-restricted | fixture_set, fixture_count, validates | finance-routing-fixtures.md, finance-red-team-cases.md |
| template | template | intent/action별 응답 구조 원문 | true | 불가 | raw-restricted | (없음 — governed_artifacts 선택) | finance-prompt-templates.md |
| validator | validator | 출력 최종 검사 규칙 | true | 불가 | raw-restricted | (없음 — governed_artifacts 선택) | finance-output-validator.md |
| architecture-decision | adr | 아키텍처 결정 기록 (ADR) | false | 불가 | internal-controlled | (없음 — supersedes 선택) | finance-knowledge-storage-decision.md |
| security-policy | security | 노출·보안 계약 | false | 불가 | internal-controlled | authority_level, governed_artifacts | finance-document-exposure-policy.md |
| report | report | 검토·정합성 보고서 (비권위 스냅샷) | false | 불가 | internal-controlled | reviewed_sources, as_of, non_authoritative | finance-policyguard-alignment.md |
| handoff | handoff | 구현·이관 인계 문서 | false | 불가 | internal-controlled | reviewed_sources, as_of, non_authoritative | finance-harness-mvp-implementation-handoff.md |
| reference | reference | 보조 참고 문서 (매핑, 개요) | false | 불가 | internal-controlled | (없음) | finance-harness-reuse-map.md |
| index | index | README, 디렉터리 안내 | false | 불가 | internal-controlled | (없음) | README.md |

비고:

- fixture의 "regression 목적 한정 true"는 exposure policy §4 주석 4와 동일한 의미다: 저장·검증 목적의 접근이며, 사용자 응답 생성 경로 유입이나 user routing 대상을 의미하지 않는다.
- `sanitized-runtime-output`은 artifact_type이 아니다. Runtime 산출물의 분류이며 원본 문서 유형으로 사용하지 않는다 (§10, §11).

---

## 8. Lifecycle Status

문서 생명주기 enum을 다음 5종으로 확정한다. proposed·superseded 같은 추가 상태는 두지 않는다 — proposed는 draft로, superseded는 deprecated + `supersedes` 관계(§12)로 표현하며, 중복 의미의 상태를 만들지 않는다.

| status | 의미 | authoring | runtime_loadable 기본 | routable 기본 | release 포함 | 다음 전이 | 금지 전이 |
|---|---|---|---|---|---|---|---|
| draft | 작성 중. 계약으로 참조 불가 | 자유 수정 | false | false | 불가 | candidate | draft → active (candidate 생략 금지) |
| candidate | 작성 완료, 검증 대기. 계약 후보 | 리뷰 기반 수정 | false (검증 맥락 한정 true 가능) | **false (기본 비활성)** | 조건부 (비활성 상태로만) | active, draft(재작업), retired(폐기) | candidate → deprecated |
| active | 검증 통과, 현행 계약 | 변경 시 version 규칙(§6) 적용 | type 기본값(§7) | lens에 한해 true 가능 | 포함 | deprecated | active → draft |
| deprecated | 대체 예정. 신규 참조·라우팅 금지 | 정정만 | false 권장 | false | 조건부 (전환기 한정) | retired, candidate(재활성 검토) | deprecated → active (재검증 없는 직접 복귀 금지) |
| retired | 종료. Runtime package 제외 | 수정 금지 (archive) | false | false | 제외 | (없음) | retired → 모든 상태 (document_id 재사용 금지, §5.2) |

명시 규칙:

- candidate는 runtime에서 **기본 비활성**이다. candidate lens/fixture가 존재한다는 것과 라우팅 대상이 된다는 것은 다르다.
- **active 전환은 validation gate를 필요로 한다** (§16의 activation 차단 규칙 + lens의 activation_gate 필드). 문서 작성자가 status 값을 active로 바꾸는 행위만으로 활성화되지 않는다.
- retired 문서는 일반 Runtime package에서 제외한다.
- deprecated는 신규 routing 대상이 아니다.
- 과거 문서의 보존(Git/archive)과 현재 Runtime 사용 여부는 별개다.

---

## 9. Implementation Status

lifecycle과 **별개의 축**으로, 실제 Finance Harness repo의 구현 상태를 표현하는 enum을 정의한다. 이 값은 문서 내용의 완성도가 아니라 **코드·Runtime의 실제 상태**를 의미한다.

| status | 의미 |
|---|---|
| contract-only | 계약만 존재. 대응 구현이 없음 (또는 시작 전) |
| repo-verification-required | 구현이 존재한다고 추정·주장되나 repo 실사로 확인되지 않음 |
| partially-implemented | 일부 구현이 repo에서 확인됨 |
| implemented | 계약 대응 구현이 repo에서 확인됨 |
| regression-verified | 구현이 fixture regression까지 통과함 |

규칙:

- 다음 조합은 정상적으로 존재하며 모순이 아니다.

```text
lifecycle_status: active
implementation_status: contract-only
```

(계약으로는 현행이지만 코드는 아직 없다는 뜻이다. storage decision의 "결정 완료 ≠ 구현 완료" 원칙의 필드화다.)

- **문서 작성 결과만으로 `implemented` 또는 `regression-verified`를 설정할 수 없다.** 이 두 값은 repo 실사와 regression 실행 결과가 있어야만 기록한다.
- implementation_status의 상향은 검증 증거(실사 기록, regression 결과)를 동반해야 하며, 하향(구현 제거·회귀 실패 발견)은 즉시 반영한다.

---

## 10. Exposure Metadata

### 10.1 등급의 소유권

노출 등급의 정의는 `finance-document-exposure-policy.md`가 소유한다. 본 표준은 등급을 재정의하지 않고 **참조·표기 방법만** 정의한다. 해당 정책의 canonical 등급은 다음 5종이다.

```text
raw-restricted            (원문 자체가 내부 규칙·공격·Prompt·평가 데이터)
internal-controlled       (내부 운영·검수용, 사용자 직접 제공 불가)
public-safe               (검토를 통과한 공개 가능 "콘텐츠 단위")
sanitized-runtime-output  (Validator 통과 후 정제된 Runtime 산출물)
operational-metadata      (문서 의미가 아닌 운영 상태 정보)
```

### 10.2 어휘 고정 (혼선 차단)

- 노출 등급명은 위 canonical 명칭만 사용한다. `internal-contract` 등 유사 명칭을 등급명으로 사용하지 않는다 (기존 논의·타 문서 초안에서 쓰인 유사 표기는 migration 시 canonical 명칭으로 정정한다 — patch).
- **`private-resource`는 노출 등급이 아니다.** storage decision §7과 exposure policy §5가 정의하는 배포·접근 경계이며, 콘텐츠 민감도 축과 분리해 표기한다 (§10.3).
- 기존 문서의 `internal-only` 자유 서술 표기는 등급이 아니다. migration 시 exposure_class 필드로 정규화한다.

### 10.3 두 축 분리: exposure_class와 distribution_boundary

하나의 필드에 서로 다른 축을 섞지 않기 위해, Metadata 모델에서 다음 두 필드로 분리하는 방식을 채택한다.

```text
exposure_class         = 콘텐츠 민감도 (exposure policy 소유)
distribution_boundary  = 배포·접근 경계 (packaging 축)
```

**exposure_class** — 문서에 부여 가능한 값:

```text
raw-restricted | internal-controlled | public-safe
```

- `sanitized-runtime-output`과 `operational-metadata`는 문서의 exposure_class 값으로 사용할 수 없다 (validation error). 전자는 Runtime 산출물의 분류이고, 후자는 상태 정보의 분류다.
- `public-safe`는 exposure policy §3.3에 따라 **콘텐츠 단위** 등급이다. public-safe 콘텐츠를 담은 원본 파일·metadata·경로는 internal-controlled 이상으로 유지되므로, repo 내 원본 문서의 exposure_class가 public-safe인 경우는 "별도 분리된 public artifact"에 한정된다.

**distribution_boundary** — MVP 값:

```text
repo-only         (repo에만 존재, Runtime package에 포함하지 않음)
private-resource  (WAS private resource로 패키징되어 Finance Runtime이 로딩)
```

- 향후 PostgreSQL projection 도입 시 값 추가(예: hosted-registry)는 minor 확장으로 검토한다. 지금 정의하지 않는다.
- distribution_boundary가 private-resource라고 해서 자동으로 안전한 것이 아니며(exposure policy·storage decision의 한계 명시 승계), exposure_class의 금지 규칙은 boundary와 무관하게 유지된다.

### 10.4 등급 변경

exposure_class의 하향(제한 완화)은 exposure policy §12의 승인 절차를 따르며, Metadata 편집만으로 수행할 수 없다. 상향(보수화)은 문서 소유자가 즉시 할 수 있다.

---

## 11. Runtime Eligibility

### 11.1 필드 정의

세 필드 모두 **boolean**으로 확정한다. enum이 필요할 만큼의 중간 상태는 lifecycle_status와 distribution_boundary가 이미 표현하므로, 여기서 중복 축을 만들지 않는다.

```text
runtime_loadable : Finance Runtime이 이 문서를 로딩 대상에 포함할 수 있는가
routable         : Router가 이 문서를 라우팅 대상으로 선택할 수 있는가
                   (문서의 원칙적 선언 — 운영 활성화가 아님)
public_exposable : 이 문서의 콘텐츠가 공개 산출물로 제공될 수 있는가
                   (선택 필드, 기본값 false)
```

### 11.2 필수 규칙

```text
- routable=true 이면 runtime_loadable=true 여야 한다
- exposure_class=raw-restricted 이면 public_exposable=false
- exposure_class=internal-controlled 이면 원문 기준 public_exposable=false
- artifact_type이 report/handoff이면 기본 routable=false
- lens라도 lifecycle_status=candidate에서는 기본 routable=false
- fixture는 Runtime regression에서 로딩할 수 있으나(runtime_loadable
  조건부 true) user routing 대상이 아니다 (routable 항상 false)
- sanitized-runtime-output은 원본 문서 type/등급이 아니라 Runtime
  산출물 분류이므로, 이 세 필드의 대상이 아니다
```

### 11.3 routable과 routing_enabled의 구분 (중복 정의 금지)

```text
routable        = 문서가 선언하는 "원칙상 라우팅 가능" (Metadata 소유)
routing_enabled = 운영 중 실제 활성 상태 (operational-metadata —
                  Runtime/미래 Control Plane 소유, 문서 Metadata 아님)
```

storage decision §12와 exposure policy §3.5의 소유권 분리를 그대로 승계한다. 따라서 `routing_enabled`, `deployment_status`, `content_hash`, `loaded_at` 같은 운영 상태 필드는 **문서 Metadata에 기록하지 않는다** (validation error, §16). 문서는 routable을 선언할 수 있지만, 운영에서 아직 활성화하지 않을 수 있다.

---

## 12. Dependency Metadata

### 12.1 원칙

문서 간 의존성은 파일명이 아니라 **document_id**를 기준으로 표현한다.

```text
- navigation용 Markdown link와 machine-readable dependency는 다르다
- 사람이 읽는 링크는 상대경로를 사용할 수 있다
- 계약 의존성은 document_id를 사용한다
```

### 12.2 필드와 방향

| 필드 | 방향/의미 | 강제성 |
|---|---|---|
| depends_on | 이 문서가 해석·준수를 위해 필요로 하는 상위/인접 계약. "A depends_on B" = A의 유효성이 B의 계약에 의존 | 강제 (검증 대상) |
| validates | 이 문서(fixture)가 검증하는 대상 문서. fixture → 피검증 문서 방향 | 강제 (fixture 전용) |
| validated_by | 이 문서를 검증하는 fixture 문서. 피검증 문서 → fixture 방향 (validates의 역방향 표기) | 강제 (검증 대상) |
| supersedes | 이 문서가 대체하는 이전 문서. 생명주기 관계이며 **파일 덮어쓰기가 아니다** — 이전 문서는 deprecated/retired로 남는다 | 강제 (생명주기 검증) |
| related_documents | 참고 관계. 검증·차단에 사용하지 않는 비강제 관계 | 비강제 |

### 12.3 표기 형식

```yaml
depends_on:
  - document_id: finance.policy.service
    version: ">=0.1.0 <1.0.0"
  - document_id: finance.policy.policyguard

validated_by:
  - finance.fixture.routing

supersedes: []

related_documents:
  - finance.report.policyguard-alignment
```

- version constraint는 선택이다. 특정 version 계약이 필요할 때만 명시하고, 없으면 "현행 active 버전"을 의미한다.
- constraint 형식은 semver range 표기를 사용한다.

### 12.4 검증 규칙

```text
- 순환 dependency는 탐지 대상이며 error다
- 존재하지 않는 document_id 참조는 validation error다
- version constraint 불일치는 activation block이다
  (참조 대상의 현행 version이 constraint를 벗어나면 활성화 불가)
- related_documents는 위 검증의 대상이 아니다 (존재 확인 warning만)
- supersedes 대상이 active로 남아 있으면 warning
  (전환기), 신규 문서와 동시 active가 장기화되면 정리 대상
```

---

## 13. Artifact별 필수 Metadata

### 13.1 공통 필드

| 필드 | 구분 | 비고 |
|---|---|---|
| document_id | **필수** | §5 형식 |
| title | **필수** | 사람이 읽는 제목 |
| artifact_type | **필수** | §7 enum |
| version | **필수** | §6 semver, v prefix 없음 |
| lifecycle_status | **필수** | §8 enum |
| implementation_status | **필수** | §9 enum |
| owner | **필수** | 담당 주체 (예: finance-harness) |
| exposure_class | **필수** | §10 — 문서 부여 가능 3값 |
| distribution_boundary | **필수** | §10.3 |
| runtime_loadable | **필수** | boolean |
| routable | **필수** | boolean |
| last_updated | **필수** | YYYY-MM-DD |
| depends_on | 조건부 필수 | policy(최상위 제외)·contract·lens·fixture·template·validator·security-policy는 필수, 그 외 선택 |
| public_exposable | 선택 | 생략 시 false |
| validates / validated_by / supersedes / related_documents | 선택 (§12의 강제성 규칙 적용) | fixture는 validates 필수 |
| description | 선택 | 없으면 info |
| 운영 상태 필드 (routing_enabled, deployment_status, content_hash, loaded_at 등) | **금지** | §11.3 — operational-metadata는 문서 Metadata가 아님 |

### 13.2 Lens 추가 필드 (전부 필수)

| 필드 | 의미 |
|---|---|
| lens_key | catalog 등록 이름 (catalog가 소유하는 값의 기재) |
| trigger | 사용 조건 요약 (canonical 상세는 lens 본문 §0) |
| forbidden_intents | 이 렌즈로 자주 유입되는 위험 intent 목록 (policyguard §3의 값만 사용) |
| redirect_to | redirect 관계 요약 |
| fixture_refs | 관련 fixture ID 목록 (예: SF-01, RF-01) |
| activation_gate | active 전환에 요구되는 검증 게이트 식별 (예: routing-fixture regression 통과) |

- 현행 lens 문서의 `## 0. Metadata` 코드블록(lens key, category, status, trigger, forbidden intent, redirect 관계, 관련 fixture, output style)과 의미가 겹치는 필드는 migration 시 front matter로 이관하며, 값의 의미는 변경하지 않는다. `finance-lens-authoring-standard.md` §3의 렌즈 공통 Metadata 형식과의 정합은 migration Phase에서 해당 표준의 개정(별도 작업)으로 맞춘다.

### 13.3 Policy / Contract / Security-policy 추가 필드 (필수)

| 필드 | 의미 |
|---|---|
| authority_level | 충돌 시 우선순위 상의 위치 (예: 1 = 최상위 service policy) |
| governed_artifacts | 이 계약이 규율하는 대상 (document_id 또는 artifact_type 목록) |
| breaking_change_policy | major 변경 시 요구 절차 (예: 전체 fixture regression + 하위 문서 정합 검토) |

### 13.4 Fixture 추가 필드 (필수)

| 필드 | 의미 |
|---|---|
| fixture_set | 케이스 집합 식별 (예: routing-baseline, red-team) |
| fixture_count | 등록 케이스 수 (정식 케이스 기준, 후보 제외) |
| validates | 검증 대상 document_id 목록 (§12) |

### 13.5 Report / Handoff 추가 필드 (필수)

| 필드 | 의미 |
|---|---|
| reviewed_sources | 검토한 원본 document_id 목록 |
| as_of | 검토 기준 시점 (YYYY-MM-DD) |
| non_authoritative | true 고정 — 보고서는 canonical 정의를 소유하지 않음 |

---

## 14. 작성 형식

### 14.1 YAML Front Matter 채택

Markdown 문서의 machine-readable Metadata 표준 형식으로 **YAML Front Matter를 채택**한다. 문서 최상단에 한 번만 배치한다.

```yaml
---
document_id: finance.contract.routing
title: Finance Routing Contract
artifact_type: contract
version: 1.0.0
lifecycle_status: active
implementation_status: contract-only
owner: finance-harness
exposure_class: raw-restricted
distribution_boundary: private-resource
runtime_loadable: true
routable: false
last_updated: 2026-07-10
depends_on:
  - document_id: finance.policy.service
    version: ">=0.1.0 <1.0.0"
---
```

### 14.2 표기 규칙 (확정)

```text
- 날짜: ISO 8601 (YYYY-MM-DD)
- boolean: 소문자 true / false ("yes", "Y", 1 금지)
- enum: 소문자 kebab-case (대소문자 변형 금지)
- 필수 목록 필드가 비어 있으면 [] 명시, 선택 필드는 생략
- 선택 필드 생략 = 정의된 기본값 적용 (public_exposable → false 등)
- 알 수 없는 필드: validation error (실험 필드는 x- prefix 사용, warning)
- Front Matter 블록은 문서당 정확히 1개 (중복 Metadata 블록 금지)
```

### 14.3 기존 표기와의 관계

현행 문서의 `> **상태**: 초안 v0.1` 인용 표기와 `## 0. Metadata` 코드블록은 legacy 표기다.

- migration(§17) 전까지 legacy 표기는 유효한 현행 표기로 인정한다.
- Front Matter가 도입된 문서에서는 Front Matter가 machine-readable 기준이며, 동일 정보의 legacy 블록은 같은 변경에서 제거하거나 사람용 요약으로만 남긴다. 두 표기가 값이 다르면 validation error다.
- **현재 문서들은 아직 이 형식으로 전환되지 않았다.** 본 문서는 target standard이며 migration은 §17의 별도 작업이다.

---

## 15. 예시

아래 7종은 **향후 적용할 표준 예시**다. 기존 문서를 실제로 수정한 결과가 아니며, 값(특히 lifecycle_status·version)은 migration inventory에서 확정하기 전의 예시값이다. 파일명은 현재 실제 파일명이다.

### 15.1 Service Policy — `00-core/0-a-safety-core/00-service-policy.md`

```yaml
---
document_id: finance.policy.service
title: AI 투자 체크리스트/기록 서비스 정책
artifact_type: policy
version: 0.1.0
lifecycle_status: active
implementation_status: contract-only
owner: finance-harness
exposure_class: raw-restricted
distribution_boundary: private-resource
runtime_loadable: true
routable: false
last_updated: 2026-07-10
authority_level: 1
governed_artifacts:
  - artifact_type: policy
  - artifact_type: contract
  - artifact_type: lens
  - artifact_type: template
  - artifact_type: validator
  - artifact_type: fixture
breaking_change_policy: 전체 하위 문서 정합 검토 + 전체 fixture regression
depends_on: []
validated_by:
  - finance.fixture.routing
---
```

### 15.2 Routing Contract — `00-core/0-c-runtime-contract/finance-routing-contract.md`

```yaml
---
document_id: finance.contract.routing
title: Finance Routing Contract
artifact_type: contract
version: 1.0.0
lifecycle_status: candidate
implementation_status: contract-only
owner: finance-harness
exposure_class: raw-restricted
distribution_boundary: private-resource
runtime_loadable: true
routable: false
last_updated: 2026-07-10
authority_level: 3
governed_artifacts:
  - artifact_type: lens
breaking_change_policy: routing fixture 전체 regression + alignment 재검토
depends_on:
  - document_id: finance.policy.service
  - document_id: finance.policy.policyguard
  - document_id: finance.contract.lens-catalog
validated_by:
  - finance.fixture.routing
  - finance.fixture.red-team
related_documents:
  - finance.report.policyguard-alignment
---
```

### 15.3 Active Lens — `01-core-analysis-lenses/finance-lens-market-context.md`

```yaml
---
document_id: finance.lens.market-context
title: Market Context Lens
artifact_type: lens
version: 0.1.0
lifecycle_status: active
implementation_status: contract-only
owner: finance-harness
exposure_class: internal-controlled
distribution_boundary: private-resource
runtime_loadable: true
routable: true
last_updated: 2026-07-10
lens_key: market-context
trigger: 종목/시장에 대한 포괄적 질문. 다른 렌즈 선택이 애매할 때의 기본 진입점
forbidden_intents:
  - RISK_BUY_SELL
  - RISK_RECOMMENDATION
  - RISK_DATA_GROUNDING
redirect_to: RISK_BUY_SELL·RISK_RECOMMENDATION의 redirect 대상, UNKNOWN 기본 렌즈
fixture_refs: [SF-01, SF-06, RF-01, RF-02, RF-03, RF-08, DG-05, AM-04]
activation_gate: routing-fixture regression 통과 (finance.fixture.routing)
depends_on:
  - document_id: finance.contract.lens-catalog
  - document_id: finance.contract.lens-authoring-standard
  - document_id: finance.policy.policyguard
validated_by:
  - finance.fixture.routing
---
```

### 15.4 Candidate Lens — `01-core-analysis-lenses/finance-lens-risk-checklist.md`

```yaml
---
document_id: finance.lens.risk-checklist
title: Risk Checklist Lens
artifact_type: lens
version: 0.1.0
lifecycle_status: candidate
implementation_status: contract-only
owner: finance-harness
exposure_class: internal-controlled
distribution_boundary: private-resource
runtime_loadable: false
routable: false
last_updated: 2026-07-10
lens_key: risk-checklist
trigger: 리스크 점검·기준 수립·기록 방법에 대한 질문
forbidden_intents:
  - RISK_STOP_LOSS
  - RISK_POSITION_SIZE
  - RISK_TARGET_PRICE
  - RISK_BUY_SELL
  - RISK_ACCOUNT_BASED_ADVICE
redirect_to: RISK_BUY_SELL(RF-01~03)·RISK_STOP_LOSS(RF-05)·RISK_POSITION_SIZE(RF-06~07)의 redirect 대상
fixture_refs: [SF-05, SF-06, RF-01, RF-02, RF-03, RF-05, RF-06, RF-07]
activation_gate: routing-fixture regression 통과 후 active 전환
depends_on:
  - document_id: finance.contract.lens-catalog
  - document_id: finance.contract.lens-authoring-standard
  - document_id: finance.policy.policyguard
validated_by:
  - finance.fixture.routing
---
```

(candidate이므로 routable=false·runtime_loadable=false가 기본이다. §8·§11의 "candidate 기본 비활성" 규칙의 예시다.)

### 15.5 Red-team Cases — `00-core/0-c-runtime-contract/finance-red-team-cases.md`

```yaml
---
document_id: finance.fixture.red-team
title: Finance Red Team Cases
artifact_type: fixture
version: 1.0.0
lifecycle_status: candidate
implementation_status: contract-only
owner: finance-harness
exposure_class: raw-restricted
distribution_boundary: repo-only
runtime_loadable: false
routable: false
public_exposable: false
last_updated: 2026-07-10
fixture_set: red-team
fixture_count: 0
depends_on:
  - document_id: finance.fixture.routing
  - document_id: finance.policy.policyguard
validates:
  - finance.contract.routing
  - finance.policy.policyguard
  - finance.validator.output
---
```

(fixture_count: 0은 red-team 케이스가 Promotion Gate 통과 전에는 정식 fixture가 아니라는 해당 문서 §16 규칙의 반영이다. 정식 승격 시 count를 갱신한다 — minor.)

### 15.6 Architecture Decision — `00-core/0-d-knowledge-architecture/finance-knowledge-storage-decision.md`

```yaml
---
document_id: finance.adr.knowledge-storage
title: Finance Knowledge Storage Decision
artifact_type: architecture-decision
version: 1.0.0
lifecycle_status: active
implementation_status: contract-only
owner: finance-harness
exposure_class: internal-controlled
distribution_boundary: repo-only
runtime_loadable: false
routable: false
last_updated: 2026-07-10
related_documents:
  - finance.security.document-exposure
---
```

### 15.7 Alignment Report — `00-core/0-c-runtime-contract/finance-policyguard-alignment.md`

```yaml
---
document_id: finance.report.policyguard-alignment
title: Finance PolicyGuard Alignment Report
artifact_type: report
version: 1.1.0
lifecycle_status: active
implementation_status: contract-only
owner: finance-harness
exposure_class: internal-controlled
distribution_boundary: repo-only
runtime_loadable: false
routable: false
last_updated: 2026-07-10
reviewed_sources:
  - finance.policy.service
  - finance.policy.policyguard
  - finance.validator.output
  - finance.template.prompt
  - finance.fixture.routing
  - finance.contract.routing
as_of: 2026-07-10
non_authoritative: true
---
```

(report의 exposure는 internal-controlled이되, 보고서에 인용된 policy·red-team 원문 부분은 exposure policy §3.2에 따라 raw-restricted에 준해 취급한다.)

---

## 16. Validation Rules

검증 규칙을 severity별로 정의하고, 각 결과가 무엇을 차단하는지 명시한다. 차단 지점은 authoring merge / build / packaging / activation / runtime load / release의 6종이다. 이 차단 어휘는 검증 파이프라인의 운영 어휘이며, Runtime의 PolicyGuard action enum(pass/block/rewrite/fallback 등)이 아니고 새 action을 신설하지 않는다.

### 16.1 Error

| # | 규칙 | 차단 지점 |
|---|---|---|
| E-01 | document_id 누락 | authoring merge |
| E-02 | document_id 중복 (repo 내 유일성 위반) | authoring merge |
| E-03 | document_id 형식 오류 또는 type segment ↔ artifact_type 불일치 | authoring merge |
| E-04 | version 형식 오류 (semver 아님, v prefix, 혼용 표기) | authoring merge |
| E-05 | 존재하지 않는 document_id를 depends_on/validates/validated_by/supersedes가 참조 | build |
| E-06 | 허용되지 않은 enum 값 (artifact_type, lifecycle_status, implementation_status, exposure_class, distribution_boundary) | authoring merge |
| E-07 | routable=true 이면서 runtime_loadable=false | build |
| E-08 | exposure_class가 raw-restricted 또는 internal-controlled인데 public_exposable=true | build |
| E-09 | exposure_class에 sanitized-runtime-output 또는 operational-metadata 사용 | authoring merge |
| E-10 | active lens인데 lens_key 등 §13.2 필수 필드 누락 | activation |
| E-11 | lifecycle_status가 active가 아닌 문서가 routable=true로 release package에 포함 (candidate 활성화 시도 포함) | activation |
| E-12 | 파일명에 version suffix 포함 | authoring merge |
| E-13 | 파일명에 underscore 사용 | authoring merge |
| E-14 | dependency version constraint 불일치 (참조 대상 현행 version이 범위 밖) | activation |
| E-15 | 운영 상태 필드(routing_enabled, deployment_status, content_hash, loaded_at 등)가 front matter에 존재 | authoring merge |
| E-16 | 순환 dependency | build |
| E-17 | retired/deprecated document_id의 재사용 | authoring merge |
| E-18 | Front Matter와 legacy Metadata 블록의 값 불일치 (공존 기간) | authoring merge |
| E-19 | fixture인데 validates 누락 또는 routable=true | build |
| E-20 | raw-restricted 문서가 §7·exposure policy §4 기본과 달리 완화 방향으로 재분류 (승인 기록 없음) | packaging |

### 16.2 Warning

| # | 규칙 | 효과 |
|---|---|---|
| W-01 | last_updated가 기준 기간(예: 180일)보다 오래됨 | 리뷰 알림 (차단 없음) |
| W-02 | owner 없음 | authoring 리뷰 지적 |
| W-03 | 권장 유형(contract, lens)에 related_documents 없음 | 리뷰 지적 |
| W-04 | deprecated 문서를 depends_on으로 참조 | 전환 계획 요구, release 노트 기재 |
| W-05 | 선택 Metadata(description 등) 누락 | 리뷰 지적 |
| W-06 | report/handoff의 as_of 누락 | 리뷰 지적 (필수 승격 전 과도기 한정 — 확정 후 error) |
| W-07 | x- prefix 실험 필드 사용 | 표준화 검토 후보 등록 |
| W-08 | supersedes 대상이 여전히 active | 전환기 관리 대상 |

### 16.3 Info

| # | 규칙 |
|---|---|
| I-01 | optional description 없음 |
| I-02 | version에 대응하는 Git tag 미생성 |
| I-03 | public-safe projection 미작성 (public 후보 콘텐츠 보유 문서) |

---

## 17. Migration Strategy

현재 문서의 Metadata는 형식이 혼재되어 있다.

```text
- "> 상태: 초안 v0.1" 인용 표기 (policyguard-rules, prompt-templates,
  routing-fixtures, lens-authoring-standard, lens 문서 서두 등)
- "## 0. Metadata" text 코드블록 (routing-contract, lens 본문,
  alignment report, 0-D 문서)
- H1 또는 참조에 version 포함 (finance-routing-contract-v1.md 류의
  versioned 파일명 인용이 fact-base 목록 등에 잔존)
- Metadata version 없음 / document_id 없음 (전 문서 공통)
```

한 번에 위험하게 수정하지 않고 다음 11단계로 이행한다.

```text
1.  Inventory            — 전 문서의 현행 표기·상태·참조 목록화
2.  document_id 할당     — §5 규칙으로 전 문서에 id 부여 (문서 수정 없이
                           매핑표 먼저 작성)
3.  filename 검증        — §4 위반 파일명 식별 (rename은 참조 갱신과
                           함께 별도 커밋)
4.  version 정규화       — "초안 v0.1" 류 표기를 semver로 매핑
5.  lifecycle/implementation status 분리 — "초안"을 draft/candidate로,
                           구현 상태를 별도 필드로 분리
6.  exposure metadata 적용 — exposure policy §4 기본 등급표 기준으로
                           exposure_class/distribution_boundary 기재
7.  dependency 등록      — 파일명 참조를 document_id 참조로 등록
8.  validation dry-run   — §16 규칙 시험 실행 (차단 없음)
9.  warning-only phase   — 위반을 경고로만 보고
10. error enforcement    — §16 Error의 차단 활성화
11. activation gate 연결 — active 전환·release에 검증 통과를 요구
```

반드시 지킬 조건:

- **이 문서 생성만으로 migration이 완료된 것이 아니다.** 현재 어떤 기존 문서도 이 표준으로 전환되지 않았다.
- 기존 문서의 intent/action/lens/fixture 의미는 migration에서 변경하지 않는다.
- 기존 파일명을 다시 versioned filename으로 변경하지 않는다.
- 문서 전체 자동 변환 전 diff review가 필요하다.
- **Metadata migration과 콘텐츠 의미 변경을 같은 작업(커밋/PR)에 섞지 않는다.** Metadata 이관 diff는 의미 불변임이 리뷰로 확인 가능해야 한다.

---

## 18. Future PostgreSQL Projection

DB schema는 작성하지 않는다. 향후 PostgreSQL Runtime Projection이 도입될 경우, 본 표준의 Metadata가 어느 쪽 소유로 투영되는지만 개념적으로 분류한다.

```text
문서 의미 소유 (Markdown Front Matter → 검증된 projection의 입력):
- document_id
- version
- artifact_type
- lifecycle_status (선언)
- dependency (depends_on / validates / validated_by / supersedes)
- exposure_class / distribution_boundary
- runtime_loadable / routable (원칙 선언)

Runtime 운영 상태 소유 (DB/Control Plane — 문서 Metadata 금지, §11.3):
- content_hash
- deployment version
- activation status (candidate/canary/active의 운영 상태)
- routing enabled
- loaded_at
- rollback target
- access audit
```

소유권 경계를 다시 명시한다.

```text
Markdown = 의미의 Source of Truth
PostgreSQL = 검증된 Runtime Projection과 운영 상태
```

- 사람이 양쪽에서 같은 의미를 수정하는 **이중 Source of Truth는 금지**한다 (storage decision §12 승계).
- document_id는 Markdown과 DB projection 양쪽에서 동일한 값을 사용하는 유일한 조인 키다. DB 도입 시 별도 식별자 체계를 신설하지 않는다 (§1의 "식별자 불일치" 혼선 차단).
- DDL, table, column type, index, migration은 본 문서의 범위가 아니며 작성하지 않는다.

---

## 19. 현재 상태

```text
decided / MVP target contract
- Metadata field standard (§13)
- filename rule (§4)
- document_id rule (§5)
- version rule (§6)
- lifecycle model (§8) / implementation status model (§9)
- exposure 표기 모델 (§10 — 등급 정의는 exposure policy 소유)
- runtime eligibility (§11)
- dependency 표기 (§12)
- validation requirements (§16)

proposed / migration required
- 기존 문서 YAML Front Matter 전환
- document_id 일괄 할당
- dependency graph 구축
- validation CLI 또는 build step
- lens-authoring-standard §3 Metadata 형식과의 정합 개정

not implemented / repo verification required
- Metadata parser
- validator
- packaging gate
- runtime loader integration
- CI enforcement
- PostgreSQL projection
```

결정 완료와 구현 완료를 동일하게 취급하지 않는다. 위 "decided" 항목은 계약 확정을 의미할 뿐, 어떤 파서·검증기·게이트도 구현되었음을 의미하지 않는다.

---

## 20. Non-goals

다음은 본 문서의 범위에서 제외한다.

```text
- 기존 문서 실제 수정
- 기존 파일 rename
- intent/action/lens key 변경
- fixture 기대값 변경
- Runtime 코드 구현
- DB schema
- migration (전략 기술만 하며 수행하지 않음)
- ingestion
- Frontend 설계
- Auth/Billing
- 새로운 PolicyGuard action 추가
- 모든 문서를 active로 승격
```

---

## 21. 최종 표준

```text
Stable filename
+ stable document_id
+ Metadata semantic version
+ explicit lifecycle status
+ separate implementation status
+ exposure classification
+ document_id-based dependency
+ validation before activation
```

- 파일명은 안정적이고 버전을 담지 않는다. 내용 계약의 버전은 Metadata semver가, 변경 이력은 Git이, 생명주기는 lifecycle_status가, 구현 여부는 implementation_status가 각각 표현한다.
- 노출 등급은 exposure policy의 canonical 등급명으로만 표기하고, 배포 경계(distribution_boundary)와 축을 분리한다.
- 의존성은 document_id로 표기하며, 활성화(activation)는 validation 통과를 전제로 한다.
- 운영 상태(operational-metadata)는 문서 Metadata에 두지 않는다.
- **현재 어떤 기존 문서도 이 표준으로 migration되지 않았다.** 본 문서는 target standard이며, 전환은 §17의 단계적 별도 작업이다.
