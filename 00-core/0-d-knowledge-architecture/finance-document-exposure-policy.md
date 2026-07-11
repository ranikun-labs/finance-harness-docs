# Finance Document Exposure Policy

## 0. Metadata

```text
status                : decided v1.0 — MVP target contract
document type         : Exposure Policy / Security Contract
scope                 : Finance Harness Knowledge Artifact가 다음 경계를
                        통과할 때의 노출 축(문서 exposure_class ·
                        distribution_boundary · Runtime output_class ·
                        operational-metadata), 분류별 허용·금지 경로,
                        sanitization·logging·debug·packaging·API 노출 규칙.
                        - authoring repository
                        - build/package
                        - WAS private resource
                        - Finance Runtime
                        - logs/metrics/traces
                        - debug/admin endpoint
                        - public API
                        - Frontend
                        - 사용자 다운로드 artifact
                        - future PostgreSQL projection
decision owner        : Finance Harness Knowledge Architecture
parent decision       : 00-core/0-d-knowledge-architecture/finance-knowledge-storage-decision.md
visibility            : internal-only — 본 문서 자체도 internal-controlled 등급이며
                        사용자 대면 리소스로 노출하지 않는다.
implementation status : contract only / repo verification required
non-goals             :
  - Finance Lens 의미·PolicyGuard intent/action·Routing Contract·Fixture 기대값 변경
  - PostgreSQL schema 설계, DB migration, 실제 코드 구현
  - Auth/Billing 전체 설계, Frontend 화면 설계
  - 새로운 Runtime action enum 추가
```

- 파일명에는 버전을 포함하지 않는다. 내용 버전은 본 Metadata의 status로만 관리한다.
- 충돌 시 우선순위: `00-service-policy.md` > `finance-policyguard-rules.md` > 기타 0-A Safety Core > 상위 결정 문서(`finance-knowledge-storage-decision.md`) 및 본 문서 > 개별 lens/예시 문서. 본 문서는 상위 문서의 규칙을 노출 관점에서 구체화할 뿐 완화하지 않는다.

---

## 1. 문서 목적

본 문서는 Finance Harness의 Knowledge Artifact가 각 시스템 경계를 통과할 때 **무엇을 노출할 수 있고 무엇을 절대 노출하면 안 되는지**를 정의하는 노출 정책이자 보안 계약이다.

이 문서는 저장 위치를 다시 결정하는 문서가 아니다. 저장 결정은 상위 문서 `finance-knowledge-storage-decision.md`가 이미 소유한다. 본 문서는 다음만 소유한다.

```text
- 문서 exposure_class (raw-restricted / internal-controlled / public-safe)
- 노출 축 구조 (exposure_class · distribution_boundary · output_class · operational-metadata)
- 분류별 허용·금지 경로
- sanitization 경계
- logging/redaction 규칙
- debug/admin 노출 규칙
- packaging 검증 규칙
- API response 노출 규칙
- future runtime projection의 노출 제한
- 검증 체크리스트 (Validation Gate / Test Cases)
```

---

## 2. 상위 결정과 범위

### 2.1 전제로 사용하는 상위 결정

본 문서는 `finance-knowledge-storage-decision.md`(decided / MVP target contract)의 다음 결정을 그대로 전제로 사용하며, 재검토하지 않는다.

```text
- Markdown = Authoring Source of Truth
- MVP = WAS private resource
- Finance Runtime 내부 로딩
- Frontend에는 Sanitized API Response만 전달
- PostgreSQL Knowledge DB는 MVP에서 미도입
- DB 저장 여부와 노출 통제는 별개의 문제
- 결정 완료와 구현 완료를 동일하게 취급하지 않음
```

### 2.2 본 문서의 위치

상위 결정 문서 §10.3은 노출 통제의 실질이 packaging boundary, access control, API response contract, logging/redaction, debug/admin exposure에 있다고 정의하고, 그 상세를 본 문서에 위임했다. 본 문서는 그 위임 범위를 채우는 후속 계약이다.

### 2.3 의미 계약과의 관계

intent, guard action, lens key, fixture, PolicyGuard 처리 의미는 각 canonical 문서(`finance-policyguard-rules.md`, `finance-routing-contract.md`, `finance-routing-fixtures.md` 등)가 소유한다. 본 문서는 이를 **참조만** 하며, 새로 정의하거나 변경하지 않는다.

특히 다음 두 가지는 본 문서가 재정의하지 않는다.

```text
- intent → Lens → PolicyGuard → Output Validator 실행 순서
  = finance-routing-contract.md 소유
- Output Validator의 pass / rewrite / fail 의미
  = finance-output-validator.md 소유
```

본 문서가 소유하는 것은 그 실행 결과에 대한 노출 규칙뿐이다: Validator 통과 전 사용자 노출 금지, 내부 원문·Prompt·rule·fixture 정보 제거, 사용자 반환 허용·금지 필드, Public API / Frontend 노출 경계.

