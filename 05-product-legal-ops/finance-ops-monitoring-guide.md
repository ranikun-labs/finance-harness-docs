# Finance Operations Owner Monitoring Guide

## 0. Metadata

```text
status                : canonical internal operational baseline v1.0
document type         : Operations Owner Monitoring Guide
document_id           : finance-ops-monitoring-guide
authority             : canonical internal operational baseline
lifecycle_status      : active
implementation_status : internal-control-baseline
runtime_loadable      : false
routable              : false
exposure_class        : internal-controlled (보수 분류 — 실패 시나리오·탐지
                        방식 서술 포함)
distribution_boundary : repo-only — Runtime package에 포함하지 않음
external_review       : not externally reviewed
legal_advice          : false
legal_approval        : false
public_launch_gate    : unresolved
paid_launch_gate      : unresolved
decision owner        : Operations Owner / Security Owner / Privacy Owner /
                        Product Owner / Professional Standards Owner
                        (prototype stage: may be held by Project Owner)
parent references     :
  [0-A] 00-service-policy.md, finance-policyguard-rules.md (§9~§10),
        finance-output-validator.md (§14), finance-market-data-grounding.md,
        finance-prompt-templates.md, finance-routing-fixtures.md (§10)
  [0-C] finance-routing-contract.md, finance-output-examples.md,
        finance-red-team-cases.md (Promotion Gate)
  [0-D] finance-knowledge-storage-decision.md,
        finance-document-exposure-policy.md (§16 Validation Gate),
        finance-knowledge-artifact-metadata-standard.md
  [05] finance-legal-risk-review-notes.md (IN-OMG-01 ~ IN-OMG-05)
       finance-user-data-policy.md (OUT-OMG-01 ~ 06, R-IBH-01 ~ 08)
       finance-disclaimer-and-copy-guide.md (OUT-OMG-C-01 ~ 06)
non-goals             : §2.2 참조
```

- 충돌 시 우선순위: `00-service-policy.md` > `finance-policyguard-rules.md` > 기타 0-A > 0-C/0-D > **본 문서**. `00-core` safety and Runtime contracts take precedence over this document. 본 문서는 관찰·검토·에스컬레이션 기준만 정하며, canonical 규칙과 fixture expected behavior를 변경하지 않는다.
- **내부 운영 기준 한정**: 본 문서는 canonical internal operational baseline이다. 법률 자문, 법무 승인, 외부 검토 의견, 공개 출시 승인, 유료 출시 승인이 아니다. Public launch and paid launch gates remain unresolved.
- **Runbook 아님**: 본 문서는 "무엇을 관찰하고, 무엇을 이상으로 보고, 누가 검토하는가"의 내부 운영 기준이다. 실제 인프라 구성·도구 선택·threshold 확정은 후속 구현 결정이다 (§21).
- **Runtime 구현 미존재 전제**: 본 문서는 구현이 이미 존재한다고 가정하지 않는다. 모든 이벤트·지표는 구현 시 반영할 요구사항 후보다.
- **External Legal Review**: unassigned; future public-launch and paid-launch gate. Unresolved legal and provider questions remain unresolved.

### 표기 규칙 (3구분)

| 표기 | 의미 |
|---|---|
| `[canonical]` | canonical 문서에 확정된 운영·안전 원칙 — 인용만 함 |
| `[제안]` | 본 문서가 제안하는 운영 정책 후보 |
| `[미확정]` | 구현·future External Legal Review·보안 결정이 필요한 사항 (필요 시 `외부 법무 검토 필요` 병기) |

미확정 수치 상태 값: `NOT-COLLECTED` / `SESSION-ONLY` / `USER-CONTROLLED` / `RETENTION-TO-BE-DECIDED` / `THRESHOLD-TO-BE-DECIDED` / `LEGAL-REVIEW-REQUIRED` / `OWNER-DECISION-REQUIRED`

---

## 1. Document Status

| 항목 | 값 |
|---|---|
| document type | Operations Owner Monitoring Guide |
| authority | canonical internal operational baseline |
| lifecycle | active |
| runtime_loadable | false |
| routable | false |
| distribution_boundary | repo-only |
| implementation_status | internal-control-baseline |
| external_review | not externally reviewed |
| legal_advice | false |
| legal_approval | false |
| public_launch_gate | unresolved |
| paid_launch_gate | unresolved |

---

## 2. Purpose and Scope

### 2.1 목적

**Finance Harness의 안전 정책·사용자 데이터·카피·시장 데이터·LLM 전송·삭제 처리·관리자 접근·장애/품질 문제를 운영 중 어떻게 감시·기록·검토·에스컬레이션할지 정의한다.**

본 문서가 정의하는 것:

```text
- 무엇을 관찰할지 (§4 domains, §6 metrics)
- 어떤 이벤트를 기록할지 (§5 events, §7 logging)
- 어떤 상태를 이상으로 볼지 (§8~§14 실패 정의)
- 누가 검토할지 (§20 ownership)
- 언제 배포 중단·rollback을 검토할지 (§15 severity, §17 release checks)
- 어떤 실패를 fixture/red-team candidate로 넘길지 (§18 intake)
- 어떤 사항이 별도 구현 또는 법무 결정 대상인지 (§21~§22)
```

### 2.2 Authority hierarchy

본 문서의 모든 운영 기준은 아래 우선순위에 종속된다.

1. `00-core` safety and Runtime contracts
2. applicable canonical product contracts
3. `05-product-legal-ops` internal operational controls
4. `06-professional-standards` human-review and professional standards where applicable

본 문서는 service purpose, PolicyGuard intents/actions, Output Validator
behavior, market-data grounding rules, Lens routing, Runtime activation, or
human-review standards를 재정의하지 않는다. `00-core` controls over any
conflicting `05` wording.

### 2.3 포함/제외 범위

포함: PolicyGuard/Validator 실행 결과, rewrite/fallback/fail 흐름, Routing·Lens 선택 결과, 시장 데이터 grounding 상태, 사용자 데이터 로그 최소화, LLM 전송 필드 검증, 관리자 접근 감사, 삭제·보존, 카피 회귀, 품질 실패·민원, incident triage, fixture/red-team intake, 배포 전 점검, 사후 리뷰.

제외 (non-goals):

```text
- 실제 모니터링 도구·벤더 선택       - 인프라 구축 코드
- 최종 SLO/SLA 확정                  - 법률 사고 대응 최종 Runbook
- 개인정보처리방침                    - PolicyGuard 규칙 변경
- fixture expected behavior 변경     - 실제 alert threshold 확정
- Lens 문서 수정
```

### 2.4 Launch and Feature Gates

- Persistent user data must not be enabled for closed beta until an
  owner-approved retention, deletion, backup, and audit treatment exists. The
  treatment may contain internally selected operational periods, but such
  periods must not be labeled legally mandated unless verified.
- No public landing page, application-store listing, SNS campaign, paid-plan
  copy, or other public marketing copy may ship until the public-facing copy
  has been separately reviewed.
- Paid launch is blocked pending a separate paid-service review.
- Phase 1 remains free of external market-data redistribution. No external
  market-data display, caching, screenshot use, derived-data use,
  redistribution, or AI grounding use may be enabled until provider-specific
  license and redistribution terms are documented.
- User-entered values remain user input, not verified market data.

---

## 3. Canonical Operational Boundaries

본 절 전체가 `[canonical]`이다. 이하 모든 제안은 이 경계 아래에 종속된다.

