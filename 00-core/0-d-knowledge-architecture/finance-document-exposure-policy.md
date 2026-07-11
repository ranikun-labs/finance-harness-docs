# Finance Document Exposure Policy

## 0. Metadata

```text
status            : decided v1.0 — 0-D Knowledge Architecture 두 번째 문서
document type     : Exposure Policy (정책 계약 문서)
path              : 00-core/0-d-knowledge-architecture/finance-document-exposure-policy.md
scope             : Finance Harness Knowledge Artifact의 노출 등급 분류와
                    Packaging, Public API, Frontend, Admin/Debug, 로그·오류,
                    외부 모델 전송, 사용자 데이터 결합, Sanitization 경계 정의.
parent decision   : finance-knowledge-storage-decision.md (decided v1.0)
                    — 본 문서는 해당 ADR의 §8.3에서 예고된 후속 문서이며,
                      상위 저장 결정을 변경하지 않는다.
fact-base documents :
  - finance-knowledge-storage-decision.md          (상위 ADR — 저장 결정)
  - finance-policyguard-alignment.md               (internal alignment report v1.1)
  - finance-routing-contract-v1.md                 (0-C Runtime Contract)
  - finance-output-examples-v1.md                  (internal-only 응답 예시)
  - finance-red-team-cases-v1.md                   (internal-only 공격 검증 corpus)
non-goals         :
  - 저장 위치·Source of Truth 재결정 (상위 ADR 소유)
  - lens key / trigger / intent / action / redirect / PolicyGuard /
    fixture expected behavior / Output Validator 의미의 수정
  - PostgreSQL schema, API endpoint, RBAC, 로그 시스템의 구현
```

- 충돌 시 우선순위: 저장 위치·Runtime 로딩 경계·"sanitized output만 나간다"는 상위 결정은 `finance-knowledge-storage-decision.md`가 소유한다. intent/action/lens/redirect/validator의 의미 정의는 각 canonical 문서(`00-service-policy.md`, `finance-policyguard-rules.md`, `finance-routing-contract-v1.md` 등)가 소유한다. 본 문서는 **노출 등급과 노출 경계만** 소유하며, 상위 결정과 canonical 의미를 재정의하거나 완화하지 않는다.

---

## 1. 문서 목적

본 문서는 Finance Harness의 문서와 Runtime 산출물을 **누구에게, 어디까지, 어떤 형태로 보여줄 수 있는가**를 잠그는 Exposure Policy다.

핵심 질문은 다음 네 가지다.

```text
1. 어떤 원문과 metadata는 서버 내부에만 남겨야 하는가?
2. 어떤 정보는 운영자나 내부 도구만 볼 수 있는가?
3. 어떤 내용은 사용자에게 안전하게 제공할 수 있는가?
4. Finance Runtime은 어떤 형태로 정제한 뒤
   Frontend와 Public API에 반환해야 하는가?
```

본 문서는 저장 위치를 다시 결정하는 문서가 아니다. 두 문서의 역할 분담은 다음과 같다.

```text
finance-knowledge-storage-decision.md
= 어디에 저장하는가 (Authoring Source, Runtime 로딩, DB 유예)

finance-document-exposure-policy.md
= 무엇을 누구에게 어디까지 보여주는가 (노출 등급, 금지 경계)
```

상위 ADR §10.3이 정의한 노출 통제의 다섯 축 — `packaging boundary`, `access control`, `API response contract`, `logging/redaction`, `debug/admin exposure` — 의 상세 규칙을 본 문서가 소유한다.

---

## 2. 상위 결정과 적용 범위

### 2.1 변경 불가한 상위 결정

다음 상위 결정은 이미 확정되었으며, 본 문서는 이를 전제로만 사용한다.

```text
Finance Harness MVP
= Markdown Authoring Source of Truth
+ WAS private resource
+ Finance Runtime 내부 로딩
+ Sanitized API Response
```

- 본 문서는 이 결정을 재검토하지 **MUST NOT** 한다.
- 본 문서의 어떤 규칙도 위 결정을 완화하는 방향으로 해석해서는 **MUST NOT** 된다.

### 2.2 적용 범위

본 정책은 다음 전부에 적용된다.

- Finance Harness의 모든 Knowledge Artifact (Lens, PolicyGuard, Routing Contract, Runtime Contract, Fixture, Red-team Case, Output Examples, Output Validator, Prompt Template, Catalog, Authoring Standard, Metadata/Dependency 정의, 공개 학습 콘텐츠, 일지 질문 템플릿)
- Finance Runtime이 생성하는 모든 사용자 응답과 중간 산출물
- 배포 산출물(Frontend bundle, static resource, CDN), Public API 응답, 오류 응답, 로그, 관측 데이터, Admin/Debug 접근 경로, 외부 모델 전송 payload

### 2.3 준수해야 하는 canonical 의미

본 정책은 다음의 의미를 인용만 하며 수정하지 **MUST NOT** 한다.