---

## 3. 핵심 원칙

1. **저장 매체는 보안이 아니다.** 파일이든 DB든, 노출 통제는 경계마다 별도로 정의하고 별도로 검증한다 (상위 결정 §10.2).
2. **가장 높은 제한 등급이 파일 전체의 기본값이다.** 하나의 문서에 여러 등급의 내용이 섞이면 문서 전체를 가장 제한적인 등급으로 취급한다 (§5).
3. **부분 공개는 원문 절단이 아니라 projection이다.** 원문 일부를 잘라 반환하는 방식은 금지하며, 공개가 필요한 내용은 Finance Runtime이 별도 sanitized projection으로 생성한다 (§5.3).
4. **원문은 서버 밖으로 나가지 않는다.** raw-restricted와 internal-controlled 원문은 어떤 경로(응답, 로그, 오류, 디버그, 다운로드)로도 사용자 측에 도달하지 않는다.
5. **WAS private resource는 배포·접근 경계이지 침해 방어 경계가 아니다.** WAS 자체가 침해된 상황까지 방어하는 독립 보안 경계로 과장하지 않는다 (상위 결정 §10.1).
6. **결정 완료 ≠ 구현 완료.** 본 문서의 모든 규칙은 target contract이며, 실제 구현 여부는 Finance Harness repo 검증 대상이다 (§16).
7. **Prompt Injection 대응은 기존 계약에 위임한다.** 사용자 입력이 원문·프롬프트·규칙 공개를 요구하는 경우의 intent 분류와 처리는 기존 PolicyGuard 계약(`RISK_PROMPT_INJECTION` → `BLOCK_PROMPT_INJECTION`)을 따르며, 본 문서는 "반환하지 않는다"는 노출 결과만 계약한다.

---

## 4. 노출 축과 분류

노출 통제는 하나의 등급 enum이 아니라 **서로 다른 축의 조합**으로 정의한다. 아래 네 축·분류를 하나의 "노출 등급 N종"으로 묶어 표현하지 않는다.

```text
문서 exposure_class        = 콘텐츠 민감도 (본 문서 소유)
  raw-restricted (§4.1) / internal-controlled (§4.2) / public-safe (§4.4)

MVP distribution_boundary  = 배포·접근 경계 (packaging 축, §4.3)
  repo-only / private-resource

Runtime output_class       = Runtime 산출물 분류 (§4.5)
  sanitized-runtime-output

operational-metadata       = 운영 상태 정보 분류 (§4.6)
  — 문서 exposure_class가 아님
```

각 분류는 문서(또는 산출물)가 어디까지 이동할 수 있는지에 대한 계약이다. 필드 표기 형식은 `finance-knowledge-artifact-metadata-standard.md` §10이 소유한다.

### 4.1 raw-restricted (document exposure_class)

가장 제한적인 원문. 공격·실패 데이터와 우회에 직접 참고될 수 있는 상세를 포함한다.

포함 예시:

```text
- Red-team user_input
- unsafe_draft
- forbidden_output
- 공격 패턴 (우회 프레이밍, multi-turn 공격 시나리오)
- Prompt Injection 우회 예시
- 내부 실패 예시 (bad answer 인용, rewrite 전 unsafe 문장)
- validator 우회 가능성이 있는 상세 규칙
```

허용:

```text
- 승인된 내부 authoring/review
- 내부 테스트 (fixture/regression 실행 환경)
- 제한된 Runtime 검증
```

금지:

```text
- Frontend
- public API
- static resource
- CDN
- 사용자 다운로드
- 일반 로그
- 일반 debug endpoint
```

### 4.2 internal-controlled (document exposure_class)

내부 실행 계약. 시스템의 분류·분기·검사·생성 규칙의 원문이다.

포함 예시:

```text
- PolicyGuard Rules
- Routing Contract
- Prompt Template
- Lens Catalog
- Knowledge Architecture 문서 (storage decision, 본 문서 포함)
- Implementation Handoff
```

(Output Validator와 Routing Fixture는 unsafe 차단 출력 예시·정책 우회 입력·forbidden output을 포함하므로 §5.1 최고 제한 우선 원칙에 따라 파일 전체가 raw-restricted다 — §4.1, §6.)

허용:

```text
- 내부 repo
- WAS private resource
- Finance Runtime
```

금지:

```text
- 원문 API 반환
- Frontend bundle
- 일반 사용자 다운로드
- 클라이언트 로그
```

### 4.3 distribution_boundary — repo-only / private-resource (배포·접근 경계 축)

**이 축의 값은 exposure_class가 아니다.** 콘텐츠 민감도(§4.1·§4.2·§4.4)와 분리된 packaging 축이며, MVP 값은 다음 두 가지다.