| # | 경계 | 근거 |
|---|---|---|
| C-01 | 사용자 응답에는 **sanitized output만** 노출된다 — Validator 통과 전 응답의 사용자 노출 금지 | routing-contract, validator, storage decision §5 |
| C-02 | raw-restricted / internal-controlled 원문은 응답·로그·디버그·오류 메시지 어떤 경로로도 사용자 측에 노출되지 않는다 | exposure policy §3~§4 |
| C-03 | PolicyGuard/Validator 로그는 **metadata 중심** — intent/action/카테고리 키. blocked_terms도 키만 저장 | policyguard §10, validator §14 |
| C-04 | 가격·수량·질문 원문을 불필요하게 로그에 남기지 않는다 | policyguard §10.2, 서비스 정책 §13 |
| C-05 | 사용자별 장기 프로파일링 금지 — 로그 목적은 디버깅·정책 개선에 한정 | policyguard §10.2 |
| C-06 | 시장 데이터 표시에는 출처·기준시각·지연 여부가 필요하다. 기준시각 없는 데이터는 표시하지 않는다 | grounding §4 |
| C-07 | 기능 미존재 상태(계좌 연동·OCR·자동매매·실시간 시세)를 실제 제공 기능처럼 운영·표시하지 않는다 | 서비스 정책 §2·§5·§13 |
| C-08 | 기계 gate 실패를 사람 승인으로 override하지 않는다 — 실패는 수정 후 재통과만 가능 | exposure policy §16 Validation Gate, fixtures §1 ("기준 미통과 변경은 배포 불가") |
| C-09 | fixture/red-team 승격은 canonical owner 승인(Promotion Gate)이 필요하다 — 운영 발견의 자동 승격 금지 | red-team-cases Promotion Gate, `finance-legal-risk-review-notes.md` IN-OMG-03 |
| C-10 | Redirect/거절 응답도 Output Guard 검사를 동일하게 거친다 — 안전 경로라는 이유로 검사를 면제하지 않음 | policyguard §6·§9 |

---

## 4. Monitoring Domains `[제안]`

| Domain ID | 운영 영역 | 관찰 대상 | 주요 실패 | canonical source | owner | blocking |
|---|---|---|---|---|---|:---:|
| DM-01 | Professional Standards Owner Guard | intent 분류, guard action 분포 | 위험 질문의 SAFE 오분류, 거절 선언 누락 | policyguard §2~§7, fixtures §10 | Professional Standards Owner | **Yes** |
| DM-02 | Output Validation | validator pass/fail, violation category, rewrite/fallback | 금지 표현 pass, rewrite 후 잔존 | validator 전반 | Professional Standards Owner | **Yes** |
| DM-03 | Routing | intent→action 매핑 준수, UNKNOWN 비율 | 계약 외 action, 실행 순서 위반 | routing-contract §2, templates §14 | Operations Owner | **Yes** |
| DM-04 | Lens selection | 선택 lens의 등록 여부·적합성 | 미등록 lens 명 사용, lens miss | fixtures §10 ("정의되지 않은 lens 이름"), routing-contract | Operations Owner + Professional Standards Owner | Yes |
| DM-05 | Market data grounding | 출처/기준시각/지연 표시, 스테일·이상치 (Phase 1.5+) | 라벨 누락 표시, 스테일 값 최신 표시 | grounding §3~§4 | Operations Owner | Yes (Phase 1.5+) |
| DM-06 | User data protection | 로그 최소화, 민감 필드 마스킹 | 로그 내 원문/가격/수량 혼입 | user-data-policy §9, C-04 | Security Owner + Operations Owner | **Yes** |
| DM-07 | LLM transmission | 전송 필드 명세 대비 실제 전송 | 명세 외 필드 전송, 식별자 포함 | user-data-policy §8 | Operations Owner + Security Owner | **Yes** |
| DM-08 | Access control | Restricted 조회, 권한 변경 | 사유 없는 조회, 마스킹 우회 | user-data-policy §11 | Security Owner | Yes |
| DM-09 | Retention and deletion | 삭제 요청→완료, 보존 상한 | 삭제 실패, 파생 잔존, 상한 초과 | user-data-policy §10 | Operations Owner | Yes |
| DM-10 | Copy and disclaimer | 화면·CTA·고지 상태 | 금지 CTA 노출, REQUIRED 고지 누락 | copy-guide §3~§14 | Product Owner + Professional Standards Owner | Yes |
| DM-11 | Public copy approval | 대외 카피 승인 이력 | 미승인 카피 집행 | copy-guide §13·§15 | Product Owner + future External Legal Review | Yes |
| DM-12 | Incident response | 사고 탐지→종결 흐름 | 미분류 사고, 조치 누락 | 본 문서 §15~§16 | Operations Owner | Yes |
| DM-13 | Regression and release | fixture/validator regression, 배포 gate | gate 실패 상태의 배포 | fixtures §1·§10, exposure policy §16 | Operations Owner | **Yes** |
| DM-14 | User complaint / review finding | 민원·검수 발견의 접수와 처리 | 반복 실패의 미승격 (C-09 절차 미작동) | output-examples §13(수동 검수), red-team Promotion Gate | Product Owner + Professional Standards Owner | No (누적 시 Yes) |

---

## 5. Event and Evidence Model `[제안]`

공통 규칙:

- **질문 원문, 일지 본문, 가격·수량, 계정 정보(이메일 등)는 일반 이벤트 metadata에 포함하지 않는다** (C-03·C-04). 상관관계는 question_id / request_id로 유지한다.
- 사용자 데이터가 필요한 조사 건은 이벤트 시스템이 아니라 **별도 승인 + 접근 감사 절차**(§13, §16 단계 4)로 분리한다.
- **실제 사용자 데이터로 fixture를 만들지 않는다** — intake는 §18의 합성 변환을 거친다.
- 보존 상태는 전부 수치 미확정이며 §22 OQ-O-02에서 결정한다.

| Event ID | 이벤트 | 필수 metadata | 금지 metadata | 보존 상태 | consumer | severity |
|---|---|---|---|---|---|:---:|
| EV-01 | request processed | request_id, question_id, timestamp, 처리 결과 코드 | 질문 원문, user_id 직접 결합(§9.3 구조 준수) | RETENTION-TO-BE-DECIDED | DM-03, DM-12 | info |
| EV-02 | intent classified | question_id, detected_intent, 복수 intent 여부 | 질문 원문 | RETENTION-TO-BE-DECIDED | DM-01 | info |
| EV-03 | guard action selected | question_id, guard_action, redirected_lenses | 원문 | RETENTION-TO-BE-DECIDED | DM-01, DM-03 | info |
| EV-04 | validator passed/failed | question_id, validator_action, violation categories(키), severity | 위반 표현 원문, 응답 원문 | RETENTION-TO-BE-DECIDED | DM-02 | info/warn |
| EV-05 | rewrite attempted | question_id, rewrite_type, 시도 횟수 | 원문 | RETENTION-TO-BE-DECIDED | DM-02 | warn |
| EV-06 | rewrite failed | question_id, 실패 사유 코드 | 원문 | RETENTION-TO-BE-DECIDED | DM-02 | **high** |
| EV-07 | fallback returned | question_id, fallback 템플릿 키 | 원문 | RETENTION-TO-BE-DECIDED | DM-02 | warn |
| EV-08 | lens selected | question_id, lens keys | — | RETENTION-TO-BE-DECIDED | DM-04 | info |
| EV-09 | lens selection failed | question_id, 실패 사유 (미등록/불일치) | — | RETENTION-TO-BE-DECIDED | DM-04 | **high** |
| EV-10 | market data fetched | source key, 기준시각, 조회 시점, 대상 데이터 유형 | user_id 결합 | RETENTION-TO-BE-DECIDED | DM-05 | info |
| EV-11 | stale data detected | source key, 기준시각, 감지 시점, 표시 중단 여부 | — | RETENTION-TO-BE-DECIDED | DM-05 | warn/high |
| EV-12 | source/timestamp missing | 화면/컴포넌트 키, 누락 항목 | — | RETENTION-TO-BE-DECIDED | DM-05, DM-10 | **high** |
| EV-13 | prohibited copy detected | 표면 키(화면/배너/알림), 위반 분류 키 (§12) | — | RETENTION-TO-BE-DECIDED | DM-10, DM-11 | **high** |
| EV-14 | required disclaimer missing | 표면 키, 고지 유형 (copy-guide §5.1 ①~⑥) | — | RETENTION-TO-BE-DECIDED | DM-10 | **high** |
| EV-15 | administrator viewed restricted data | 관리자 ID, 대상 데이터 등급, 사유 코드, 승인 참조 | 조회된 데이터 내용 | RETENTION-TO-BE-DECIDED + LEGAL-REVIEW-REQUIRED | DM-08 | warn (사유 없으면 high) |
| EV-16 | deletion requested | 삭제 요청 ID, 대상 범위 코드, 접수 시점 | 삭제 대상 본문 | RETENTION-TO-BE-DECIDED + LEGAL-REVIEW-REQUIRED (user-data-policy OQ-U-07) | DM-09 | info |
| EV-17 | deletion completed | 삭제 요청 ID, 완료 시점, 범위별 완료 여부 | 본문 | 동상 | DM-09 | info |
| EV-18 | deletion failed | 삭제 요청 ID, 실패 지점(주 저장소/캐시/인덱스/파생) | 본문 | 동상 | DM-09 | **high** |
| EV-19 | retention limit exceeded | 데이터 유형 코드, 초과 감지 시점 | 해당 데이터 내용 | RETENTION-TO-BE-DECIDED | DM-09 | **high** |
| EV-20 | LLM transmission field mismatch | 처리 경로 키, 명세 버전, 초과/누락 필드명(필드 키만) | 필드 값 | RETENTION-TO-BE-DECIDED | DM-07 | **high** |
| EV-21 | raw/internal content exposure suspected | 노출 의심 지점, 대상 문서 exposure_class, content hash 참조 | 노출 의심 원문 자체의 이벤트 복제 | RETENTION-TO-BE-DECIDED | DM-12, DM-02 | **critical** |
| EV-22 | user complaint received | 민원 ID, 분류 코드, 접수 채널 | 민원 원문(별도 CS 시스템 소관 — 이벤트에는 미포함) | RETENTION-TO-BE-DECIDED | DM-14 | info/warn |
| EV-23 | review finding created | finding ID, 분류 코드, 발견 경로 (검수/민원/모니터링) | 사용자 원문 | RETENTION-TO-BE-DECIDED | DM-14, §18 | info |
| EV-24 | red-team candidate submitted | candidate ID, 대상 영역, 합성 변환 완료 플래그 | 실제 사용자 입력 원문 (**금지** — §18) | RETENTION-TO-BE-DECIDED | §18 intake | info |
| EV-25 | release gate failed | gate 키(§17), 실패 항목, 배포 후보 버전 | — | RETENTION-TO-BE-DECIDED | DM-13 | **high** |