```text
- lens key           (finance-lens-catalog.md 소유)
- trigger            (lens-catalog + per-lens §0 소유)
- intent             (finance-policyguard-rules.md §3 소유, 12종)
- guard action       (fixtures §3 enum + policyguard §6 소유, 7종)
- redirect           (policyguard §6 소유)
- PolicyGuard 의미   (finance-policyguard-rules.md 소유)
- fixture expected behavior (finance-routing-fixtures-v2.md 소유)
- Output Validator 의미     (finance-output-validator.md 소유)
```

---

## 3. 노출 등급

노출 관련 개념은 하나의 등급 체계(단일 enum)가 아니라, 서로 다른 축으로 분리해 정의한다. 아래 다섯 개념의 정책 의미는 §3.1~§3.5에서 그대로 유지하되, 같은 계층의 단일 "등급" 목록으로 표현하지 않는다.

```text
exposure_class (문서 원문의 민감도)
= raw-restricted | internal-controlled | public-safe

distribution_boundary (문서의 배포·접근 경계) — MVP 값
= private-resource

output_class (Runtime 산출물의 노출 분류)
= sanitized-runtime-output

운영 상태 분류 (문서 exposure_class 아님)
= operational-metadata
```

모든 문서(Artifact)는 exposure_class를 반드시 하나 가져야 하며(MUST), 분류가 불명확한 경우 **더 제한적인 exposure_class로 보수 분류**해야 한다(MUST). Runtime 산출물은 output_class 계약(§3.4, §11)을 충족해야 한다(MUST).

### 3.1 exposure_class: `raw-restricted`

**정의**

```text
원문 자체가 내부 실행 규칙, 공격 사례, Prompt,
정책 우회 정보 또는 독점 Knowledge를 포함해
일반 사용자에게 노출하면 안 되는 자료
```

**대표 대상**

- PolicyGuard 원문 (`finance-policyguard-rules.md`)
- Red-team case 원문 — `user_input`, `unsafe_draft`, `forbidden_output` 포함 (`finance-red-team-cases-v1.md` — 문서 자체가 internal-only 명시)
- unsafe draft / forbidden output 예시 (`finance-output-examples-v1.md` §11~§12 — 실패 예시의 인용)
- 내부 Prompt Template (`finance-prompt-templates-v2.1.md`)
- Output Validator 내부 규칙 (`finance-output-validator.md`)
- 내부 Routing Contract 원문 (`finance-routing-contract-v1.md`)
- 비공개 평가 corpus (`finance-routing-fixtures-v2.md` 정식 fixture 포함)
- secret 또는 credential (API key, token, 개인 키 등)

**원칙**

| 규칙 | 수준 |
|---|---|
| WAS 내부 Finance Runtime만 접근한다 | MUST |
| Frontend에 전달하지 않는다 | MUST NOT |
| Public API로 반환하지 않는다 | MUST NOT |
| 일반 로그에 기록하지 않는다 | MUST NOT |
| 오류 응답에 포함하지 않는다 | MUST NOT |
| 다운로드로 제공하지 않는다 | MUST NOT |
| 원문 열람이 필요한 경우 break-glass 절차(§7)만 사용한다 | MUST |

### 3.2 exposure_class: `internal-controlled`

**정의**

```text
운영·검수·개발 목적으로 내부에서 사용할 수 있지만
일반 사용자에게 직접 제공하지 않는 자료
```

**대표 대상**

- Lens 원문 (1차 Core Analysis Lens 10종 및 향후 Asset/Long-term Lens)
- 문서 metadata 전체 (status, scope, source documents 등)
- dependency graph
- fixture 연결 정보 (어떤 lens/intent가 어떤 fixture에 연결되는지)
- document version / content hash
- validation result (정합성 검토 보고서 `finance-policyguard-alignment.md` 포함 — 단, 보고서에 인용된 policy·red-team 원문 부분은 `raw-restricted`에 준해 취급)
- internal catalog (`finance-lens-catalog.md`)
- rollout 상태

**원칙**

| 규칙 | 수준 |
|---|---|
| 내부 Runtime 또는 승인된 운영 도구만 접근한다 | MUST |
| 일반 사용자 API에 노출하지 않는다 | MUST NOT |
| 운영 화면에는 필요한 최소 필드만 노출한다 | SHOULD |
| 원문 전체 다운로드는 기본 금지하며, 예외는 승인·감사를 거친다 | MUST |

### 3.3 exposure_class: `public-safe`

**정의**

```text
내용 자체가 사용자에게 제공 가능하도록
검토·작성된 공개 안전 콘텐츠
```

**대표 대상 후보**

- 사용자용 학습 개념 설명 (Public Learning Content)
- 일반적인 체크리스트 항목 (공개용으로 검토된 것)
- 투자 일지 질문 (Journal Question Template의 사용자 제공분)
- 공개 가능한 고지 문구 (notices)
- 공개 Help 문서

**주의 — 등급의 범위는 콘텐츠이지 파일이 아니다**

```text
문서가 public-safe 등급이라고 해서
내부 metadata나 원본 파일 전체가 자동 공개되는 것은 아니다.
```