```text
repo-only         : repo에만 존재. Runtime package에 포함하지 않음
                    (예: ADR, 검토 보고서, handoff 문서)
private-resource  : WAS private resource로 패키징되어
                    Finance Runtime이 로딩하는 내부 배포물
```

private-resource에 대해 다음을 명시한다.

- raw-restricted 또는 internal-controlled 문서가 private-resource로 **패키징될 수 있다**. 패키징되어도 원문 등급(raw-restricted / internal-controlled)의 금지 규칙은 그대로 유지된다.
- private-resource라고 해서 **자동으로 안전한 것은 아니다**. 로딩 이후의 응답·로그·디버그 경로에서 별도 통제가 없으면 원문은 그대로 노출될 수 있다.
- `static/`, `public/` 등 static/public resource와 **명확히 분리**된 경로여야 한다.
- **서버 침해 시 독립 보호 경계가 아니다** (상위 결정 §10.1).

### 4.4 public-safe (document exposure_class)

사용자에게 공개 가능한 일반 설명·정책·교육 콘텐츠.

public-safe로 분류되기 위한 조건 (전부 충족):

```text
- 내부 라우팅 규칙 없음
- Prompt/Validator 원문 없음
- 공격 입력 예시 없음
- 내부 파일 경로 없음
- 금지 표현 탐지 상세 없음
- 시스템 운영 상태 없음
```

허용: Frontend, 공개 문서, 마케팅·고지 영역. 단 문구는 `00-service-policy.md` §12의 마케팅 문구 정책을 따른다.

주의: 현재 첨부·확인된 Knowledge Artifact 중 파일 단위로 public-safe에 해당하는 문서는 없다 (§6). public-safe 콘텐츠가 필요하면 원문 승격이 아니라 별도 작성 또는 sanitized projection으로 만든다.

### 4.5 Runtime output_class — sanitized-runtime-output

Finance Runtime이 내부 Knowledge를 사용한 뒤 생성한 정제 결과. **문서 exposure_class가 아니라 Runtime 산출물의 분류(output_class)** 이며, Frontend에 도달할 수 있는 유일한 분류다. 문서의 exposure_class 필드 값으로 사용할 수 없다 (metadata-standard §10.3).

포함 가능:

```text
- 최종 답변
- applied lens key
- 정제된 checklist
- journal question
- 사용자 고지
- 일반적인 데이터 기준시각
```

포함 금지:

```text
- raw Markdown
- 내부 Prompt Template
- PolicyGuard 원문
- validator rule
- fixture ID 전체
- red-team input
- unsafe draft
- 내부 파일 경로
- stack trace
- content hash
- 내부 version graph
- hidden system instruction
```

### 4.6 operational-metadata (운영 상태 정보 분류)

버전, content hash, deployment 상태(candidate/canary/active 등), routing_enabled, audit 기록처럼 **문서 의미가 아니라 운영 상태를 나타내는 정보**의 분류다.

- **문서 exposure_class가 아니다.** 문서 front matter에 운영 상태 필드를 기록하는 것 자체가 금지다 (metadata-standard §11.3).
- 사용자 응답(sanitized-runtime-output)에 기본적으로 포함될 수 없다 (§4.5 포함 금지 목록의 content_hash·내부 version graph와 동일 원칙).
- 운영자도 필요한 최소 범위만 접근하며, 내부 문서 본문과 분리해 관리한다.

---

## 5. 등급 결정 규칙

본 절의 "등급"은 문서 exposure_class(§4.1·§4.2·§4.4)를 의미한다.

### 5.1 최고 제한 등급 우선

하나의 문서에 여러 등급의 내용이 섞여 있다면 **가장 높은 제한 등급을 문서 전체의 기본값으로 적용**한다.

예:

- 좋은 응답 예시 + unsafe output이 함께 있으면 → **raw-restricted** (실제 사례: `finance-output-examples.md`, §6 참조)
- 일반 Lens 설명 + 내부 forbidden output이 함께 있으면 → **internal-controlled 이상**
- 일부 문단만 공개 가능하더라도 원문 파일 전체를 public-safe로 승격하지 않는다.

### 5.2 등급의 방향

등급 조정은 제한 강화(상향)만 문서 소유자의 판단으로 가능하며, 제한 완화(하향)는 본 문서의 개정 없이 할 수 없다.

### 5.3 부분 공개 규칙

부분 공개가 필요한 경우 Finance Runtime이 **별도 sanitized projection을 생성**해야 하며, 원문 일부를 직접 잘라서 반환하지 않는다. 예를 들어 lens 체크리스트가 사용자에게 전달되는 것은 lens 문서 원문의 인용이 아니라, Runtime이 생성한 sanitized-runtime-output(§4.5)으로서다.

### 5.4 미분류 문서의 기본값

exposure class가 지정되지 않은 신규 문서는 분류 확정 전까지 **internal-controlled로 보수 취급**하며, unsafe 예시·공격 입력 포함이 확인되면 raw-restricted로 상향한다.