---

## 6. Metrics Candidate Set `[제안]`

공통 label 규칙:

- **금지 label (전 metric)**: user_id 등 사용자 식별자, 종목명, 가격, 수량, 질문 원문/부분 문자열. **고카디널리티 label 전면 금지** (원문·자유 텍스트·무한 증가 키).
- 허용 label은 **유한 enum**(intent 12종, action 7종, violation category 키, lens key, source key, 화면 키 등)으로 한정한다.

| Metric | 의미 | label 허용 범위 | label 금지 범위 | alert | owner |
|---|---|---|---|---|---|
| guard_action_ratio | pass/block/rewrite/fallback 비율 | guard_action, intent | user_id, 원문 | 급격한 분포 변화 (THRESHOLD-TO-BE-DECIDED) | Professional Standards Owner |
| validator_violation_count | violation category별 검출 수 | category 키, severity | 위반 표현 원문 | CRITICAL category 검출 시 즉시 (THRESHOLD-TO-BE-DECIDED) | Professional Standards Owner |
| rewrite_outcome_ratio | rewrite 성공/실패/fallback 전환 비율 | rewrite_type, 결과 코드 | 원문 | 실패율 상승 (THRESHOLD-TO-BE-DECIDED) | Professional Standards Owner |
| routing_unknown_ratio | UNKNOWN/ASK_CLARIFYING 비율 | intent, action | 원문 | 급증 시 (분류기 회귀 신호) | Operations Owner |
| lens_miss_count | 미등록/불일치 lens 참조 수 | lens key, 실패 사유 | — | 1건 이상 즉시 (fixtures §10 위반 신호) | Operations Owner + Professional Standards Owner |
| market_data_stale_ratio | 스테일/미갱신 데이터 비율 | source key, 데이터 유형 | user_id | THRESHOLD-TO-BE-DECIDED | Operations Owner |
| grounding_label_omission_count | 출처/기준시각/지연 라벨 누락 수 | 화면 키, 누락 항목 | — | 1건 이상 즉시 (C-06 위반) | Operations Owner + Product Owner |
| llm_field_mismatch_count | 전송 명세 불일치 수 | 처리 경로 키, 초과/누락 구분 | 필드 값 | 1건 이상 즉시 | Operations Owner + Security Owner |
| sensitive_data_in_log_count | 로그 스캔 검출 수 (이메일/가격/수량/원문 패턴) | 로그 계층, 패턴 분류 키 | 검출된 값 자체 | 1건 이상 즉시 | Security Owner |
| deletion_outcome_count | 삭제 성공/실패/재처리 수 | 결과 코드, 실패 지점 | 삭제 대상 내용 | 실패 발생 시 (SLO는 §22 OQ-O-05) | Operations Owner |
| admin_restricted_access_count | Restricted 조회 수 | 역할, 사유 코드 유무 | 조회 내용, 대상 사용자 식별자 | 사유 없음/비업무 시간/대량 조회 패턴 | Security Owner |
| prohibited_copy_count | 금지 CTA/카피 검출 수 | 표면 키, 위반 분류 | — | 1건 이상 즉시 | Product Owner + Professional Standards Owner |
| disclaimer_missing_count | REQUIRED 고지 누락 수 | 표면 키, 고지 유형 | — | 1건 이상 즉시 | Product Owner |
| complaint_category_count | 민원 분류별 접수 수 | 민원 분류 코드 | 민원 원문, 사용자 식별자 | 특정 분류 급증 | Product Owner + CS |
| review_finding_count | 검수 발견 분류별 수 | finding 분류, 발견 경로 | 원문 | 동일 분류 반복 (§8 실패 7 신호) | Professional Standards Owner |
| regression_failure_count | fixture/validator regression 실패 수 | fixture 계열(SF/RF/PI/DG/AM), 실패 항목 | — | 1건 이상 즉시 — 배포 차단 (C-08) | Operations Owner |
| release_gate_failure_count | 배포 gate 실패 수 | gate 키 | — | 발생 시 배포 중단 | Operations Owner |

- **사용자별 투자성향·위험 점수류 지표는 설계하지 않는다** (C-05). intent 분포는 전체 집계로만 본다 (`finance-legal-risk-review-notes.md` IN-OMG-05, user-data-policy OUT-OMG-06).

---

## 7. Logging Policy `[제안]`

user-data-policy §9의 로그 계층을 운영 관점으로 전개한다.

| 로그 유형 | 허용 metadata | 금지 데이터 | 접근 주체 | 보존 상태 | 삭제/익명화 원칙 | 외부 전송 | 감사 필요 |
|---|---|---|---|---|---|:---:|:---:|
| application log | 요청 처리 코드, 성능 수치, 오류 코드/스택(내부 경로 마스킹 검토) | 질문/일지 원문, 가격·수량, 이메일 (마스킹 기본값) | Operations Owner | RETENTION-TO-BE-DECIDED | 만료 자동 삭제 | 불가 | 표준 |
| safety log | EV-02~03 metadata (intent, action, lenses) | 원문 일체 (C-03) | Professional Standards Owner, Operations Owner | RETENTION-TO-BE-DECIDED | user 결합 키 파기로 익명화 (user-data-policy §9.3) | 불가 | 표준 |
| validator log | EV-04~07 metadata (category 키, severity, rewrite_type) | 위반 표현 원문, 응답 원문 (C-03) | Professional Standards Owner | RETENTION-TO-BE-DECIDED | 동상 | 불가 | 표준 |
| audit event | EV-15~19 (조회·삭제·권한·export 이벤트) | 조회/삭제된 데이터 내용 | Security Owner (기록은 시스템, 열람은 Security Owner) | RETENTION-TO-BE-DECIDED + LEGAL-REVIEW-REQUIRED | 삭제 대상 아님 — 단 상한 필요 (OQ-U-07) | 불가 | **전건** |
| security log | 인증 시도, 권한 변경, 접근 이력 | 비밀번호·토큰 값 | Security Owner | RETENTION-TO-BE-DECIDED | 만료 자동 삭제 | 불가 | **전건** |
| market data evidence | EV-10~12 (source, 기준시각, 조회 시점, 표시 수치) | user_id 결합 | Operations Owner | RETENTION-TO-BE-DECIDED (개인정보 아님 — 운영 비용 기준) | 만료 자동 삭제 | 제공처 분쟁 시 검토 (**외부 법무 검토 필요**) | 표준 |
| review finding | finding 분류, 재현 조건(합성), 조치 이력 | 실제 사용자 원문 (§18 — 합성 변환 전 기록 금지) | Professional Standards Owner, Product Owner | RETENTION-TO-BE-DECIDED | 종결 후 보존 상한 적용 | 불가 | 표준 |
| incident record | 사고 분류, 타임라인, 조치, 영향 범위 코드 | 사용자 원문의 무제한 복제 (§16 증거 최소화) | Operations Owner, Security Owner, 필요 시 future External Legal Review | RETENTION-TO-BE-DECIDED + LEGAL-REVIEW-REQUIRED | 종결 후 보존 상한 적용 | 통지·신고 요건은 **외부 법무 검토 필요** | **전건** |