- `public-safe`는 **검토를 통과한 콘텐츠 단위**에만 적용되며, 해당 콘텐츠를 담은 원본 Markdown 파일·내부 metadata·서버 경로는 여전히 `internal-controlled` 이상으로 취급해야 한다(MUST).
- 공개가 필요한 경우 내부 원문에서 분리한 **별도 public artifact**로 제공해야 한다(MUST, §12).

### 3.4 output_class: `sanitized-runtime-output`

**정의**

```text
Finance Runtime이 내부 Knowledge를 사용해 생성한 뒤
PolicyGuard와 Output Validator를 통과하고
내부 규칙·Prompt·평가 데이터를 제거한 사용자 응답
```

**대표 필드**

- answer (최종 답변)
- applied lens keys (적용 Lens key — catalog 등록 이름 한정, §6.3)
- sanitized checklist (정제된 체크리스트)
- journal questions (투자 일지 질문)
- notices (사용자 고지)
- 사용자에게 필요한 제한적 출처·기준 정보

**원칙**

| 규칙 | 수준 |
|---|---|
| Frontend와 Public API의 기본 반환 등급이다 | MUST |
| Output Validator를 통과하지 않은 응답은 이 등급이 될 수 없다 | MUST NOT |
| §11의 Sanitization 계약을 전부 충족해야 한다 | MUST |

- `sanitized-runtime-output`은 원본 문서의 exposure_class가 아니다. Runtime 산출물에만 부여되는 output_class이며, 어떤 원본 문서도 이 값을 exposure_class로 가질 수 없다(MUST NOT).

### 3.5 운영 상태 분류: `operational-metadata`

**정의**

```text
문서 의미가 아니라 운영 상태를 나타내는 정보
```

**대표 대상**

- artifact version
- content hash
- active / candidate / canary 상태
- routing enabled 여부
- validation status (pass/fail 상태값)
- access audit 기록
- rollout environment

**원칙**

| 규칙 | 수준 |
|---|---|
| 사용자 응답에 기본적으로 포함하지 않는다 | MUST NOT |
| 운영자도 필요한 최소 범위만 접근한다 | MUST |
| 내부 문서 본문과 분리하여 관리한다 | MUST |

- `operational-metadata`는 문서 원문의 exposure_class가 아니라 **운영 상태 정보의 분류**다. 문서의 exposure_class 값으로 사용하지 않는다(MUST NOT).

### 3.6 distribution_boundary: `private-resource`

**정의**

```text
문서 원문의 민감도가 아니라,
문서가 어디에 배포되어 누가 접근할 수 있는지를 정하는 배포·접근 경계
```

**원칙**

| 규칙 | 수준 |
|---|---|
| `private-resource`는 exposure_class가 아니며, 노출 등급 목록에 포함하지 않는다 | MUST (구분 유지) |
| MVP의 distribution_boundary 값은 `private-resource` 하나다 — Knowledge 문서를 WAS private resource로 패키징해 서버 Runtime만 읽게 하는 경계 (§5) | MUST |
| `raw-restricted` 또는 `internal-controlled` 문서에 **함께 적용될 수 있다** (예: PolicyGuard 원문 = exposure_class `raw-restricted` + distribution_boundary `private-resource`) | MUST (허용) |
| private-resource로 패키징되어도 exposure_class의 금지 규칙(§3.1, §3.2)은 그대로 유지된다 — 배포 경계가 민감도 통제를 대체하지 않는다 | MUST |
| WAS 침해까지 방어하는 독립 보안 경계로 과장하지 않는다 (상위 ADR §10.1) | MUST NOT (과장) |

### 3.7 분류 간 경계 요약

```text
[exposure_class — 문서 원문의 민감도]
raw-restricted        정책·공격·Prompt·평가 원문
                      서버 Runtime 전용, break-glass 외 열람 불가
internal-controlled   Lens 원문·metadata·catalog·검증 결과
                      내부 운영 도구까지, 사용자 API 불가
public-safe           검토 완료 공개 콘텐츠
                      승인된 콘텐츠 단위만, 별도 artifact로 분리 공개

[distribution_boundary — 배포·접근 경계 (exposure_class 아님)]
private-resource      WAS private resource 패키징 경계 (§3.6, §5)
                      raw-restricted / internal-controlled 문서에 함께 적용 가능

[output_class — Runtime 산출물 (문서 exposure_class 아님)]
sanitized-runtime-output
                      Validator 통과 후 정제된 사용자 응답
                      Frontend / Public API 기본 반환

[운영 상태 분류 (문서 exposure_class 아님)]
operational-metadata  version·hash·rollout·audit
                      본문과 분리, 최소 접근
```

---

## 4. 문서 유형별 기본 등급

아래 표가 Finance Harness 문서 유형의 **기본(default) exposure_class 분류표**다. 분류 근거는 첨부 문서에 명시된 internal-only 표시와 각 문서의 실제 역할이며, 문서 의미를 추측해 재분류하지 않았다.

범례: ✅ 허용 / ❌ 금지 / ⚠️ 조건부(주석 참조)