---

## 6. 문서별 기본 분류

첨부 문서는 실제 내용을 확인하고 분류했다. 미첨부 문서(※ 표기)는 canonical 참조 문서(`finance-policyguard-alignment.md`, `finance-harness-mvp-implementation-handoff.md`, `finance-routing-contract.md`)에 기록된 내용 근거로 분류했으며, repo 검증 시 원문 확인으로 확정한다.

| document | default exposure class | allowed locations | forbidden exposure | reason |
|---|---|---|---|---|
| 00-service-policy.md | internal-controlled | 내부 repo, WAS private resource, Finance Runtime | 원문 API 반환, Frontend bundle, 사용자 다운로드, 클라이언트 로그 | 최상위 서비스 정책. 다만 §9 Output Guard 검사 대상 표현 목록, §10 우회 대응 정책 등 탐지 상세를 포함하므로 파일 원문은 내부 한정. 서비스 정체성·고지 문구 등 공개 가능한 내용은 별도 public-safe 콘텐츠로 재작성해 사용한다. |
| finance-policyguard-rules.md | internal-controlled | 상동 | 상동 | intent 12종 정의, §5 금지 표현군, §7.1 Input Guard 키워드/패턴 표 등 분류·탐지 상세를 포함. 원문 노출은 우회 시도의 직접 참고 자료가 된다. |
| finance-output-validator.md | **raw-restricted** | 승인된 내부 authoring/review, 내부 테스트, 제한된 Runtime 검증 | Frontend, public API, static resource, CDN, 사용자 다운로드, 일반 로그, 일반 debug endpoint | internal-only 명시 확인. §5 unsafe 차단 출력 예시와 validator 우회에 활용 가능한 상세 실패 판정 규칙을 포함한다. "파일 전체 internal-controlled + 일부 문장만 raw-restricted"의 이중 취급은 금지 구조이므로, §5.1 최고 제한 우선 원칙에 따라 파일 전체를 raw-restricted로 확정한다. |
| finance-prompt-templates.md | internal-controlled | 상동 | 상동 | internal-only 명시 확인. LLM 프롬프트에 주입되는 생성 제한과 intent/action→템플릿 매핑 원문. 노출 시 Generation Guard 우회에 직접 참고된다. |
| finance-routing-fixtures.md ※ | **raw-restricted** | 승인된 내부 authoring/review, 내부 테스트, 제한된 Runtime 검증 | Frontend, public API, static resource, CDN, 사용자 다운로드, 일반 로그, 일반 debug endpoint | 미첨부. 참조 근거(handoff §1: 32개 fixture, alignment §2: guard action enum 소유)상 정책 우회 입력, forbidden output, expected intent/action, 정책 경계 탐색이 가능한 regression 기대값을 포함하는 실행 계약이다. §5.1 최고 제한 우선 원칙에 따라 파일 전체를 raw-restricted로 확정한다 (repo 검증은 분류 확인용이며, 하향 근거가 아니다). |
| finance-market-data-grounding.md ※ | internal-controlled | 상동 | 상동 | 미첨부. 참조 근거(validator §10, routing-contract §10 연동)상 데이터 단정 금지의 판정 규칙을 소유하는 0-A Safety Core 계약. |
| finance-lens-catalog.md ※ | internal-controlled | 상동 | 상동 | 미첨부. 참조 근거(alignment §2: lens key·trigger 의미의 canonical)상 lens 등록 원본. lens key 자체는 응답에 노출 가능하나(§9), catalog 원문(trigger·금지 표현 연결 포함)은 내부 한정. |
| finance-lens-authoring-standard.md ※ | internal-controlled | 상동 | 상동 | 미첨부. 렌즈 작성 표준 = 내부 authoring 계약. 사용자 가치가 없고 내부 구조만 드러낸다. |
| finance-routing-contract.md | internal-controlled | 상동 | 상동 | 질문 입력부터 노출까지의 전체 분기 규칙, intent priority, fallback 동작을 포함한 실행 파이프라인 계약. 분기 조건의 노출은 경계 탐색(우회 지점 찾기)에 직접 참고된다. |
| finance-output-examples.md | **raw-restricted** | 승인된 내부 authoring/review, 내부 테스트, 제한된 Runtime 검증 | Frontend, public API, static resource, CDN, 사용자 다운로드, 일반 로그, 일반 debug endpoint | internal-only 명시 + §11 Bad Example Catalog(금지 인용, CRITICAL 위반 문장), §12 Rewrite Before/After(unsafe draft) 포함 확인. 좋은 예시가 함께 있으나 §5.1 최고 등급 우선 원칙으로 파일 전체를 raw-restricted로 취급한다. |
| finance-red-team-cases.md | **raw-restricted** | 상동 | 상동 | user_input / unsafe_draft / forbidden_output 필드와 RT-D~RT-V 공격 시나리오(완곡어법, 역할극, prompt injection, multi-turn 공격) 포함 확인. 공격·실패 데이터의 대표 문서. |
| finance-policyguard-alignment.md | internal-controlled | 내부 repo (검토 전용) | 원문 API 반환, Frontend, 사용자 다운로드, 클라이언트 로그, 사용자 대면 콘텐츠 재사용 | internal-only(내부 검토 전용) 명시 확인. 문서 간 충돌 기록과 canonical 우선순위 등 내부 정합성 정보. 검토 보고서이므로 runtime 로딩 대상도 아니다. |
| finance-knowledge-storage-decision.md | internal-controlled | 내부 repo, WAS private resource(포함될 경우), Finance Runtime | 원문 API 반환, Frontend bundle, 사용자 다운로드, 클라이언트 로그 | Knowledge Architecture 결정 문서. 내부 저장 구조·경계 설계를 기술하므로 내부 한정. |
| finance-harness-mvp-implementation-handoff.md | internal-controlled | 내부 repo | 상동 | internal-only 명시 확인. 구현 계획, API 후보, 도메인 모델 등 내부 구조 정보. |
| 01-core-analysis-lenses/finance-lens-*.md (10종) ※ | internal-controlled | 내부 repo, WAS private resource, Finance Runtime | 원문 API 반환, Frontend bundle, 사용자 다운로드, 클라이언트 로그 | 미첨부. 참조 근거(routing-contract §7, red-team 소스 목록)상 lens 본문은 체크리스트 외에 trigger, forbidden intent, redirect, fixture 연결을 포함한다. 체크리스트 내용은 §5.3에 따라 sanitized projection으로만 사용자에게 전달한다. |