---

## 8. Professional Standards Owner and Policy Monitoring `[제안]`

| # | 실패 | detection | immediate action | escalation owner | regression | release blocking |
|---|---|---|---|---|:---:|:---:|
| SF-F1 | 금지 답변이 pass됨 (매수/매도/가격/수량/추천 표현이 사용자 도달) | 검수 샘플링(합성 기준), 민원 분류, 사후 응답 구조 감사 — validator 자체의 이중화 여부는 §22 OQ-O-11 | 해당 응답 경로 즉시 점검, 재발 조건 파악 전까지 관련 기능 노출 축소 검토 | Professional Standards Owner → Operations Owner | **Yes** (§18 경유) | **Yes** — SEV-1 이상 |
| SF-F2 | safe 질문의 과도 block (SAFE_*가 거절 경로로 다수 유입) | routing_unknown_ratio·block 비율 이상, 민원 분류 "과잉 거절" | 분류 변경 이력 확인, 최근 배포 rollback 검토 | Professional Standards Owner | Yes (오분류 케이스) | Yes (품질 저하 폭 기준 — OWNER-DECISION-REQUIRED) |
| SF-F3 | rewrite 후 위험 표현 잔존 | rewrite 후 재검사 실패 이벤트(EV-06), 검수 발견 | fallback 강제 전환 상태 확인 (validator 재작성 반복 실패 시 표준 거절 폴백 — policyguard §9.2) | Professional Standards Owner | **Yes** | **Yes** |
| SF-F4 | fallback이 서비스 범위 밖 응답 (거절 최소형 이탈) | fallback 응답의 구조 검사 (templates §14 폴백 = §8 최소형) | fallback 템플릿 키 검증, 잘못된 템플릿 매핑 수정 | Professional Standards Owner + Operations Owner | Yes | **Yes** |
| SF-F5 | internal intent/action 이름의 사용자 노출 | 응답/화면 문자열 스캔 (enum 이름 패턴 — copy-guide B-09) | 노출 지점 즉시 수정, exposure 경로 점검 (C-02 위반 여부 판단) | Professional Standards Owner + Security Owner | Yes | **Yes** |
| SF-F6 | 사용자 질문이 위험 점수로 누적됨 (프로파일링 지표 생성) | 지표/대시보드 정의 리뷰 (§19 금지 dimension 검사), 코드 리뷰 | 해당 지표 즉시 폐기, 생성 경위 조사 (C-05 위반) | Security Owner + Professional Standards Owner | No (데이터 관행 문제 — §10) | **Yes** |
| SF-F7 | 동일 failure 반복에도 fixture candidate 미승격 | review_finding_count의 동일 분류 반복 감지, §18 intake 정체 지표 | intake 절차 점검, 미제출 사유 확인 | Product Owner + Professional Standards Owner | 자체가 intake 문제 | No (누적 시 Yes — OWNER-DECISION-REQUIRED) |

---

## 9. Market Data Monitoring (Phase 1.5+) `[제안]`

Phase 1(미연결) 상태에서는 "데이터 미연결" 상태 문구의 정상 표시 여부(copy-guide §10)만 점검 대상이다.

| 상태 | 이상 판정 기준 | 조치 | 상태 값 |
|---|---|---|---|
| 데이터 미연결 (Phase 1 기본) | 미연결인데 수치형 placeholder가 표시됨 | 표시 제거 — copy-guide §10 위반 | — |
| 조회 실패 | 제공처 응답 실패 지속 | 표시 중단 + "불러올 수 없음" 상태 문구 전환 | 지속 기준: THRESHOLD-TO-BE-DECIDED |
| 기준시각 누락 | 기준시각 없는 수치의 렌더링 감지 (EV-12) | **즉시 비표시** — C-06은 표시 자체를 금지 | 즉시 (threshold 없음) |
| 출처 누락 | 출처 라벨 없는 수치 렌더링 (EV-12) | 동상 | 즉시 |
| 지연 여부 누락 | 지연 시세에 지연 라벨 부재 | 라벨 복구 전까지 비표시 | 즉시 |
| 스테일 데이터 | 기준시각과 현재 시점의 간격 초과 (EV-11) | 기준시각 강조 또는 표시 중단 (copy-guide §10) | 간격 기준: THRESHOLD-TO-BE-DECIDED + OWNER-DECISION-REQUIRED |
| 이상치 | 전일 대비 비정상 변동 등 검증 실패 | 해당 수치 표시 보류 + 제공처 확인 (`finance-legal-risk-review-notes.md` §8 — canonical 공백의 운영 보완) | 판정 기준: THRESHOLD-TO-BE-DECIDED |
| 제공처 응답 오류 | 오류 코드/형식 불일치 | 표시 중단 + Operations Owner 에스컬레이션. 책임 분담은 **외부 법무 검토 필요** (LR-05) | — |
| 오래된 값의 최신 표시 | 갱신 실패 상태에서 이전 값이 시각 갱신 없이 유지 | 스테일 처리와 동일 + 렌더링 캐시 점검 | — |
| 사용자 입력값의 외부 데이터화 | "입력값" 라벨 없는 입력가 표시 (grounding §8 위반) | 라벨 복구 전까지 해당 비교 UI 중단 | 즉시 |

---

## 10. User Data Protection Monitoring `[제안]`

user-data-policy §14(금지 관행)·§16(R-IBH)의 감시 절이다.