| 문서 유형 | 기본 exposure_class | WAS 내부 접근 | 내부 운영 도구 노출 | Public API 노출 | Frontend 노출 | 원문 다운로드 | 예외 승인 필요 |
|---|---|---|---|---|---|---|---|
| Safety Core (`00-service-policy.md` 등 0-A 전체) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| PolicyGuard (`finance-policyguard-rules.md`) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Routing Contract (`finance-routing-contract-v1.md`) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Runtime Contract (0-C 계약 문서 전체) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Lens Infrastructure (overview, authoring-standard, catalog) | `internal-controlled` | ✅ | ⚠️² | ❌ | ❌ | ❌³ | 원문 열람 시 필요 |
| Core Analysis Lens (1차 10종 원문) | `internal-controlled` | ✅ | ⚠️² | ❌ | ❌ | ❌³ | 원문 열람 시 필요 |
| Asset / Long-term Lens (2차 이후 원문) | `internal-controlled` | ✅ | ⚠️² | ❌ | ❌ | ❌³ | 원문 열람 시 필요 |
| Fixture (`finance-routing-fixtures.md` — 비공개 평가 corpus. 원문 확인: PI-01~05 정책 우회 입력 원문, 케이스별 forbidden_outputs와 §9 금지 표현 assertion, 입력→expected_intent/expected_action 전체 매핑(§4~§8)과 §10 regression 실패 기준 등 정책 경계 탐색에 직접 활용 가능한 실패 기대값 포함 — 파일 전체 `raw-restricted` 확정) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Red-team Case (`finance-red-team-cases-v1.md` — internal-only 명시) | `raw-restricted` | ✅⁴ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Output Examples (`finance-output-examples-v1.md` — internal-only 명시, §11~§12 unsafe 포함) | `raw-restricted` | ✅⁴ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Output Validator (`finance-output-validator.md` — 원문 확인: §5 "차단해야 하는 출력 예시"에 구체 수치·행동 지시를 포함한 unsafe 문장 원문이 CRITICAL 실패 예시로 인용되고, §6 오탐 방지 예외의 허용/차단 판정 기준 등 validator 우회에 활용 가능한 상세 포함 — 최고 제한 우선 원칙에 따라 파일 전체 분류이며 일부 문장 단위 예외 없음) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Internal Prompt Template (`finance-prompt-templates-v2.1.md`) | `raw-restricted` | ✅ | ❌¹ | ❌ | ❌ | ❌ | break-glass 필수 |
| Document Catalog (`finance-lens-catalog.md`) | `internal-controlled` | ✅ | ✅ (최소 필드) | ❌ | ❌⁵ | ❌³ | 원문 열람 시 필요 |
| Authoring Standard (`finance-lens-authoring-standard.md`) | `internal-controlled` | ✅ | ✅ (최소 필드) | ❌ | ❌ | ❌³ | 원문 열람 시 필요 |
| Metadata / Dependency Definition (문서 metadata, dependency graph, 정합성 보고서) | `internal-controlled` | ✅ | ✅ (최소 필드) | ❌ | ❌ | ❌³ | 원문 열람 시 필요 |
| Public Learning Content (사용자용 학습 콘텐츠) | `public-safe` | ✅ | ✅ | ⚠️⁶ | ⚠️⁶ | ⚠️⁶ | 공개 승격 리뷰 필요 |
| Journal Question Template (일지 질문 — 사용자 제공분) | `public-safe` | ✅ | ✅ | ⚠️⁶ | ⚠️⁶ | ⚠️⁶ | 공개 승격 리뷰 필요 |

**주석**

1. `raw-restricted` 문서는 일반 운영 화면에 원문을 노출하지 **MUST NOT** 한다. 운영 도구에는 `operational-metadata`(document key, version, hash, validation pass/fail)만 노출할 수 있다. 원문 열람은 §7의 break-glass 절차만 허용한다.
2. `internal-controlled` 문서는 승인된 운영 도구에 한해 **필요 최소 필드**를 노출할 수 있다(SHOULD). 원문 전체 표시는 별도 권한과 감사를 요구한다(MUST).
3. 원문 전체 다운로드는 기본 금지(MUST NOT)이며, 예외는 §12의 승인 기록을 남긴 경우만 허용한다.
4. Red-team Case와 Output Examples의 unsafe 부분은 WAS가 파일로 보유하더라도 **production Prompt 조립에서 기본 제외**해야 한다(§9). "WAS 내부 접근 가능"은 저장·검증 목적의 접근을 의미하며, 사용자 응답 생성 경로에 원문이 유입될 수 있다는 의미가 아니다.
5. lens key(catalog 등록 이름)는 `sanitized-runtime-output`의 `applied lens keys` 필드로 노출 가능하다. 그러나 catalog **문서 원문**(trigger 조건, 병행 lens, 금지 우회 경로 서술)은 Frontend에 노출하지 **MUST NOT** 한다.
6. `public-safe` 콘텐츠도 원본 파일 그대로가 아니라, 승인 리뷰를 거쳐 내부 원문에서 분리된 **별도 public artifact** 형태로만 Public API·Frontend·다운로드에 제공해야 한다(MUST, §3.3·§12).