분류 요약: raw-restricted 4건(output-examples, red-team-cases, output-validator, routing-fixtures), internal-controlled 11건(렌즈 10종 1개 항목 포함), public-safe 해당 파일 없음.

---

## 7. Packaging Boundary

다음을 build/package 단계의 계약으로 정의한다.

- Knowledge Markdown은 **Frontend build 대상에 포함하지 않는다**.
- `static/`, `public/`, CDN, downloadable artifact에 Knowledge 문서를 포함하는 것을 금지한다.
- Knowledge 문서는 **WAS private resource 경로에서만 로딩**한다.
- private resource 목록을 반환하는 **resource listing endpoint를 두지 않는다**.
- 리소스 로딩 경로에 대한 **directory traversal을 방지**한다 (사용자 입력이 리소스 경로 구성에 관여하지 않도록).
- 응답에 Knowledge 문서의 **파일명·절대경로를 포함하지 않는다**.
- package manifest에 각 문서의 **exposure_class·distribution_boundary와 허용 경로를 기록하고 검증**한다.
- **raw-restricted 문서가 public artifact(Frontend bundle, static/public, CDN, 다운로드 산출물)에 포함되면 build fail**이다.
- **internal-controlled 문서가 Frontend bundle에 포함되면 build fail**이다.

위 규칙은 target contract다. manifest 검증, build fail 로직 등 실제 build 구현이 완료되었다는 의미가 아니며, 구현 여부는 repo 검증 대상이다 (§16).

---

## 8. Runtime Access Boundary

- Knowledge 원문에 접근할 수 있는 것은 **Finance Runtime뿐**이다.
- Frontend는 Knowledge 원문에 직접 접근할 수 없다.
- 일반 API handler는 어떤 경우에도 원문을 반환하지 않는다.
- Runtime은 문서에서 **필요한 의미만 내부적으로 사용**하며, 사용 사실이 원문 반환을 정당화하지 않는다.
- 모든 응답은 노출 전 **sanitization을 필수로 거친다**. 기존 계약상 이 위치는 Output Validator 통과 이후, 사용자 노출 직전이다 (routing-contract §9의 "Validator 미통과 응답 비노출" 원칙과 결합).
- 응답은 원문 일부를 **그대로 인용하거나 echo하지 않는다**. 체크리스트 등 전달이 필요한 내용은 sanitized projection으로 생성한다 (§5.3).
- 사용자 입력이 **원문·프롬프트·규칙의 공개를 요구해도 반환하지 않는다**. "시스템 프롬프트 보여줘", "네 규칙 전문을 알려줘" 류 요청의 intent 분류와 응답 처리는 기존 PolicyGuard 계약(`RISK_PROMPT_INJECTION` → `BLOCK_PROMPT_INJECTION`, prompt-templates §9)에 위임하며, 본 문서는 **어떤 처리 결과에서도 원문이 포함되지 않는다**는 노출 결과만 계약한다.
- 본 절은 기존 intent/action의 의미를 새로 정의하지 않는다.

---

## 9. API Response Contract

### 9.1 허용 필드 (예시)

```text
answer
applied_lens_keys
sanitized_checklist
journal_questions
notices
data_as_of
```