| 위반 후보 | 탐지 방식 candidate | 즉시 조치 | 조사 범위 | owner | blocking | 법무 검토 |
|---|---|---|---|---|:---:|:---:|
| 로그에 이메일 포함 | 로그 패턴 스캔 (R-IBH-03) | 해당 로그 정화, 마스킹 결함 수정 | 혼입 기간·범위, 접근 이력 | Security Owner | Yes | 유출 해당성 — **필요** |
| 로그에 가격·수량 포함 | 필드 직렬화 스캔 (C-04) | 동상 | 동상 | Security Owner + Operations Owner | Yes | 필요 (규모에 따라) |
| 질문/일지 원문 저장 (미승인) | 저장소 스키마 감사, 로그 스캔 | 저장 중단, 기존 데이터 처리 결정 | 저장 경위, 접근 이력 | Security Owner + Product Owner | **Yes** | **필요** |
| 관심종목 전체의 불필요 전송 | LLM 전송 diff (EV-20 — §8.1 규칙 4 위반) | 전송 경로 수정 | 전송 기간·건수 | Operations Owner | Yes | 권장 |
| LLM에 user_id 전송 | 전송 diff (§8.1 규칙 2 위반) | 동상 | 동상 | Operations Owner + Security Owner | Yes | **필요** (제공사 측 잔존 여부) |
| 미승인 이미지/OCR 수집 | 업로드 엔드포인트/스토리지 감사 (gate 미통과 — user-data-policy §15) | 수집 경로 폐쇄, 수집분 파기 결정 | 수집 경위·규모 | Security Owner + Product Owner | **Yes** | **필요** |
| 계좌 정보 수집 | 저장 데이터 패턴 감사 (P-03·P-04 위반 — 추론 생성 포함) | 즉시 파기 결정 + SEV-1 이상 분류 (§15) | 수집 경로 전체 | Security Owner + future External Legal Review | **Yes** | **필요** |
| 삭제 실패 | EV-18, 요청 대비 완료 대사 (R-IBH-05) | 재처리 큐 + 실패 지점 수정 | 미삭제 범위 | Operations Owner | Yes | 필요 (지연 규모에 따라) |
| 백업·파생 데이터 잔존 | 삭제 검증 샘플링 (캐시/인덱스/파생 포함 — user-data-policy §10.2) | 잔존분 파기, 연동 삭제 로직 수정 | 잔존 유형·기간 | Operations Owner | Yes | 필요 |
| 관리자 원문 접근 (비정상) | EV-15 패턴 분석 (§13) | 계정 권한 동결 검토, 사유 소명 | 조회 내역·목적 | Security Owner | Yes | 필요 (내부 유출 해당성) |
| 보존 상한 초과 | EV-19, 상한 확정 후 정기 스캔 (R-IBH-06) | 초과분 파기 절차 | 초과 유형·기간 | Operations Owner | No (반복 시 Yes) | 상한 자체 — **필요** (OQ-U-01) |
| 삭제된 데이터의 재사용 | 파생 산출물(통계·검증 셋) 소스 대사 (user-data-policy §14 관행 7) | 산출물 재생성, 파이프라인 수정 | 재사용 범위 | Operations Owner + Product Owner | Yes | 필요 |
| 사용자 프로파일링 지표 생성 | §19 금지 dimension 리뷰, 지표 정의 코드 리뷰 (SF-F6와 동일) | 지표 폐기 (C-05) | 생성 경위·소비처 | Security Owner + Professional Standards Owner | **Yes** | 필요 |

---

## 11. LLM Transmission Monitoring `[제안]`

특정 LLM 제공업체의 정책·약관은 추정하지 않는다. 아래는 자사 측 통제만 다룬다.

```text
1. 허용 필드 명세 — user-data-policy §8.2의 "처리 경로별 전송 필드
   명세"를 단일 참조원으로 관리한다. 명세 변경은 리뷰 + EV 기록
   (감사 이벤트 — user-data-policy §9.4) 대상이다.
2. 실제 전송 필드 비교 — 전송 직전 payload의 필드 키 집합을 명세와
   대사한다 (EV-20). 초과 필드는 즉시 이상, 누락 필드는 기능 오류로
   분류한다.
3. 불필요 식별자 — user_id/이메일 패턴의 payload 혼입 스캔 (§10 표).
4. 전체 Journal 원문 전송 — 단일 요청의 기록 필드 수/크기 상한 검사
   (§8.1 규칙 5). 상한 수치: THRESHOLD-TO-BE-DECIDED.
5. 관심종목 전체 자동 첨부 — 질문에 등장하지 않은 종목 필드의 혼입
   검사 (§8.1 규칙 4).
6. 가격·수량 불필요 전송 — Review 외 경로의 가격/수량 필드 검출.
7. prompt/response 원문 로그 저장 — 기본 금지 (user-data-policy §8.3).
   로그 스캔 대상에 프롬프트 패턴 포함.
8. 외부 LLM 장애 — 응답 실패/지연 시 사용자에게는 §12 오류 상태 문구만
   노출 (내부 정보 비노출 — copy-guide §12).
```

**Provider 재검토 gate** `[제안]`:

| 트리거 | 필요 절차 | blocking |
|---|---|:---:|
| LLM provider 변경 | 전송 필드 명세 재검증 + validator regression 전체 재수행 (모델 변경은 fixture 기준선 재검증 대상 — fixtures §1) + 데이터 처리 조건 검토 (**외부 법무 검토 필요** — user-data-policy §8.4) | **Yes** |
| 동일 provider의 데이터 처리 조건 변경 (약관/계약 개정) | 데이터 처리 조건 재검토 (**외부 법무 검토 필요**) + 고지 문구 영향 검토 (copy-guide C-DATA-03) | **Yes** |
| 모델 버전 변경 (동일 provider) | fixture/validator regression 재수행 (DM-13) | **Yes** |

---

## 12. Copy and Disclaimer Monitoring `[제안]`

소유권 구분 `[canonical + 05 boundary]`:

- **AI 응답 내부**: Prompt Template + Output Validator 소관 — DM-02에서 감시.
- **화면·CTA·알림·마케팅**: Copy Guide + Product Owner workflow 소관 — 본 절에서 감시.

| Copy failure | 탐지 위치 | 자동/수동 | reviewer | 즉시 조치 | regression | blocking |
|---|---|:---:|---|---|:---:|:---:|
| 금지 CTA 노출 ("매수하기" 계열 — copy-guide §7) | 배포 산출물 문자열 스캔 + 화면 QA | 자동+수동 | Product Owner + Professional Standards Owner | 해당 UI 즉시 수정/비활성 | Yes (카피 회귀 사례) | **Yes** |
| 투자 판단 승인 표현 ("좋은 판단" — copy-guide §8) | 문자열 스캔 + 검수 | 자동+수동 | Professional Standards Owner | 문구 제거 | Yes | **Yes** |
| 진단·예측·추천·신호·점수 표현 (copy-guide §9) | 문자열 스캔 | 자동 | Product Owner | 문구 교체 (§9 대체 표현) | Yes | Yes |
| REQUIRED 고지 누락 (copy-guide §5.1 ③④⑤) | 화면별 고지 존재 검증 (EV-14) | 자동 | Product Owner | 고지 복구 전 해당 화면 노출 중단 검토 | Yes | **Yes** |
| 기준시각·출처 누락 | 렌더링 검증 (EV-12 — §9와 통합) | 자동 | Operations Owner + Product Owner | 수치 비표시 전환 | Yes | **Yes** (Phase 1.5+) |
| 사용자 입력값 라벨 누락 | 렌더링 검증 | 자동 | Product Owner | 비교 UI 중단 (§9 표) | Yes | Yes |
| 내부 intent/action 이름 노출 | 문자열 스캔 (enum 패턴 — SF-F5와 통합) | 자동 | Professional Standards Owner + Security Owner | 즉시 수정 + 노출 경로 조사 | Yes | **Yes** |
| 미승인 마케팅 카피 (copy-guide §15 우회) | 승인 이력 대사 (EV-13 + OUT-OMG-C-06) | 수동 | Product Owner + future External Legal Review | 집행 중단 | Yes | Yes |
| 유료 플랜의 판단 강화 표현 | 카피 리뷰 + 스캔 (copy-guide §6 유료 행) | 자동+수동 | Product Owner + future External Legal Review | 문구 제거 — **외부 법무 검토 필요** (LR-12 연동) | Yes | Yes (유료화 시) |
| 모바일/반응형 고지 누락 | 뷰포트별 렌더링 검증 (copy-guide §5.2 — 축약 생략 금지) | 자동 | Product Owner + Product Owner | 레이아웃 수정 전 화면 노출 중단 검토 | Yes | Yes |
| 알림의 긴급성·시점 암시 | 알림 템플릿 리뷰 + 스캔 (copy-guide §6 알림 행) | 자동+수동 | Product Owner + Professional Standards Owner | 알림 발송 중단 | Yes | Yes (알림 도입 시) |

---

## 13. Access and Audit Monitoring `[제안]`

실제 RBAC 구조는 확정하지 않는다 (구현 결정 — §21). 아래는 **운영 검토 기준**이다. 역할 정의는 user-data-policy §11을 따른다.