**보수 분류 원칙**

- 위 표에 없는 신규 문서 유형은 역할이 확정되기 전까지 `internal-controlled` 이상의 exposure_class로 분류해야 한다(MUST).
- 하나의 문서에 여러 민감도의 콘텐츠가 섞여 있으면 **문서 전체는 가장 제한적인 exposure_class**를 따른다(MUST). 예: 좋은 예시와 unsafe draft가 공존하는 `finance-output-examples-v1.md`는 문서 전체가 `raw-restricted`다.

**분류 요약 (문서 유형 단위)**: `raw-restricted` 9종 / `internal-controlled` 6종 / `public-safe`(콘텐츠 단위) 2종. Output Validator와 Fixture는 첨부 원문을 직접 확인해 재검증한 분류다.

---

## 5. Packaging 경계

### 5.1 금지 규칙

다음 규칙을 위반하는 빌드·배포 산출물은 그 자체로 정책 위반이다.

```text
raw-restricted 또는 internal-controlled 문서를

- Frontend source tree
- Frontend bundle
- static/public directory
- CDN
- browser cache
- service worker cache
- source map
- downloadable artifact
- client-side configuration
- npm package의 공개 배포물

에 포함하지 않는다. (MUST NOT)
```

- 원문 전체뿐 아니라 **부분 발췌, 주석, 테스트 fixture, 예시 문자열** 형태의 혼입도 동일하게 금지한다(MUST NOT).
- 빌드 도구의 자동 포함(예: glob 패턴에 의한 우발적 번들링)도 위반이며, "의도하지 않았다"는 예외 사유가 되지 않는다.

### 5.2 서버 패키징 원칙

| 원칙 | 수준 |
|---|---|
| Knowledge 문서는 WAS private resource 또는 서버 전용 저장소에만 포함한다 | MUST |
| 서버 Runtime만 읽을 수 있게 구성한다 | MUST |
| Public static handler가 접근할 수 없는 경로를 사용한다 | MUST |
| build 단계에서 Frontend 산출물에 내부 문서가 혼입되지 않았는지 검증한다 | MUST (§13 — future implementation requirement) |

- 본 절은 경계 원칙만 정의하며, 특정 framework의 구현 방식을 규정하지 않는다.

---

## 6. Public API와 Frontend 경계

### 6.1 원문 조회 API 금지

Public API는 원문 문서 조회 기능을 제공하지 **MUST NOT** 한다. 다음과 같은 endpoint는 어떤 형태로도 존재해서는 안 된다.

```text
GET /documents/{id}/raw
GET /policyguard/rules
GET /red-team/cases
GET /prompts/{id}
GET /routing-contract
```

- 경로·이름을 바꾼 동등 기능(예: `GET /internal/knowledge`, query parameter로 원문을 우회 반환)도 동일하게 금지한다(MUST NOT).

### 6.2 반환 가능 등급

Public API가 반환할 수 있는 것은 다음 두 가지뿐이다.

```text
1. sanitized-runtime-output  (§11 계약 충족분)
2. 명시적으로 승인된 public-safe 항목 (§12 승인 기록 존재분)
```

### 6.3 Public API 응답 금지 항목

다음은 Public API 응답의 어떤 필드에도 포함하지 **MUST NOT** 한다.

- raw Markdown (전체·부분 불문)
- 전체 Prompt
- PolicyGuard 내부 판단 규칙
- forbidden expression 목록 전체
- unsafe draft
- red-team input
- validator 내부 체크리스트
- 서버 파일 경로
- 내부 document id 또는 storage key
- dependency graph 전체
- 내부 confidence / debug trace
- stack trace

**`applied lens keys`의 제한**

- `applied lens keys`는 **사용자에게 설명 가능한 식별자**(catalog 등록 lens key)로 제한한다(MUST).
- lens key를 통해 내부 파일 경로, 문서 저장 위치, 문서 버전 체계를 유추할 수 있는 값을 사용하지 **MUST NOT** 한다. 예: `01-core-analysis-lenses/finance-lens-valuation.md` 같은 경로형 값은 금지, `valuation` 같은 등록 key는 허용.

### 6.4 Frontend 경계

- Frontend는 §6.2의 반환 가능 등급 외의 어떤 데이터도 수신·보관·캐싱하지 **MUST NOT** 한다.
- Frontend 코드, 환경 변수, client-side configuration에 내부 문서 내용·경로·식별자를 하드코딩하지 **MUST NOT** 한다.

---

## 7. Admin·Debug 접근 경계

### 7.1 기본 원칙

`admin`이나 `debug`라는 이유만으로 원문 전체를 자동 노출해서는 **MUST NOT** 된다.