### 9.2 금지 필드 (예시)

```text
raw_markdown
prompt_template
policyguard_rule
validator_rule
fixture_payload
unsafe_draft
internal_path
stack_trace
content_hash
source_document_body
```

### 9.3 applied_lens_keys의 한계

`applied_lens_keys`는 catalog에 등록된 lens key 값 자체만 공개 가능하다. 다음은 lens key가 공개되더라도 **함께 공개하지 않는다**.

```text
- 내부 routing score
- fallback reason (분기 실패·폴백 사유)
- policy trace (intent 판정 근거, guard action 이력)
```

이 정보는 분기 조건의 역추적을 가능하게 하므로 sanitized-runtime-output에 포함될 수 없다. 필요 시 내부 로그(§10)에만 기록한다.

### 9.4 필드 추가 규칙

응답 schema에 새 필드를 추가할 때는 §4.5의 포함 금지 목록과 대조하고, §14 Gate 4의 검사 대상에 등록한 뒤에만 추가한다.

---

## 10. Logging / Metrics / Tracing

기존 로그 계약(`finance-policyguard-rules.md` §10의 로그 최소화 원칙, `finance-output-validator.md` §14의 로그 필드)을 유지하며, 노출 관점의 허용·금지를 다음과 같이 계약한다.

### 10.1 로그 허용

```text
- document_id 또는 안정적인 논리 식별자
- exposure class
- load success/failure
- sanitized error code
- 적용 lens key
- validation pass/fail
- latency
- aggregate count
```

### 10.2 로그 금지

```text
- 문서 원문
- Prompt Template 원문
- Red-team input
- unsafe output
- 사용자의 민감한 투자 일지 원문
- 파일 절대경로
- stack trace 내 문서 본문
- raw LLM prompt 전체
- API response 전체 덤프
```

### 10.3 Metrics

- metric label의 cardinality를 제한한다 (label 값은 enum 성격의 안정 식별자만).
- **문서 본문·사용자 입력을 label로 사용하지 않는다.**

### 10.4 Trace

- span attribute에 **원문, prompt, 사용자 금융정보를 넣지 않는다**.
- trace에 담을 수 있는 것은 §10.1 허용 목록과 동일한 수준의 식별자·상태 값이다.

---

## 11. Error Handling

에러 메시지에는 다음을 노출하지 않는다.

```text
- 내부 문서 파일명 전체
- 파일 절대경로
- Prompt/Policy 내용
- fixture ID 세부
- raw parse error body
- stack trace
```

- 외부 응답은 **안정적인 error code와 일반 메시지**만 사용한다.
- 내부 조사에 필요한 상세 정보는 제한된 내부 로그로 분리하되, **그 로그에도 원문을 기본적으로 남기지 않는다** (§10.2). 원문 없이 재현 가능한 식별자(document_id, 위치 offset, error code)로 기록한다.

---

## 12. Debug / Admin Boundary

### 12.1 MVP 기본 원칙

MVP에는 다음 endpoint·기능을 두지 않는다.

```text
- raw document 조회 endpoint 없음
- full prompt 조회 endpoint 없음
- PolicyGuard 전체 dump 없음
- validator rule dump 없음
- red-team case 조회 없음
- 일반 운영자 UI에서 원문 노출 없음
```

### 12.2 향후 admin 기능이 필요한 경우

향후 운영상 admin 기능이 필요해지더라도, 다음을 갖추어야 한다.

```text
- 별도 인증·권한
- 감사 로그
- 최소 필드 (원문이 아닌 식별자·상태 중심)
- 원문 기본 비노출 (명시적·감사되는 예외 절차 없이는 원문 접근 불가)
- 사용 목적 제한
- 다운로드 제한
```

위 요건은 필요 조건의 계약이며, 이러한 admin 기능이 구현되어 있다는 의미가 아니다.

---

## 13. Future PostgreSQL Projection

상위 결정에 따라 PostgreSQL은 MVP에서 미도입이며, 향후 도입 시에도 다음 노출 규칙이 유지된다.

### 13.1 등급 불변 원칙

**PostgreSQL이 도입돼도 문서 exposure_class는 유지된다.** DB에 저장되었다고 public-safe가 되는 것이 아니다. raw-restricted 문서는 DB 안에서도 raw-restricted다. Registry/projection이 저장하는 exposure_class 값은 문서 exposure_class 3종(raw-restricted / internal-controlled / public-safe)으로 제한되며, sanitized-runtime-output·operational-metadata를 문서 등급 값으로 저장하지 않는다.

### 13.2 DB projection이 허용할 수 있는 운영 metadata (예)

```text
- document_id
- content_hash
- version
- lifecycle_status        (문서 authoring lifecycle — metadata-standard §8)
- exposure_class          (문서 exposure_class 3종으로 제한)
- dependency
- deployment_status       (candidate/canary/active 등 운영 배포 축 —
                           문서 lifecycle_status와 별개, Control Plane 소유)
```