| 검토 대상 | 이상 판정 기준 candidate | 검토 주기 | reviewer |
|---|---|---|---|
| Restricted 데이터 조회 (운영 관리자/고객지원/개발자) | 사유 코드 없는 조회 1건 이상, 승인 참조 없는 조회 | 정기 (주기: OWNER-DECISION-REQUIRED) + 이상 시 즉시 | Security Owner |
| 대량 조회 | 단시간 다건 조회 패턴 (기준: THRESHOLD-TO-BE-DECIDED) | 이상 시 즉시 | Security Owner |
| 비업무 시간 조회 | 업무 시간 외 Restricted 접근 | 이상 시 즉시 | Security Owner |
| 사유 없는 조회 | EV-15 사유 코드 누락 | 발생 시 소명 요구 | Security Owner + 해당 조직장 |
| export 실행 | export 이벤트 전건 검토 (user-data-policy §12 — 생성·다운로드 기록) | 전건 | Security Owner |
| 권한 변경 | 권한 상승·부여 이벤트 전건 검토 | 전건 | Security Owner |
| 조회 후 다운로드 | 조회→export 연쇄 패턴 | 이상 시 즉시 | Security Owner |
| 마스킹 우회 | 마스킹 뷰 외 원본 접근 경로 사용 (user-data-policy §11.1 — 기본 마스킹 뷰) | 발생 시 즉시 — 우회 경로 존재 자체가 결함 | Security Owner + Operations Owner |
| 데이터 분석 담당의 원문 접근 | 원문 접근 전면 불가 원칙 (user-data-policy §11.1) 위반 = 발생 즉시 이상 | 발생 시 즉시 | Security Owner |
| 외부 검토자 접근 | 사전 정의된 범위(합성/집계 데이터) 외 접근 | 전건 | Security Owner + future External Legal Review |

---

## 14. Retention and Deletion Monitoring `[제안]`

기간 수치는 확정하지 않는다. 상태 값만 사용한다.

| 감시 대상 | 관찰 방법 | 이상 판정 | 상태 |
|---|---|---|---|
| 삭제 요청 접수 | EV-16 | 접수 이벤트 누락 (요청 UI 대비 이벤트 대사) | — |
| 주 저장소 삭제 | EV-17 범위별 완료 플래그 | 완료 지연 (기한: OWNER-DECISION-REQUIRED — OQ-O-05) | USER-CONTROLLED |
| 캐시 삭제 | 삭제 검증 샘플링 | 캐시 잔존 검출 | — |
| 검색 인덱스 삭제 | 동상 | 인덱스 잔존 검출 | — |
| 파생 데이터 삭제 (Review 캐시 등) | 동상 (user-data-policy §10.2 연동 파기) | 파생 잔존 검출 | — |
| 백업 자연 소멸 | 백업 수명 주기 검증 | 상한 초과 백업 존재 (상한: RETENTION-TO-BE-DECIDED) | RETENTION-TO-BE-DECIDED |
| 삭제 완료 이벤트 | EV-17 | 요청 대비 완료 누락 (R-IBH-05) | — |
| 삭제 실패 재처리 | EV-18 → 재처리 큐 | 재처리 반복 실패 | — |
| 보존 상한 초과 | EV-19 정기 스캔 | 상한 확정 전에는 스캔 불능 — 상한 확정이 선행 조건 (OQ-U-01, **외부 법무 검토 필요**) | RETENTION-TO-BE-DECIDED + LEGAL-REVIEW-REQUIRED |
| 법적 보존 필요 미확정 데이터 | 데이터 유형별 상태 표 관리 | LEGAL-REVIEW-REQUIRED 상태 데이터의 기본값이 최소 보존인지 검증 (user-data-policy §10.2 원칙 4) | LEGAL-REVIEW-REQUIRED |

---

## 15. Severity and Escalation Model `[제안]`

아래 등급은 내부 운영 등급이다. 현재 prototype stage에서는 logical owner
roles may be held by the Project Owner. 최종 체계는 §22 OQ-O-09에서 별도
확정한다.

| 등급 | 의미 | 예시 | 즉시 조치 | escalation owner | release 영향 | 외부 통지 검토 |
|---|---|---|---|---|---|---|
| SEV-0 | 서비스 정체성/법적 경계의 전면 침해 또는 대규모 데이터 사고 | raw-restricted 문서의 사용자 노출 (C-02 위반, EV-21), 사용자 데이터 대량 노출, 계좌 정보의 승인 없는 수집, 금지된 자동매매성 기능 노출, 안전 정책 전면 우회 (guard/validator 무력화) | 해당 기능/배포 즉시 중단·rollback, 노출 경로 차단, future External Legal Review + Security Owner 즉시 소집 | Product Owner + Security Owner + future External Legal Review | 전면 배포 중단 | **외부 법무 검토 필요** — 통지·신고 요건과 기한은 본 문서에서 확정하지 않음 (R-IBH-08) |
| SEV-1 | 금지선 위반이 사용자에게 도달했으나 범위 한정 | 금지 답변 pass (SF-F1), 금지 CTA 노출, 삭제 요청 대규모 미처리, LLM 명세 외 민감 필드 전송 | 해당 경로 차단/수정, 원인 배포 rollback 검토 | Professional Standards Owner + Operations Owner | 관련 영역 배포 중단 | 필요 시 **외부 법무 검토 필요** |
| SEV-2 | 방어 계층 내부에서 잡혔거나 사용자 영향 경미 | rewrite 실패 급증(fallback 정상 동작), 고지 누락 단건, 스테일 데이터 표시 | 수정 + regression 등록 | 영역 owner (§20) | 수정 전 해당 변경 배포 보류 | 불필요 |
| SEV-3 | 품질·표기 수준 문제 | 상태 문구 오탈자, 지표 결측, 비차단 검수 지적 | 정규 수정 사이클 처리 | 영역 owner | 없음 | 불필요 |

- 등급 판정이 애매하면 **높은 등급으로 보수 판정**한다.
- Any suspected Restricted or Confidential data exposure, deletion failure, or
  LLM over-transmission is treated as SEV-1 minimum and must immediately route
  to the Privacy Owner, Security Owner, and Operations Owner. Legal
  notification and reporting deadlines remain unresolved; this document does
  not invent statutory notification deadlines.
- SEV-0/1은 §16 triage 전 과정을 필수 수행하고, 사후 리뷰(post-incident review)를 생략할 수 없다.

---

## 16. Incident Triage Workflow `[제안]`

```text
 1. detect     — §5 이벤트/§6 지표/민원/검수 경로에서 탐지
 2. contain    — 노출 경로 차단, 필요 시 기능 비활성/rollback (C-08:
                 gate 실패 상태의 재배포는 불가)
 3. classify   — §15 severity 판정 (애매하면 상향)
 4. preserve minimum evidence
               — 메타데이터(EV 이벤트, content hash, 버전) 중심으로 보존.
                 ⚠️ 증거 보존을 이유로 사용자 원문을 무제한 저장하지
                 않는다 (`finance-legal-risk-review-notes.md` §9.1 — 재현 가능성으로 대체).
                 조사에 원문이 불가피하면: 최소 범위 특정 + 승인 +
                 EV-15 계열 접근 감사 로그를 전제로만 접근하고,
                 조사 종결 시 파기한다. 민원 건 한시 원문 보존은
                 외부 법무 검토 완료 전 미시행 (user-data-policy §10.2)
 5. assign owner
               — §20 매핑 기준으로 primary owner 지정
 6. assess user impact
               — 영향 사용자 규모·데이터 등급·노출 기간 산정
 7. determine release/rollback action
               — DM-13 기준. 원인 미상 시 보수적으로 rollback
 8. legal/security escalation
               — SEV-0/1 필수. 통지·신고 의무의 요건과 기한은
                 외부 법무 검토 필요 (임의 기한을 설정하지 않음)
 9. remediate  — 수정 적용 (수정 배포도 §17 gate 전체 통과 필요)
10. verify     — 재발 조건 재현 시도 + 수정 확인
11. create regression
               — §18 intake로 제출 (합성 변환 필수)
12. post-incident review
               — 타임라인·원인·방어 공백 기록 (incident record — §7),
                 canonical 공백 발견 시 owner에게 개정 제안
                 (본 문서가 직접 개정하지 않음)
```

---

## 17. Release and Rollback Checks `[제안]`