| 원칙 | 수준 |
|---|---|
| 일반 운영 화면은 `operational-metadata` 중심으로 구성한다 | MUST |
| raw document 열람은 별도 권한과 감사를 요구한다 | MUST |
| debug endpoint는 production에서 기본 비활성화한다 | MUST |
| 전체 Prompt, raw Markdown, red-team corpus를 admin/debug 응답으로 반환하지 않는다 | MUST NOT |
| 운영 화면에서 문서 참조가 필요하면 document id / version / hash만 제공한다 | SHOULD |
| 원문 열람이 불가피한 경우 명시적 break-glass 절차를 사용한다 | MUST |

### 7.2 break-glass 절차 원칙

break-glass는 예외 절차이며, 다음 요건을 전부 갖춰야 한다.

```text
- 별도 고권한        (일반 admin 권한과 분리, MUST)
- 사유 입력          (열람 목적 기록, MUST)
- 시간 제한          (세션 만료, MUST)
- 접근 로그          (누가·언제·무엇을, MUST)
- 다운로드 기본 금지  (열람과 반출의 분리, MUST)
- 민감 필드 masking  (secret·사용자 데이터 등, MUST)
```

- 본 절은 절차 요건만 정의하며, 구체적인 RBAC 모델·권한 시스템 설계는 본 문서의 범위가 아니다(§16).

---

## 8. 로그·오류·관측성 경계

### 8.1 일반 로그 기록 금지 항목

다음은 일반 로그(application log, access log, 관측 pipeline)에 기록하지 **MUST NOT** 한다.

```text
- raw Markdown
- full Prompt
- user secret
- private key / token
- red-team case 원문
- unsafe draft
- 내부 Policy 전체
- 전체 프로젝트 context
- 사용자 투자 일지 원문 전체
```

### 8.2 로그 허용 항목

다음은 로그에 기록할 수 있다(허용 예시이며, 이 범위에서도 최소화가 원칙이다 — SHOULD).

```text
- document key
- artifact version
- content hash 일부 또는 식별용 hash
- applied lens key
- PolicyGuard action code
- validator pass / fail
- latency
- token usage
- sanitized error code
```

### 8.3 오류 응답 경계

오류 응답에 다음을 포함하지 **MUST NOT** 한다.

```text
- stack trace
- server file path
- SQL / DB query
- raw document fragment
- Prompt fragment
- 내부 rule id 설명 전체
```

- 오류 응답은 **사용자용 error code와 안전한 설명만** 반환해야 한다(MUST).
- Output Validator 실패·재작성 실패를 포함한 어떤 오류 경로에서도, 실패한 원 응답(unsafe draft)이나 내부 문서 fragment를 폴백으로 노출하지 **MUST NOT** 한다.

---

## 9. Prompt와 외부 모델 전송 경계

Finance Runtime이 외부 LLM API를 사용하더라도, 모든 내부 문서를 무조건 전체 전송해서는 **MUST NOT** 된다. **외부 모델 전송은 Frontend 노출과 다른 경계이지만, 동일하게 최소화와 통제가 필요하다.**

| 원칙 | 수준 |
|---|---|
| 해당 요청에 필요한 최소 Knowledge block만 조립해 전송한다 | MUST |
| 적용되지 않는 Lens / Policy 문서는 Prompt에서 제외한다 | MUST |
| Red-team corpus 원문은 production prompt에 기본 포함하지 않는다 | MUST NOT (포함) |
| Output Examples의 unsafe example이 모델 출력으로 복제되지 않도록 Prompt 조립에서 분리한다 | MUST |
| Prompt 전문의 logging은 기본 금지한다 | MUST NOT (기록) |
| 모델 제공자의 retention / no-training 조건을 별도로 확인한다 | MUST |

- 본 문서는 특정 LLM provider를 선택하지 않는다. provider 선택·계약 조건은 별도 결정 범위다.
- Generation Guard 주입 등 Prompt 구성의 의미 규칙은 canonical 문서(policyguard §8, templates v2.1)가 소유하며, 본 절은 **전송 범위의 최소화 경계만** 정의한다.

---

## 10. 사용자 데이터와의 결합 경계

Knowledge Artifact 노출 정책과 사용자 데이터 정책은 별개 축이다. 다만 Runtime에서 두 데이터가 결합될 수 있으므로 최소 경계를 기록한다.

**사용자 입력 후보**

```text
- 투자 질문
- 관심 종목
- 체크리스트 답변
- 투자 일지
- 첨부 이미지 / OCR 결과
```

**최소 경계**

| 원칙 | 수준 |
|---|---|
| 내부 Knowledge 원문과 사용자 데이터를 함께 로그에 남기지 않는다 | MUST NOT |
| 사용자 일지 원문을 debug payload로 반환하지 않는다 | MUST NOT |
| 사용자 데이터로 Prompt를 구성할 때 필요한 범위만 사용한다 | MUST |
| 사용자 응답에 다른 사용자의 데이터가 혼입되지 않는다 | MUST NOT |

- 상세 개인정보 처리·보존 기간·파기 정책은 **별도 문서의 범위**이며, 본 문서는 확정하지 않는다.

---

## 11. Sanitized Runtime Output 계약