이 metadata는 운영 조회용이며, 이 중 content_hash·version graph·상태 정보는 sanitized-runtime-output에 포함될 수 없다 (§4.5).

### 13.3 원문 content를 저장하는 경우의 요건

원문 content를 DB에 저장하는 경우에도 다음을 요구한다.

```text
- 최소 권한
- 별도 role (runtime 조회용 role과 원문 접근 role 분리)
- raw 조회 제한
- API 직접 노출 금지
- 감사 로그
- Runtime projection 전용 (원문 테이블을 일반 서비스 쿼리가 직접 읽지 않음)
```

### 13.4 이중 Source of Truth 금지 유지

Markdown과 DB를 사람이 각각 수정하는 이중 Source of Truth는 상위 결정과 동일하게 **계속 금지**한다. DB는 검증된 projection의 저장처일 뿐 의미의 수정처가 아니다.

---

## 14. Exposure Validation Gate

노출 정책의 검증 지점을 6개 Gate로 정의한다.

Gate의 "실패 시 action"은 본 문서의 **운영 action 어휘**(build fail / deployment block / runtime load reject / response reject / redaction / alert)로 표현한다. 이는 Runtime의 PolicyGuard guard action enum(pass/block/rewrite/fallback 등)과 **다른 계층의 어휘**이며, 새로운 PolicyGuard action을 신설하는 것이 아니다. 두 어휘를 혼용하지 않는다.

### Gate 1. Authoring Gate

- 검사 대상: 신규·변경 문서의 exposure class 표기, internal-only 표기, §6 기본 분류와의 정합, §5 분류 원칙 준수.
- 실패 조건: exposure class 누락, §6 분류의 무단 하향, unsafe 예시 포함 문서의 raw-restricted 미지정.
- 실패 시 action: build fail (리뷰 반려로 병합·빌드 진입 차단).
- 구현 상태: not implemented / repo verification required.

### Gate 2. Build/Packaging Gate

- 검사 대상: Frontend bundle, `static/`·`public/` 경로, CDN 산출물, 다운로드 artifact의 내용물과 package manifest의 등급-경로 매핑.
- 실패 조건: raw-restricted 문서가 public artifact에 포함, internal-controlled 문서가 Frontend bundle에 포함, manifest와 실제 패키징 불일치.
- 실패 시 action: build fail.
- 구현 상태: not implemented / repo verification required.

### Gate 3. Runtime Load Gate

- 검사 대상: Finance Runtime이 로딩하는 리소스의 경로와 등급 (private resource 경로 준수, 미분류 문서 여부).
- 실패 조건: private resource 외 경로에서의 로딩 시도, exposure class 미지정 문서의 로딩, 경로 조작(traversal) 흔적.
- 실패 시 action: runtime load reject + alert.
- 구현 상태: not implemented / repo verification required.

### Gate 4. Response Sanitization Gate

- 검사 대상: public API 응답의 필드와 값 (§9 허용·금지 필드, §4.5 포함 금지 목록, 원문 인용·echo 여부, 내부 경로·hash 포함 여부).
- 실패 조건: 금지 필드 존재, 원문·경로·hash·trace성 정보 포함, routing score/fallback reason/policy trace 노출.
- 실패 시 action: response reject (필드 단위로 안전 제거가 확정적인 경우 redaction) + alert.
- 구현 상태: not implemented / repo verification required.

### Gate 5. Logging/Tracing Gate

- 검사 대상: 로그 레코드, metric label, trace span attribute (§10 허용·금지 목록).
- 실패 조건: 문서 원문·prompt 원문·red-team input·unsafe output·사용자 일지 원문·절대경로의 기록, 본문성 label 사용.
- 실패 시 action: redaction + alert.
- 구현 상태: not implemented / repo verification required.

### Gate 6. Release Regression Gate

- 검사 대상: §15 테스트 케이스 전체의 통과 여부 (배포 후보 산출물 기준).
- 실패 조건: §15 케이스 중 1건 이상 실패.
- 실패 시 action: deployment block.
- 구현 상태: not implemented / repo verification required.

---

## 15. Test Cases

노출 정책의 최소 검증 케이스를 정의한다. 전 케이스가 Gate 6의 회귀 대상이다.