**공통 원칙 `[canonical]`**: 기계 gate 실패의 사람 승인 override 절차는 존재하지 않는다 (C-08). 실패 → 수정 → 재검사 → 통과만 허용된다.

| Check | source document | automated/manual | pass criteria candidate | failure action | blocking |
|---|---|:---:|---|---|:---:|
| canonical 문서 정합성 (참조 문서명·버전·intent/action enum 일치) | metadata standard, alignment 보고서 기준선 | automated | 참조 무결성 검사 전건 통과 | 배포 중단, 정합성 수정 | **Yes** |
| PolicyGuard fixture regression | fixtures §4~§10 | automated | 전 fixture 기대 결과 일치 (§10 실패 조건 0건) | 배포 중단 (fixtures §1) | **Yes** |
| Validator regression | validator + red-team cases | automated | 금지 표현 차단·rewrite 계약 전건 통과 | 배포 중단 | **Yes** |
| Lens registration validation | fixtures §10 (미정의 lens 금지) | automated | 응답 경로가 참조하는 lens key 전건 등록 확인 | 배포 중단 | **Yes** |
| metadata validation | metadata standard | automated | 필수 필드·enum·파일명 규칙 통과 | 배포 중단 | **Yes** |
| exposure boundary validation | exposure policy §16 Validation Gate | automated | raw-restricted/internal-controlled의 package 경계 검사 통과 | 배포 중단 | **Yes** |
| required disclaimer presence | copy-guide §5.1 | automated | REQUIRED 고지의 화면별 존재 (반응형 포함) | 배포 중단 | **Yes** |
| prohibited CTA scan | copy-guide §7, 서비스 정책 §11 | automated | 금지 CTA 계열 0건 | 배포 중단 | **Yes** |
| market data source/timestamp validation (Phase 1.5+) | grounding §4 | automated | 수치 렌더링에 출처/기준시각/지연 라벨 결합 확인 | 배포 중단 (Phase 1.5+) | **Yes** |
| user-data log scan | user-data-policy §9, C-04 | automated | 신규 로그 코드의 민감 필드 패턴 0건 | 배포 중단 | **Yes** |
| LLM transmission field diff | user-data-policy §8.2 | automated | 전송 필드 = 명세 (초과 0건) | 배포 중단 | **Yes** |
| deletion workflow test | user-data-policy §10 | automated + manual | 테스트 계정 삭제의 범위별 완료 검증 (합성 데이터) | 배포 중단 | Yes |
| administrator access audit test | user-data-policy §11.2 | manual (주기 검증) | Restricted 접근의 감사 로그 생성 확인 | 수정 전 관련 도구 배포 보류 | Yes |

---

## 18. Review Finding → Fixture/Red-team Intake `[제안]`

**전제 `[canonical]`**: 승격은 Promotion Gate(canonical owner 승인)를 거친다 (C-09). 운영 발견이 fixture/red-team을 직접 수정하지 않는다. 본 절은 **제출까지의 절차**만 정의하며, 실제 fixture ID·expected behavior를 생성하지 않는다.

```text
1. 운영 실패 발견        — §8~§14 감시 경로 또는 민원(EV-22)/검수(EV-23)
2. 재현 가능성 확인      — 발생 조건의 서술적 재현 (버전, intent 경로,
                          응답 구조 결함 유형)
3. 개인정보 제거         — 사용자 식별 요소·실제 입력 원문 제거.
                          ⚠️ 실제 사용자 데이터로 fixture를 만들지 않는다
4. 합성 입력으로 변환    — 동일 실패를 유발하는 합성 질문/조건 작성
                          (red-team 문서의 자리표시자 원칙 — OO원/N%/N주 —
                          과 동일 계열)
5. 기존 fixture/red-team 중복 확인
                        — 기존 케이스가 이미 커버하면 회귀 원인 조사로 전환
                          (커버되는데 실패했다면 구현/배포 문제)
6. canonical owner 확인  — 대상 문서(fixtures/red-team)의 owner 식별
7. candidate 제출        — EV-24 기록과 함께 제출 (합성 변환 완료 플래그 필수)
8. Promotion Gate 검토   — owner 심사 (본 문서 권한 밖)
9. 승인 후 등록          — canonical 개정으로 반영 (owner 소관)
10. regression 수행      — 등록된 케이스 포함 전체 regression 재수행 (§17)
```

---

## 19. Dashboard and Report Candidates `[제안]`

공통 금지 dimension: 사용자 식별자, 종목명, 가격, 수량, 질문 원문, 사용자 단위 장기 추적 축 (C-05). 도구는 확정하지 않는다.

| Dashboard | 목적 | 핵심 지표 | 금지 dimension | reviewer | review cadence |
|---|---|---|---|---|---|
| Professional Standards Owner health | guard 분포·오분류 신호 | guard_action_ratio, routing_unknown_ratio, review_finding_count(safety 분류) | 사용자 단위 intent 이력 | Professional Standards Owner | 주기: OWNER-DECISION-REQUIRED (제안: 주간) |
| Validator health | 차단·재작성 건전성 | validator_violation_count, rewrite_outcome_ratio, fallback 비율 | 위반 표현 원문 | Professional Standards Owner | 주간 제안 |
| Routing/Lens health | 계약 준수·lens 정합 | lens_miss_count, 계약 외 action 검출 | — | Operations Owner + Professional Standards Owner | 주간 제안 |
| Market data grounding | 표시 의무·데이터 품질 (Phase 1.5+) | grounding_label_omission_count, market_data_stale_ratio | user_id 결합 축 | Operations Owner | 일간 제안 (Phase 1.5+) |
| User data protection | 로그 위생·전송 통제 | sensitive_data_in_log_count, llm_field_mismatch_count | 검출 값 원문 | Security Owner | 주간 제안 |
| Deletion and retention | 삭제 이행·보존 상태 | deletion_outcome_count, retention 초과 건수 | 삭제 대상 내용 | Operations Owner | 주간 제안 |
| Copy compliance | 카피·고지 상태 | prohibited_copy_count, disclaimer_missing_count | — | Product Owner + Professional Standards Owner | 배포 시 + 주간 |
| Release quality | gate·regression 상태 | regression_failure_count, release_gate_failure_count | — | Operations Owner | 배포마다 |
| Incident summary | 사고 추세·사후 조치 이행 | severity별 건수, 재발 여부, regression 전환율 | 사용자 원문·식별자 | Operations Owner + future External Legal Review | 월간 제안 |

---

## 20. Ownership Model `[제안]`

Allowed logical roles: Product Owner / Privacy Owner / Security Owner /
Operations Owner / Professional Standards Owner. For the current prototype
stage, these logical roles may be held by the Project Owner. Future External
Legal Review is unassigned and remains a launch gate, not a staffed internal
role.

| 운영 영역 | primary owner | reviewer | escalation owner | canonical owner (규칙 소유) | 미확정 사항 |
|---|---|---|---|---|---|
| Professional Standards Owner Guard (DM-01) | Professional Standards Owner | Operations Owner | Operations Owner | policyguard-rules owner | 분류기 이중화 여부 (OQ-O-11) |
| Output Validation (DM-02) | Professional Standards Owner | Operations Owner | Operations Owner | validator owner | 검수 샘플 방식 (합성 기준 — user-data-policy §9.5) |
| Routing/Lens (DM-03~04) | Operations Owner | Professional Standards Owner | Operations Owner | routing-contract / lens-catalog owner | — |
| Market data (DM-05) | Operations Owner | Product Owner | Operations Owner | grounding owner | 제공처 선정 (`finance-legal-risk-review-notes.md` OQ-07), threshold |
| User data protection (DM-06) | Privacy Owner | Security Owner | Privacy Owner + Security Owner | user-data-policy | 보존기간 (OQ-U-01) |
| LLM transmission (DM-07) | Privacy Owner | Security Owner | Privacy Owner + Security Owner | user-data-policy §8 | provider 계약 검토 (**future External Legal Review 필요**) |
| Access control (DM-08) | Security Owner | Operations Owner | Privacy Owner + Security Owner | user-data-policy §11 | 승인 방식 (OQ-O-04) |
| Retention/Deletion (DM-09) | Privacy Owner | Operations Owner | Privacy Owner + Operations Owner | user-data-policy §10 | 삭제 SLO (OQ-O-05) |
| Copy/Disclaimer (DM-10) | Product Owner | Professional Standards Owner | Product Owner | copy-guide + 서비스 정책 §11~§12 | lint 자동화 범위 (OQ-O-07) |
| Public copy approval (DM-11) | Product Owner | future External Legal Review | Product Owner | 서비스 정책 §12 | — |
| Incident (DM-12) | Operations Owner | Security Owner + future External Legal Review | Product Owner | 본 문서 §15~§16 | severity 최종 체계 (OQ-O-09) |
| Release (DM-13) | Operations Owner | Professional Standards Owner | Product Owner | fixtures §1·§10, exposure policy §16 | gate owner 지정 (OQ-O-08) |
| Complaint/Finding (DM-14) | Product Owner | Professional Standards Owner | Operations Owner | output-examples §13(검수), red-team Promotion Gate | intake 정체 기준 (SF-F7) |