응답이 `sanitized-runtime-output` 등급이 되기 위한 최소 조건을 정의한다. 아래 단계를 전부 거치지 않은 산출물은 이 등급을 주장할 수 **MUST NOT** 없다.

### 11.1 최소 단계

```text
1. 사용자 intent 확인            (policyguard §3의 분류 사용)
2. 적용 가능한 Lens 선택          (routing contract의 라우팅 사용)
3. PolicyGuard 적용              (canonical 의미 그대로)
4. 내부 Prompt / rule / fixture 원문 제거
5. Output Validator 검사         (통과 전 노출 불가)
6. 금지 표현·단정 표현·내부 식별자 제거
7. 사용자용 구조로 변환
```

- 단계의 순서·의미는 `finance-routing-contract-v1.md` §2의 실행 파이프라인을 따르며, 본 절은 그 파이프라인에 **노출 관점의 요건**(4·6·7단계)을 결합한 것이다. 파이프라인 자체를 재정의하지 않는다.
- Output Validator를 통과하지 못한 응답은 재작성·재검사 결과만 노출 후보가 되며, **원 응답은 어떤 형태로도 노출하지 MUST NOT 한다** (routing-contract §13과 정합).

### 11.2 Sanitization 결과에 포함 가능한 것

```text
- 설명 가능한 최종 답변
- 판단 전 체크리스트
- 확인해야 할 데이터
- 불확실성 표시
- 투자 일지 질문
- 사용자 고지
```

### 11.3 포함하면 안 되는 것 (MUST NOT)

```text
- 내부 chain-of-thought
- 전체 정책 규칙
- raw validator trace
- red-team 비교 내용
- 내부 Prompt
- 문서 저장 위치
```

---

## 12. 등급 변경과 예외 승인

노출 등급은 임의로 낮출 수 **MUST NOT** 없다.

### 12.1 등급 변경 원칙

| 원칙 | 수준 |
|---|---|
| `raw-restricted` → `public-safe` 직접 변경은 금지한다 | MUST NOT |
| `public-safe` 승격은 별도 리뷰를 거쳐야 한다 | MUST |
| sanitized output은 Runtime 생성물이며, 원문 문서의 등급 변경이 아니다 | MUST (구분 유지) |
| 문서 일부를 공개할 경우 별도 public artifact로 분리한다 | MUST |
| 내부 원문을 조건문으로 필터링해 그대로 공개하는 방식은 금지한다 | MUST NOT |

- 마지막 규칙의 의미: "internal 부분만 if문으로 가리고 나머지를 원문 그대로 서빙"하는 구조는 필터 오류 하나로 전체가 유출되는 구조이므로 채택하지 않는다. 공개는 항상 **분리된 산출물 생성**으로만 한다.

### 12.2 예외 승인 기록 항목

예외를 승인할 경우 다음을 반드시 기록한다(MUST).

```text
- 대상 artifact
- 현재 등급
- 요청 등급
- 공개 목적
- 공개 범위
- 검수자
- 승인 일시
- 만료 또는 재검토 일자
```

- 예외는 기본 규칙보다 **좁고, 시한이 있으며, 감사 가능**해야 한다(MUST).
- 구체적인 승인 시스템(워크플로 도구, 결재 체계)은 본 문서에서 구현하지 않는다(§16).

---

## 13. 검증·테스트 요구사항

아래 항목은 전부 `future implementation requirement`다. **현재 구현 완료 상태가 아니며**, 향후 구현 시 본 정책의 준수를 기계적으로 검증하는 최소 기준이다.

| # | 검증 항목 | 대응 경계 |
|---|---|---|
| V-01 | Frontend bundle에 raw Markdown이 없는지 검사 | §5 |
| V-02 | static / public / CDN 산출물 검사 | §5 |
| V-03 | Public API 응답 snapshot에 내부 필드(§6.3 금지 항목)가 없는지 검사 | §6 |
| V-04 | 로그에 Prompt / Markdown / secret이 없는지 검사 | §8 |
| V-05 | production debug endpoint 비활성화 확인 | §7 |
| V-06 | Red-team 케이스(user_input / unsafe_draft / forbidden_output)가 사용자 응답에 포함되지 않는지 검사 | §9, §11 |
| V-07 | Output Validator 실패 시 원문(unsafe draft·문서 fragment)을 반환하지 않는지 검사 | §8.3, §11 |
| V-08 | source map과 error response에 서버 경로가 없는지 검사 | §5, §8.3 |

- 각 항목은 CI 또는 배포 게이트에 통합하는 것을 목표로 한다(SHOULD — future implementation requirement).
- 검증 실패는 배포 차단 사유로 취급해야 한다(SHOULD — future implementation requirement).

---

## 14. PostgreSQL 도입과의 관계

본 정책은 저장 매체와 무관하게 동일하게 적용된다.

```text
Markdown in WAS
또는
Markdown Projection in PostgreSQL
```

어느 저장 방식을 사용하더라도 다음은 변하지 않는다.

```text
- raw document API 금지          (§6)
- 최소 권한                      (§7)
- sanitized response             (§11)
- 로그 redaction                 (§8)
- debug / admin 통제             (§7)
- 원문과 public artifact 분리     (§12)
```