| ID | 시나리오 | 기대 결과 | 연결 Gate |
|---|---|---|---|
| EXP-01 | raw-restricted 문서(예: red-team cases)가 Frontend bundle에 포함됨 | **실패** — build fail | Gate 2 |
| EXP-02 | internal-controlled 문서가 `public/`·`static/` 경로에 존재 | **실패** — build fail | Gate 2 |
| EXP-03 | API 응답에 `raw_markdown` 필드가 존재 | **실패** — response reject | Gate 4 |
| EXP-04 | 응답 본문·필드에 내부 파일 경로 포함 | **실패** — response reject | Gate 4 |
| EXP-05 | 로그에 Prompt Template 원문이 포함 | **실패** — redaction + alert | Gate 5 |
| EXP-06 | trace span attribute에 사용자 투자 일지 원문 존재 | **실패** — redaction + alert | Gate 5 |
| EXP-07 | 응답이 §9.1 허용 필드로만 구성된 sanitized output | **성공** | Gate 4 |
| EXP-08 | 응답에 applied lens key 포함, routing score·fallback reason·policy trace 미포함 | **성공** (lens key 허용) / trace성 정보가 포함되면 **실패** | Gate 4 |
| EXP-09 | debug endpoint 호출이 문서 원문을 반환 | **실패** — response reject + alert (해당 endpoint 자체가 MVP 금지, §12.1) | Gate 4 / Gate 6 |
| EXP-10 | future DB projection에서 일반 runtime role이 raw content 테이블을 직접 조회 가능 | **실패** — deployment block (권한 과다, §13.3) | Gate 6 |

- 테스트의 입력으로 red-team 케이스를 사용할 경우, 해당 입력·기대 출력 데이터 자체가 raw-restricted이므로 테스트 리포트·CI 로그에 원문을 남기지 않는다.

---

## 16. Implementation Status

- 본 문서는 **아키텍처·보안 계약**이다.
- packaging validator **구현 완료를 의미하지 않는다**.
- Runtime sanitizer **구현 완료를 의미하지 않는다**.
- logging redaction **구현 완료를 의미하지 않는다**.
- API schema **적용 완료를 의미하지 않는다**.
- 실제 구현 여부는 **Finance Harness repo 검증 대상**이다.

상태 구분:

```text
decided / MVP target contract
- 노출 축과 분류 (§4 — 문서 exposure_class 3종 / distribution_boundary / Runtime output_class / operational-metadata)
- 등급 결정 규칙 (§5)
- 문서별 기본 분류 (§6 — 미첨부 문서는 repo 검증으로 확정)
- Packaging / Runtime / API / Logging / Error / Debug 경계 (§7~§12)
- Future PG projection 노출 제한 (§13)
- Validation Gate 6종과 테스트 케이스 (§14~§15)

proposed / deferred
- PostgreSQL projection의 실제 도입 (상위 결정 §11 트리거 발생 시)
- admin 기능 (§12.2 요건 충족 전제)
- 미첨부 문서의 repo 원문 확인 (§6 분류의 사실 확인용 — routing-fixtures는 이미 파일 전체 raw-restricted로 확정이며, 확인 결과는 하향·재분류 근거가 아니다)

not implemented / repo verification required
- package manifest 등급 검증
- build fail 로직
- runtime load reject
- response sanitization 검사
- logging/trace redaction
- §15 테스트 케이스의 CI 통합
```

---

## 17. Non-goals

다음은 본 문서의 범위에서 제외한다.

```text
- Finance Lens 의미 수정
- PolicyGuard intent/action 변경
- Routing Contract 변경
- Fixture 기대값 변경
- PostgreSQL schema 설계
- Auth/Billing 전체 설계
- Frontend 화면 설계
- 실제 코드 구현
- DB migration
- 새로운 Runtime action enum 추가
```

---

## 18. 최종 정책

```text
Finance Harness의 노출 통제는 하나의 등급 enum이 아니라
다음 축의 조합으로 관리한다.

문서 exposure_class : raw-restricted / internal-controlled / public-safe
distribution_boundary: repo-only / private-resource (배포·접근 경계)
Runtime output_class : sanitized-runtime-output
operational-metadata : 운영 상태 정보 분류

원문(raw-restricted, internal-controlled)은 서버 내부에 머물고,
Frontend와 사용자에게 도달할 수 있는 것은
sanitized-runtime-output뿐이다.
```

- 혼합 문서는 가장 높은 제한 등급을 따르고, 부분 공개는 원문 절단이 아니라 Runtime의 sanitized projection으로만 한다.
- repo-only·private-resource는 exposure_class가 아니라 distribution_boundary(배포·접근 경계)이며, 저장 매체(파일/DB)의 선택은 노출 통제를 대체하지 않는다. WAS 침해까지 방어하는 독립 보안 경계로 과장하지 않는다.
- packaging, runtime access, API response, logging/metrics/tracing, error, debug/admin의 6개 경계는 각각 §14의 Gate로 검증하며, raw-restricted의 public artifact 포함과 internal-controlled의 Frontend bundle 포함은 build fail이다.
- future PostgreSQL projection이 도입되어도 문서 exposure_class와 이중 Source of Truth 금지는 그대로 유지된다.
- 본 문서의 모든 규칙은 decided / MVP target contract이며, 구현 완료를 주장하지 않는다. 구현 여부는 Finance Harness repo 검증으로 확인한다.