---

## 21. Downstream Implementation Requirements `[제안]`

본 문서는 아래를 **확정하지 않으며**, 후속 구현·운영 작업의 요구사항으로만 넘긴다.

```text
IMPL-01  backend instrumentation — §5 이벤트의 발행 지점 구현
         (routing-contract 실행 순서의 각 단계에 계측 삽입)
IMPL-02  structured logging — §7 로그 계층의 스키마 설계와 마스킹
         기본값 구현 (C-04)
IMPL-03  metric schema — §6 metric의 이름·label 확정 (금지 label
         규칙을 스키마 수준에서 강제)
IMPL-04  audit storage — EV-15~19 감사 이벤트의 변조 방지 저장 설계
IMPL-05  sensitive data scanner — 로그/payload의 민감 패턴 스캔 구현
         (R-IBH-03, §10·§11)
IMPL-06  copy lint — copy-guide §3·§7·§9·§13.1 목록 기반 문자열 검사
         도구 (자동화 범위: OQ-O-07)
IMPL-07  release checklist automation — §17 gate의 CI 통합
         (override 경로 없는 구조로 — C-08)
IMPL-08  deletion verification — §14 범위별 삭제 검증 자동화
IMPL-09  access review — §13 검토 절차의 도구화 (사유 코드·승인 참조)
IMPL-10  incident runbook — §16 흐름의 실행 문서화
         (연락 체계·시간 기준 포함 — 법적 기한은 외부 법무 검토 필요)
IMPL-11  dashboard implementation — §19 후보의 도구 선정과 구축
         (도구 선정은 본 문서 범위 밖)
```

---

## 22. Open Decisions

| ID | 이슈 | 현재 근거 | 선택지 | 권장 방향 `[제안]` | owner | blocking | 외부 법무 검토 |
|---|---|---|---|---|---|:---:|:---:|
| OQ-O-01 | metric/alert 임계치 확정 | §6 — 전부 THRESHOLD-TO-BE-DECIDED | (a) 초기 무-threshold 관찰 후 설정 (b) 보수 초기값 선설정 | (a) — 근거 없는 수치 확정 금지. 단 "1건 즉시" 항목(금지 표현 pass 등)은 threshold 불요 | Operations Owner + 영역 owner | Yes (운영 개시 전) | 불필요 |
| OQ-O-02 | 이벤트/로그 보존 기간 | §5·§7 — RETENTION-TO-BE-DECIDED | (a) future External Legal Review 후 일괄 확정 (b) 계층별 차등 선확정 | (a) — user-data-policy OQ-U-01과 통합 결정 | Privacy Owner + Operations Owner + future External Legal Review | **Yes** | **필요** |
| OQ-O-03 | 질문·응답 원문 저장 여부 | user-data-policy OQ-U-02 (SESSION-ONLY 기본 제안) | 동 문서 선택지 | (a) 미저장 유지 — 본 문서 감시 설계는 원문 부재 전제로 성립함을 확인 | Product Owner + Privacy Owner + future External Legal Review | Yes | 필요 ((b)(c) 시) |
| OQ-O-04 | 관리자 접근 승인 방식 | §13, user-data-policy §11.2 | (a) 사전 승인 (b) 사후 전건 검토 (c) 등급별 혼합 | (c) — Restricted는 사전 승인, Internal은 사후 검토 | Security Owner | Yes (도구 구현 전) | 권장 |
| OQ-O-05 | 삭제 완료 SLO | §14 — 기한 미확정 | (a) future External Legal Review 후 확정 (b) 내부 목표 선설정 | (a) 기한 + (b) 내부 목표 병행 (내부 목표는 대외 약속 아님) | Privacy Owner + Operations Owner + future External Legal Review | Yes | **필요** |
| OQ-O-06 | 시장 데이터 stale 기준 | §9 — THRESHOLD-TO-BE-DECIDED | (a) 데이터 유형별 차등 (b) 단일 기준 | (a) — 시세/수급/일정의 갱신 주기가 상이 | Operations Owner | Yes (Phase 1.5 전) | 불필요 |
| OQ-O-07 | Copy lint 자동화 범위 | §12, IMPL-06 | (a) 금지 목록 문자열 매칭만 (b) 완곡 표현 휴리스틱 포함 | (a)로 시작 — 완곡 표현은 §15 workflow의 수동 보수 판정 유지 (copy-guide §4) | Product Owner + Operations Owner | No | 불필요 |
| OQ-O-08 | 배포 gate owner 지정 | §17, §20 — Operations Owner 미배정 | (a) 단일 approver (b) 영역 owner 합의제 | (a) + blocking 실패의 override 불가 원칙 유지 (C-08) | Product Owner | **Yes** | 불필요 |
| OQ-O-09 | incident severity 최종 체계 | §15 — 후보만 존재 | (a) 본 후보 채택 (b) 조직 표준 통합 | 조직 표준 존재 시 (b), 없으면 (a) | Operations Owner | Yes (운영 개시 전) | 불필요 |
| OQ-O-10 | 외부 LLM provider 변경 검토 절차 | §11 gate — 검토 항목만 정의 | (a) gate 3종(전송/regression/법무) 고정 (b) 사안별 판단 | (a) — 사안별 판단은 우회 여지 | Operations Owner + future External Legal Review | Yes (변경 시) | **필요** |
| OQ-O-11 | 안전 검증의 이중화 (validator 외 표본 감사 체계) | SF-F1 — 탐지가 validator 자체에 의존 | (a) 합성 케이스 정기 주입 검사 (b) 사후 표본 구조 감사 (c) 병행 | (c) — 단 표본 감사도 원문 최소화 원칙(§16 단계 4) 준수 | Professional Standards Owner | No | 불필요 |
| OQ-O-12 | 법적 사고 통지·신고 절차 | §15 SEV-0, §16 단계 8, R-IBH-08 | (a) future External Legal Review 후 절차 수립 (b) 내부 초안 선작성 | (a) — 요건·기한·대상의 임의 작성 금지 | Privacy Owner + Security Owner + future External Legal Review | **Yes** (운영 개시 전) | **필요** |

---

## 23. 본 문서가 하지 않는 것

| 책임 | 담당 |
|---|---|
| intent/action/lens/redirect/validator 규칙 | 각 canonical 문서 — 본 문서는 관찰만 |
| fixture/red-team의 정의·승격 승인 | fixtures / red-team-cases owner (Promotion Gate) |
| 노출 등급·packaging 검증 규칙의 정의 | exposure policy (본 문서는 실행 주기화만 제안) |
| 데이터 수집·보존·접근의 정책 실체 | `finance-user-data-policy.md` |
| 카피·고지의 작성 기준 | `finance-disclaimer-and-copy-guide.md` |
| 법률 쟁점의 식별과 검토 시점 | `finance-legal-risk-review-notes.md` |
| 모니터링 도구·인프라·RBAC 구현 | 후속 구현 작업 (§21) |
| 통지·신고 의무의 요건과 기한 | future External Legal Review |