- **PostgreSQL은 이 정책을 대체하지 않는다.** 상위 ADR §10.2가 확정했듯, 저장 매체를 파일에서 DB로 바꾸는 것은 노출 문제를 이동시킬 뿐 해결하지 않는다.
- 향후 PostgreSQL이 Runtime Projection / Control Plane으로 도입되더라도(상위 ADR §11~§12), projection 데이터·운영 상태 데이터에 본 문서의 등급 분류와 금지 경계를 **동일하게 적용해야 한다**(MUST).
- DB 도입을 이유로 본 정책의 어떤 규칙도 완화되지 않는다(MUST NOT).

---

## 15. 현재 정책과 미래 구현 구분

정책 결정과 구현 완료를 혼동하지 않도록 상태를 명시한다.

```text
decided / policy contract        (본 문서로 확정된 정책)
- exposure_class 3종 · distribution_boundary · output_class · 운영 상태 분류와 경계 (§3, §4)
- Packaging 금지 경계             (§5)
- Public API 원문 비노출          (§6)
- 로그·오류 redaction 원칙        (§8)
- sanitized output 원칙          (§11)

future implementation requirement (정책은 확정, 구현은 미래)
- bundle leak scan               (V-01, V-02, V-08)
- API response validation        (V-03)
- logging filter                 (V-04)
- debug endpoint control         (V-05)
- access audit                   (§7)
- break-glass workflow           (§7.2)

not implemented                  (본 문서가 설계하지 않는 것)
- RBAC
- DLP
- PostgreSQL row-level security
- automated exposure scanner
- admin approval system
```

- `future implementation requirement`와 `not implemented` 항목은 본 문서 어디에서도 완료된 것으로 취급하지 않는다.
- 정책 위반 판정은 `decided / policy contract` 항목에 대해서만 즉시 유효하며, 검증 자동화의 부재가 정책 위반의 면책 사유가 되지는 않는다.

---

## 16. 비목표

본 문서는 다음을 하지 않는다.

```text
- Finance Lens 의미 수정
- PolicyGuard action 수정
- Routing Contract 수정
- Output Validator 의미 수정
- PostgreSQL schema 설계
- API endpoint 구현
- RBAC 구현
- 로그 시스템 구현
- 개인정보 보존 기간 확정
- 특정 LLM provider 선정
- Frontend 화면 설계
```

- 위 항목이 필요한 시점에는 각각 별도 문서·별도 결정이 이 정책을 **전제로** 작성된다. 본 문서를 수정해 위 항목을 흡수하지 않는다.

---

## 17. 최종 정책

```text
Document exposure_class
= raw-restricted | internal-controlled | public-safe

MVP distribution_boundary
= private-resource

Runtime output_class
= sanitized-runtime-output

운영 상태 분류 (문서 exposure_class 아님)
= operational-metadata

사용자에게 나가는 것은
sanitized-runtime-output과
승인된 public-safe artifact뿐이다.
```

1. **원문은 서버 안에 남는다.** `raw-restricted`와 `internal-controlled` 문서는 Frontend bundle, static/public, CDN, source map, 다운로드 artifact, client configuration 어디에도 포함하지 않는다(MUST NOT).
2. **Public API는 원문 조회 기능을 제공하지 않는다**(MUST NOT). 반환 가능한 것은 Output Validator를 통과하고 §11 계약을 충족한 `sanitized-runtime-output`과, §12 승인 기록이 있는 `public-safe` artifact뿐이다.
3. **admin / debug는 원문 자동 열람 권한이 아니다.** 일반 운영 화면은 `operational-metadata` 중심이며, 원문 열람은 break-glass(고권한·사유·시간 제한·감사·다운로드 금지·masking)로만 한다(MUST).
4. **로그와 오류는 redaction이 기본이다.** raw Markdown, Prompt, secret, red-team 원문, 사용자 일지 원문은 일반 로그와 오류 응답에 기록·포함하지 않는다(MUST NOT).
5. **외부 모델 전송도 최소화 대상이다.** 필요한 최소 Knowledge block만 조립하고, red-team corpus와 unsafe example은 production prompt에서 기본 분리하며, Prompt logging은 기본 금지한다(MUST).
6. **등급은 임의로 내려가지 않는다.** `raw-restricted → public-safe` 직접 변경 금지, 공개는 별도 리뷰와 별도 public artifact 분리로만 하며, 예외는 좁고 시한이 있고 감사 가능해야 한다(MUST).
7. **저장 매체는 이 정책을 대체하지 않는다.** 파일이든 PostgreSQL projection이든 동일한 경계가 적용되며, DB 도입은 어떤 규칙도 완화하지 않는다(MUST NOT).

본 정책은 노출 사고 발생, 상위 ADR의 재검토(도입 트리거 발생 포함), 또는 새 노출 표면(신규 배포 채널·신규 API 형태)의 등장 시 재검토하며, 그 전까지 Finance Harness의 유효한 Exposure Policy로 유지된다.
