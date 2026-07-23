# Finance User Data Policy

## 0. Metadata

```text
status                : canonical internal operational baseline v1.0
revision note         : v0.2 — consistency review의 F-01(D-08 AI 응답의
                        조건부 분류·파생 등급 상속)과 F-05(분류표에서
                        비-사용자-데이터 분리) 반영. 제품 범위, LLM 전송
                        경계, 보존 상태, 삭제 규칙, 접근 통제, 학습 기본
                        금지, feature gate, 외부 법무 검토 표시를 보존하고
                        internal-baseline status와 bounded gates를 추가함
document type         : User Data Policy
document_id           : finance-user-data-policy
authority             : canonical internal operational baseline
lifecycle_status      : active
implementation_status : internal-control-baseline
runtime_loadable      : false
routable              : false
exposure_class        : internal-controlled (보수 분류)
distribution_boundary : repo-only — Runtime package에 포함하지 않음
external_review       : not externally reviewed
legal_advice          : false
legal_approval        : false
public_launch_gate    : unresolved
paid_launch_gate      : unresolved
decision owner        : Product Owner / Privacy Owner / Security Owner /
                        Operations Owner (prototype stage: may be held by
                        Project Owner)
parent references     :
  [0-A] 00-service-policy.md (§5~§7, §13), finance-policyguard-rules.md (§10),
        finance-output-validator.md (§14), finance-market-data-grounding.md (§8)
  [0-C] finance-routing-contract.md, finance-red-team-cases.md
  [0-D] finance-knowledge-storage-decision.md,
        finance-document-exposure-policy.md,
        finance-knowledge-artifact-metadata-standard.md
  [05] finance-legal-risk-review-notes.md
              (IN-UDP-01 ~ IN-UDP-05 입력 수용)
non-goals             : §2.2 참조
```

- 충돌 시 우선순위: `00-service-policy.md` > `finance-policyguard-rules.md` > 기타 0-A > 0-C/0-D 계약 > **본 문서**. 본 문서는 canonical의 데이터 원칙을 구현 가능한 내부 기준으로 구체화할 뿐 완화하지 않는다.
- **내부 운영 기준 한정**: 본 문서는 canonical internal operational baseline이다. 법률 자문, 법무 승인, 외부 검토 의견, 공개 출시 승인, 유료 출시 승인이 아니다.
- **개인정보처리방침 아님**: 본 문서는 내부 데이터 처리 기준이며, 대외 처리방침·이용약관의 문구를 작성하지 않는다.
- **법률 판단 없음**: 법정 보존기간·동의 요건 등 확정 판단이 필요한 항목은 결론 없이 `외부 법무 검토 필요`로 표시한다.
- **00-core 우선**: `00-core` safety and Runtime contracts take precedence over this document. Unresolved legal and provider questions remain unresolved.
- **External Legal Review**: unassigned; future public-launch and paid-launch gate.

### 표기 규칙 (3구분)

| 표기 | 의미 |
|---|---|
| `[canonical]` | canonical 문서에 이미 확정된 원칙 — 인용만 함 |
| `[제안]` | 본 문서가 보존하는 내부 운영 정책 표기 — 현행 baseline에서는 owner 결정 또는 future review가 필요한 항목 |
| `외부 법무 검토 필요` | 내부에서 결론 낼 수 없는 법률·규제 판단 항목 |

---

## 1. Document Status

| 항목 | 값 |
|---|---|
| document type | User Data Policy |
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

**본 문서는 Finance Harness가 현재 또는 향후 취급할 수 있는 사용자 데이터의 수집·사용·저장·접근·전송·로그·삭제·파기·품질 개선 이용에 대한 내부 운영 기준을 정의한다.**

- 대상 독자는 제품·백엔드·운영·보안 구현자다. 사용자 대면 문서가 아니다.
- 목표는 canonical의 데이터 원칙(§3)을 **구현 시 판단 없이 따를 수 있는 기준**으로 전개하는 것이다.

### 2.2 Authority hierarchy

본 문서의 모든 데이터 처리 기준은 아래 우선순위에 종속된다.

1. `00-core` safety and Runtime contracts
2. applicable canonical product contracts
3. `05-product-legal-ops` internal operational controls
4. `06-professional-standards` human-review and professional standards where applicable

본 문서는 service purpose, PolicyGuard intents/actions, Output Validator
behavior, market-data grounding rules, Lens routing, Runtime activation, or
human-review standards를 재정의하지 않는다. `00-core` controls over any
conflicting `05` wording.

### 2.3 포함/제외 범위

포함:

```text
- opaque identity_user_id          - Finance Profile
- 관심종목                         - 투자 일지
- 사용자 입력 가격·수량            - Checklist
- 대화 입력 (질문 원문/AI 응답)    - Journal Review 입력
- PolicyGuard 판정 로그           - Output Validator 로그
- 시장 데이터 조회 기록 (Phase 1.5+) - 운영·감사 이벤트
- 향후 이미지/OCR                 - 향후 계좌 연동
- 미래 Commerce 경계의 결제·구독 데이터 후보 (V1 미수집)
```

제외 (non-goals):

```text
- 개인정보처리방침 최종본          - 이용약관 최종본
- 법률 자문
- 계좌 연동·OCR·실시간 시세가 현재 존재한다는 가정
  (전부 현재 미존재 — 서비스 정책 §2·§5·§13)
- 광고 목적 데이터 이용의 자동 허용 (§7·§14에서 명시 금지)
- 구체 암호 알고리즘·인프라 구현의 확정 (§12 — 후속 보안 설계)
```

### 2.4 Identity / Finance / Commerce 소유권 경계

Finance는 외부 사용자를 opaque한 `identity_user_id`로만 참조한다.
Finance는 Account, Password, Credential, Access Token, Refresh Token,
Session 또는 Identity 저장 구조를 소유하지 않는다.

Finance Profile은 Identity Account와 별개의 Finance Domain 데이터다.
Finance는 Journal, Question, Checklist, Review, Watchlist와 각 데이터의
`identity_user_id` 기반 소유권을 관리한다.

결제·구독 데이터는 미래 Commerce 경계의 책임 후보다. V1은 무결제를
유지하고 결제·구독 데이터를 수집하지 않는다. 향후 요금제 또는
Entitlement가 도입되더라도 Finance PolicyGuard와 Output Validator는
모든 Finance 응답에 동일하게 적용한다.

---

## 3. Canonical Data Principles

아래는 canonical 문서에서 확인되는 확정 원칙이다. 본 절 전체가 `[canonical]`이며, 본 문서의 모든 제안은 이 원칙 아래에 종속된다.

| # | 원칙 | 근거 |
|---|---|---|
| P-01 | **최소 수집** — 투자 기록은 투자성향·자산 추론이 가능하므로 최소 수집한다 | 서비스 정책 §7·§13 |
| P-02 | **선택 입력** — 가격·수량은 선택 입력이며, 총액 자동 강조 금지, 평단·보유수량 관리·비중은 초기 제외 | 서비스 정책 §7 |
| P-03 | **계좌 연동 없음** — 증권사 API, 계좌 조회, 보유종목 자동 수집 금지 | 서비스 정책 §2·§13 |
| P-04 | **증권사 API Key 저장 없음** | 서비스 정책 §13 |
| P-05 | **OCR 없음, 원본 증권앱 이미지 저장 없음** | 서비스 정책 §13 |
| P-06 | **LLM 전송 전 민감정보 최소화** | 서비스 정책 §13 |
| P-07 | **로그 원문 최소화** — intent/메타데이터 중심 저장, 민감정보·계좌·가격·수량을 불필요하게 로그에 남기지 않음 | policyguard §10, validator §14 (blocked_terms는 키만, 원문 아님) |
| P-08 | **사용자 프로파일링 금지** — 로그 목적은 디버깅·정책 개선에 한정 | policyguard §10.2 |
| P-09 | **삭제권 제공** — 사용자 삭제 기능 제공 | 서비스 정책 §13 |
| P-10 | **암호화 또는 접근통제 검토** — Finance가 소유하는 기록 본문과 민감 필드에 암호화 또는 강한 접근통제를 적용한다 | 서비스 정책 §13 |
| P-11 | **내부 raw 문서와 사용자 응답의 분리** — raw-restricted/internal-controlled 원문은 응답·로그·디버그 어떤 경로로도 사용자 측에 도달하지 않으며, Frontend에는 sanitized output만 전달 | exposure policy §3~§4, storage decision §5·§8 |
| P-12 | **개인정보 수집·이용 시 고지** — 목적·항목·보유기간·거부권 고지 기준의 존재 (서비스 정책 §7 인용) | 서비스 정책 §7 — **구체 이행 방식은 외부 법무 검토 필요** |
| P-13 | **외부 Identity 참조** — Finance는 Account·Credential·Token·Session을 소유하지 않고 opaque `identity_user_id`만 참조한다 | 서비스 정책 §13 |
| P-14 | **Finance 데이터 소유** — Finance Profile은 Identity Account와 별개이며, Finance는 Journal·Question·Checklist·Review·Watchlist와 그 소유권을 관리한다 | 서비스 정책 §13 |
| P-15 | **Commerce 유예** — 결제·구독은 미래 Commerce 경계의 책임 후보이며 V1은 무결제·결제 데이터 미수집을 유지한다 | 서비스 정책 §13 |
| P-16 | **안전정책 불변** — 요금제·Entitlement와 무관하게 Finance PolicyGuard와 Output Validator를 항상 적용한다 | 서비스 정책 §9·§13 |

주의: canonical `exposure_class`(raw-restricted / internal-controlled / public-safe)는 **내부 Knowledge Artifact의 분류**이며, 본 문서 §5의 사용자 데이터 분류와 별개 체계다. 두 체계를 혼용하지 않는다.

---

## 4. Data Inventory

민감도 후보는 §5의 분류(Internal / Confidential / Restricted — Public은 사용자 데이터 등급으로 사용하지 않음, §5 참조)를 사용한다. 현재 미존재 데이터는 `현재 미수집`으로 표시한다.

| 데이터 ID | 데이터 유형 | 예시 | 현재 수집 여부 | 수집 목적 | 필수/선택 | 민감도 후보 | 저장 위치 후보 | LLM 전송 여부 | 로그 허용 범위 | 삭제 대상 | 법무 검토 |
|---|---|---|---|---|:---:|:---:|---|:---:|---|:---:|:---:|
| D-01 | 외부 Identity 참조 | opaque `identity_user_id` | 수집 | Finance 데이터 소유 연결 | 필수 | Internal | Finance 주 DB의 소유권 필드 | 불가 `[제안]` (요청 범위의 비식별 correlation key로 대체) | 원문 로그 기본 금지 | Identity Account 삭제 시 Finance 데이터 삭제 범위에 포함 | — |
| D-02 | Finance Profile | Finance 전용 표시·설정 값 | Profile 기능 사용 시 수집 | Finance 제품 설정·표시 | 선택 | Confidential | Finance 주 DB | 필요 필드만 (§8) | 원문 기록 금지 (마스킹) | 개별/Identity Account 삭제 시 | 고지 이행 방식 — 필요 |
| D-03 | 관심종목 | 종목명/코드 목록 | 수집 | Watchlist 기능 제공 | 선택 | Confidential `[제안]` (투자 관심 프로파일 — `finance-legal-risk-review-notes.md` §7) | 주 DB | 질문 맥락에 한해 해당 종목만 (§8) | 종목명의 일반 로그 기록 금지 `[제안]` | 개별/Identity Account 삭제 시 | 목적 확대 시 — 필요 |
| D-04 | 투자 일지 텍스트 | 판단 이유, 감정 태그, 배운 점 | 수집 | 기록 저장, 복기 | 선택 | Restricted | 주 DB (암호화 또는 접근통제 — P-10) | Review 요청 시 필요 범위만 (§8) | 본문 로그 기록 금지, 이벤트 메타만 | 개별/Identity Account 삭제 시 | 보유기간 — 필요 |
| D-05 | 사용자 입력 가격 | "178,000원" | 수집 | 기록의 선택 필드 | 선택 (P-02) | Restricted | 주 DB (암호화 검토 — P-10) | Review 필요 시 "입력값" 라벨로만 (grounding §8) | 일반 로그 복제 금지 (P-07) | 개별/Identity Account 삭제 시 | 필요 |
| D-06 | 사용자 입력 수량 | "N주" | 수집 | 기록의 선택 필드 | 선택 (P-02) | Restricted | 주 DB (동상) | 동상 | 동상 | 개별/Identity Account 삭제 시 | 필요 |
| D-07 | 질문 원문 | "하이닉스 요즘 어때?" | 수집 (처리 시) | 응답 생성 | 필수 (기능상) | Confidential | 저장 여부 자체가 미결 (§10, OQ-U-02) | 필요 범위 전송 (§8) | 무제한 저장 금지 (P-07) — intent/메타 중심 | 저장 시 삭제 대상 | 필요 |
| D-08 | AI 응답 | sanitized 응답 본문 | 수집 (처리 시) | 사용자 표시, 대화 연속성 | 필수 (기능상) | **조건부** `[제안]`: 기본 Internal. 단 Restricted 데이터(D-04~06, D-09)를 포함·재현·요약·파생한 응답(Journal Review 응답 등)은 **원천 등급(Restricted)을 상속** — §5.1 | 저장 여부 미결 (§10). Restricted 상속분은 저장 시 D-04와 동일 경계 | 해당 없음 (산출물) | validator 메타데이터 중심 (validator §14). Restricted 상속분은 응답 본문 로그 금지 | 저장 시 삭제 대상 (파생분은 원천 기록과 연동 삭제 — §10.2 원칙 3) | — |
| D-09 | Journal Review 입력 | 복기 대상 기록 선택, 후속 발화 | 수집 (처리 시) | 복기 질문 생성 | 선택 | Restricted (D-04 파생) | D-04와 동일 경계 | 필요 범위만 (§8) | 이벤트 메타만 | D-04와 연동 삭제 | 필요 |
| D-10 | PolicyGuard intent | RISK_BUY_SELL 등 12종 값 | 수집 (로그) | 안전 정책 실행·개선 | 시스템 생성 | Internal | 안전 정책 로그 저장소 | 해당 없음 | 저장 가능 (policyguard §10.1) — 단 사용자 장기 연결 제한 (§9) | §10 보존 정책 따름 | 프로파일링 경계 — 필요 (LR-11) |
| D-11 | guard action | pass/block/rewrite/fallback | 수집 (로그) | 동상 | 시스템 생성 | Internal | 동상 | 해당 없음 | 저장 가능 (동상) | 동상 | — |
| D-12 | validator violation category | 카테고리 키, severity | 수집 (로그) | 안전 검증, regression | 시스템 생성 | Internal | 동상 | 해당 없음 | 카테고리 키 중심 — 원문 금지 (validator §14) | 동상 | — |
| D-13 | rewrite/fail 결과 | rewrite_type, fallback 여부 | 수집 (로그) | 동상 | 시스템 생성 | Internal | 동상 | 해당 없음 | 저장 가능 | 동상 | — |
| D-14 | market data source/timestamp | 출처, 기준시각, 조회 시점 | **현재 미수집** (Phase 1.5+) | 표시 정확성 증빙 (`finance-legal-risk-review-notes.md` §9.1) | 시스템 생성 | Internal | 데이터 로그 저장소 | 해당 없음 | 수치·출처·시각 저장 가능 (개인정보 아님 — 단 `identity_user_id` 결합 제한 §9) | 보존 정책 따름 | 재제공 약관 — 필요 (LR-06) |
| D-15 | 삭제 요청 이벤트 | 요청·완료 타임스탬프 | 수집 (이벤트) | 삭제권 이행 증빙 (P-09) | 시스템 생성 | Internal | 감사 이벤트 저장소 | 해당 없음 | 본문 아닌 이벤트만 | 이행 증빙 목적 보존 (§10) | 보존 기간 — 필요 |
| D-16 | 이미지 | 증권앱 캡처 등 | **현재 미수집** (P-05) | — | — | Restricted (도입 시) | — | 불가 | — | — | 도입 시 — 필요 (LR-15) |
| D-17 | OCR 추출 결과 | 캡처 내 텍스트 | **현재 미수집** (P-05) | — | — | Restricted (도입 시) | — | 불가 | — | — | 도입 시 — 필요 |
| D-18 | 계좌 정보 | 계좌번호, 잔고, 보유종목 | **현재 미수집** (P-03·P-04) | — | — | Restricted (도입 시) | — | 불가 | — | — | 도입 검토 자체 — 필요 (LR-13) |
| D-19 | 미래 Commerce 데이터 후보 | 플랜, 결제·구독 상태 | **현재 미수집** (V1 무결제, 유료화 미설계) | 미래 Commerce 책임 후보 | — | Confidential (도입 시 Commerce 정책 적용) | Finance 저장 위치 없음 | 불가 | — | — | 유료화 설계 시 — 필요 (LR-12) |

---

## 5. Data Classification `[제안]`

사용자 데이터 분류 등급을 internal baseline으로 둔다. 본 표는 **사용자 데이터만** 포함한다. 비-사용자-데이터와의 구분은 아래 §5.2 대비 각주 참조.

| 등급 | 포함 데이터 | 저장 허용 범위 | 접근 주체 | 로그 허용 범위 | 외부 전송 조건 | 삭제·보존 원칙 |
|---|---|---|---|---|---|---|
| **Internal** | opaque Identity 참조(D-01), 시스템 생성 메타데이터(D-10~D-15), AI 응답(D-08)의 **기본 등급** (파생 상속 예외는 §5.1) | 내부 저장소 | Runtime + 권한 보유 내부 인력 | 메타데이터 그대로 허용. D-01 원문 로그는 금지 | 원칙 불가 — 장애 분석 등 명시 목적 + 승인 시만 | §10 보존 정책. 사용자 연결 데이터는 Identity Account 삭제 시 연결 해제 또는 삭제 |
| **Confidential** | Finance Profile(D-02), 관심종목(D-03), 질문 원문(D-07) | Finance 주 DB — 접근통제 필수 | 사용자 본인 + Runtime + 업무상 필요 최소 인력 | 마스킹/토큰화 후에만. 원문 기록 금지 | LLM 전송은 §8 최소화 규칙 하에만. 그 외 불가 | 사용자 삭제권 대상. 보존기간 RETENTION-TO-BE-DECIDED |
| **Restricted** | 투자 일지(D-04), 입력 가격/수량(D-05~06), Review 입력(D-09), Restricted 파생 AI 응답(D-08 — §5.1), (도입 시) 이미지/OCR/계좌(D-16~18) | 주 DB — 암호화 또는 강한 접근통제 (P-10) | 사용자 본인 + Runtime. 내부 인력 원문 접근은 예외 승인 + 감사 로그 필수 | **원문 로그 전면 금지.** 이벤트 메타데이터만 | LLM 전송은 §8의 필요 범위·라벨링 규칙 하에만. 그 외 전면 불가 | 사용자 삭제권 최우선 대상. 파기 절차 필수. 파생분은 원천 기록과 연동 삭제 |

- 등급 판정이 애매하면 **높은 등급으로 보수 분류**한다 (exposure policy §5.1의 최고 제한 우선 원칙과 동일한 방향 — 체계는 별개).
- Public 등급은 사용자 데이터 분류에 두지 않는다: 사용자 데이터 중 "제한 없이 공개 가능한" 유형은 현재 인벤토리(§4)에 존재하지 않으며, 존재를 가정한 등급을 만들면 하향 분류의 통로가 된다.

### 5.1 파생 데이터 등급 상속 규칙 `[제안 — F-01 반영]`

```text
1. AI 응답(D-08)의 기본 등급은 Internal이다 (모델이 생성한 일반 산출물).
2. 단, 응답이 Restricted 사용자 데이터(D-04~06, D-09)를 포함·재현·
   요약하거나 그로부터 파생된 경우(대표적으로 Journal Review 응답),
   해당 응답은 원천 데이터의 더 높은 등급을 상속한다.
3. 파생 데이터는 "모델이 생성했다"는 이유만으로 원천보다 낮게
   분류될 수 없다. 생성 여부는 등급 판정 기준이 아니며,
   판정 기준은 "무엇으로부터 왔는가"다.
4. 원천이 복수이면 가장 높은 등급을 상속한다 (보수 분류 원칙).
5. 본 규칙은 사용자 데이터 민감도 규칙이며, Knowledge Artifact의
   exposure_class 규칙이 아니다 (§5.2).
```

### 5.2 비-사용자-데이터 대비 각주 (본 분류의 대상 아님)

다음 세 체계를 혼동하지 않는다. 본 문서 §5는 ①만 다룬다.

| 체계 | 대상 | 소유 문서 |
|---|---|---|
| ① 사용자 데이터 민감도 분류 (본 절) | 사용자가 제공했거나 사용자로부터 파생된 데이터 | 본 문서 |
| ② artifact `exposure_class` (raw-restricted / internal-controlled / public-safe) | 내부 Knowledge Artifact — 렌즈·규칙·fixture·red-team 원문 | `finance-document-exposure-policy.md` `[canonical]` |
| ③ sanitized runtime output | Validator 통과 후 사용자에게 노출되는 응답 표면 — 노출 가능 여부의 문제이며, 그 응답의 **데이터 보관 등급**은 별도로 ①의 §5.1 규칙을 따름 | validator / routing-contract `[canonical]` |

- 예: public-safe 산출물은 ②의 노출 등급이지 ①의 사용자 데이터가 아니므로 본 분류표에 포함하지 않는다.
- 예: Journal Review 응답은 ③으로서 사용자에게 노출 가능(sanitized)하지만, ①로서는 Restricted 파생이므로 저장·로그·접근은 Restricted 규칙을 따른다. "노출 가능"과 "낮은 민감도"는 다른 판정이다.

---

## 6. Collection Policy

### 6.1 수집 구분 `[제안]`

| 구분 | 데이터 | 규칙 |
|---|---|---|
| 필수 참조 | D-01 | 외부 Identity가 제공한 opaque `identity_user_id`만 사용. Account·Credential·Token·Session 수집 금지 |
| 선택 수집 | D-02 | Finance Profile 기능에 필요한 Finance 전용 필드만. Identity Account 필드 복제 금지 |
| 선택 수집 | D-03~D-06, D-09 | 미입력 시에도 전 기능 사용 가능해야 함 (가격/수량 미입력 기록 허용 — P-02) |
| 처리상 수집 | D-07~D-08 | 응답 생성에 필요한 처리. **저장은 별개 결정** (§10, OQ-U-02) |
| 시스템 생성 (파생) | D-10~D-15 | 사용자 제공 데이터가 아님. §9 로그 정책 적용 |
| **기능 도입 전 금지** | D-16~D-19 | 수집 인터페이스 자체를 두지 않음 (§6.2) |

### 6.2 수집 원칙 `[제안]`

```text
1. 기능에 필요한 최소 데이터만 수집한다 (P-01의 구현).
2. 빈 필드·미사용 필드를 "나중에 쓸 수 있으니" 선수집하지 않는다.
3. 향후 기능(유료화·알림·분석)을 이유로 미리 수집하지 않는다 —
   기능 승인 시점에 해당 데이터 수집을 함께 승인한다 (§15).
4. 가격·수량은 선택 입력을 유지하고, 입력 유도 UI 압박을 두지 않는다
   (`finance-legal-risk-review-notes.md` §5.3 UX 방어와 정합).
5. 계좌·잔고·평단의 자동 수집 경로를 만들지 않는다 (P-03).
6. 이미지 첨부 슬롯은 기능 승인 전 UI에 노출하지 않는다 —
   슬롯이 존재하면 미처리라도 저장 순간 Restricted 데이터 보유가 된다
   (`finance-legal-risk-review-notes.md` §5.7).
```

---

## 7. Purpose Limitation

### 7.1 목적별 허용 매트릭스 `[제안]`

기호: ✅ 허용 / ⚠️ 조건부 (명시 조건 충족 시만) / ❌ 금지 (근거 있는 개정 전까지)

| 데이터 유형 | 서비스 제공 | 안전 검증 | 장애 대응 | 품질 개선 | 통계 | 마케팅 | 모델 학습 | 기본 정책 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| Identity 참조/Finance Profile (D-01~02) | ✅ | ✅ | ✅ | ❌ | ⚠️ 집계 수치만 | ❌ | ❌ | Finance 데이터 소유 연결·제품 설정 한정 |
| 관심종목 (D-03) | ✅ | ✅ | ⚠️ 장애 건 한정 | ❌ | ⚠️ 익명 집계만 (OQ-U-03) | ❌ | ❌ | 서비스 제공 한정 (`finance-legal-risk-review-notes.md` OQ-03 (a)안) |
| 투자 일지 (D-04~06, D-09) | ✅ | ✅ | ⚠️ 장애 건 한정 | ❌ (OQ-U-04) | ❌ | ❌ | ❌ | 기록·복기 한정 |
| 대화 (D-07~08) | ✅ | ✅ | ⚠️ 장애 건 한정 | ⚠️ 원문 아닌 메타/합성 케이스만 (`finance-legal-risk-review-notes.md` OQ-02 (a)안) | ⚠️ intent 분포 등 집계만 | ❌ | ❌ | 응답 생성·안전 실행 한정 |
| 안전 로그 (D-10~13) | — | ✅ | ✅ | ✅ (정책 개선 — policyguard §10) | ⚠️ 집계만 | ❌ | ❌ | 디버깅·정책 개선 한정 `[canonical]` |
| 시장 데이터 기록 (D-14) | ✅ | ✅ | ✅ | ✅ | ✅ (개인정보 아님) | ❌ | ❌ | 표시 정확성 증빙 |
| 감사 이벤트 (D-15) | — | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | 이행 증빙 한정 |

### 7.2 마케팅·모델 학습에 대한 명시 규칙

- **마케팅 이용은 전 데이터 기본 금지**다 `[제안]`. 허용하려면 ① 목적 고지와 동의 구조 확정(**외부 법무 검토 필요**), ② 본 문서 개정, ③ owner 승인이 모두 선행되어야 한다.
- **모델 학습 이용도 전 데이터 기본 금지**다 `[제안]`. §15의 feature gate를 통과하기 전에는 어떤 사용자 데이터도 학습 파이프라인에 넣지 않는다 (`finance-legal-risk-review-notes.md` §5.8 — 목적 외 이용, **외부 법무 검토 필요**).
- "품질 개선"과 "모델 학습"을 혼용하지 않는다: 품질 개선은 메타데이터·합성 케이스 기반 검증(§9.5)이고, 모델 학습은 사용자 데이터의 학습 투입이다. 후자는 별도 gate 대상이다.

---

## 8. LLM Transmission Policy

P-06(LLM 전송 전 민감정보 최소화)의 구현 기준이다. 특정 LLM 사업자의 약관·데이터 정책은 추정하지 않는다.

### 8.1 전송 최소화 규칙 `[제안]`

```text
1. 필요 범위 선택 전송 — 요청 처리에 필요한 필드만 전송한다.
   전체 컨텍스트의 관성적 전송을 금지한다.
2. 식별자 제거 — `identity_user_id` 등 외부 사용자 식별자를 프롬프트에
   포함하지 않는다. 세션 상관관계가 필요하면 요청 단위 임시 키를 쓴다.
3. 가격·수량 — Review 등 처리에 실제로 필요한 경우에만 전송하며,
   전송 시 "사용자 입력값" 라벨을 유지한다 (grounding §8).
   필요하지 않으면 마스킹 또는 제외한다.
4. 종목명 — 질문 처리에 필요한 범위(질문에 등장한 종목)만 전송한다.
   관심종목 목록 전체의 자동 첨부를 금지한다.
5. Journal 원문 — 전체 일지의 일괄 전송을 금지한다.
   복기 대상으로 사용자가 지정한 기록의 필요 필드만 전송한다.
6. 내부 규칙 원문 — raw-restricted/internal-controlled 문서 원문을
   프롬프트에 포함할 때는 exposure policy의 경계를 따르며,
   그 원문이 응답으로 반사되지 않도록 validator가 검사한다 (P-11).
```

### 8.2 전송 필드 목록 관리 `[제안]`

- 각 처리 경로(intent/action별)의 **전송 필드 목록을 코드가 아닌 검토 가능한 명세로 관리**하고, 변경 시 리뷰를 거친다. 이 목록이 서비스 정책 §13 "LLM 전송 전 민감정보 최소화"의 이행 증빙이 된다 (`finance-legal-risk-review-notes.md` §5.8 로그·감사 근거).
- 목록의 실제 관리 위치·검증 주기는 `finance-ops-monitoring-guide.md`로 이관 (§17).

### 8.3 프롬프트·응답 로그 `[제안]`

- 프롬프트 원문·응답 원문의 로그 저장은 **기본 금지**. 저장 필요 주장이 있으면 §9.5 품질 검증 샘플 규칙과 §18 OQ-U-02 결정을 따른다.
- validator 메타데이터(D-12~13)로 재구성 가능한 정보는 원문 저장의 근거가 되지 않는다 (`finance-legal-risk-review-notes.md` §9.1 — 재현 가능성으로 대체).

### 8.4 외부 LLM 제공사 관련 자리 표시

- LLM 제공사의 입력 데이터 보관·학습 이용 여부, 국외 이전 해당 여부와 고지 방식은 **제공사 계약·정책 확인과 함께 외부 법무 검토 필요** (`finance-legal-risk-review-notes.md` §10 "대화 기록의 LLM 제공사 전달 고지" — blocking, MVP 공개 전). 본 문서는 해당 검토 결과를 반영하는 개정 자리만 둔다.

### 8.5 LLM provider-control checklist `[internal baseline]`

LLM 제공자 또는 API 제품을 사용하기 전에 아래 체크리스트를 별도 검토
기록으로 유지한다. 이 체크리스트는 특정 제공자가 자동으로 요건을
충족한다는 의미가 아니다.

| 항목 | 상태 |
|---|---|
| provider and API product | OWNER-DECISION-REQUIRED |
| endpoint used | OWNER-DECISION-REQUIRED |
| store or persistence behavior | OWNER-DECISION-REQUIRED |
| abuse-monitoring retention | OWNER-DECISION-REQUIRED |
| application-state retention | OWNER-DECISION-REQUIRED |
| training-use status | OWNER-DECISION-REQUIRED |
| ZDR or modified-abuse-monitoring eligibility where applicable | OWNER-DECISION-REQUIRED |
| overseas processing or transfer notice requirement | LEGAL-REVIEW-REQUIRED |
| provider agreement and subprocessor review status | LEGAL-REVIEW-REQUIRED |
| fields transmitted | OWNER-DECISION-REQUIRED |
| prohibited fields | OWNER-DECISION-REQUIRED |
| configuration verification owner | OWNER-DECISION-REQUIRED |
| last verification date | OWNER-DECISION-REQUIRED |

공개 출시 전 사용자 고지와 제공자 계약·처리 조건은 future External Legal
Review gate로 남는다. 비공개 베타에서도 실제 전송 경로가 생기면
Privacy Owner, Security Owner, and Operations Owner가 위 체크리스트의
현행 상태를 확인해야 한다.

---

## 9. Logging and Audit Policy

`finance-legal-risk-review-notes.md` IN-UDP-03(증빙 필요 ≠ 원문 저장 — 메타데이터 우선)과 IN-UDP-04(한시 원문 보존은 검토 전 미시행)를 본 절에 반영한다.

### 9.1 로그 계층 구분 `[제안]`

| 계층 | 내용 | 원문 포함 |
|---|---|---|
| 운영 로그 | 요청 처리, 성능, 오류 스택 | 금지 — 사용자 데이터 필드는 마스킹/제외 |
| 보안 로그 | Finance 데이터 접근 거부, 권한 변경, 관리자 접근 | 금지 — 비식별 correlation key·이벤트만 |
| 안전 정책 로그 | D-10~D-13 (intent, action, violation category, rewrite) | 금지 — 카테고리 키·메타데이터만 (validator §14 `[canonical]`) |
| 감사 이벤트 | 삭제 요청/완료(D-15), 관리자 원문 조회, export | 금지 — 이벤트 사실만 |
| 품질 검증 샘플 | §9.5 참조 | 원칙 금지 — OQ-U-02 결정 전까지 |

### 9.2 최소 로그 원칙 `[제안 — canonical 원칙의 전개]`

```text
1. 원문보다 metadata 우선 (P-07). 메타데이터로 목적 달성이 가능하면
   원문을 저장하지 않는다.
2. 질문 원문의 무제한 저장 금지. 저장한다면 범위·기간·접근이
   §10·§11로 통제되는 별도 결정(OQ-U-02)을 거친다.
3. 가격·수량을 일반 애플리케이션 로그에 기록하지 않는다 (P-07).
   직렬화 시 해당 필드는 마스킹을 기본값으로 한다.
4. PolicyGuard intent/action은 저장 가능하다 (policyguard §10.1).
   단 사용자 식별자와의 장기 결합을 제한한다 — §9.3.
5. Validator violation은 카테고리 키 중심으로 저장한다 (validator §14).
   blocked_terms도 키만 저장하고 원문 표현을 저장하지 않는다.
6. 사용자별 장기 행동 프로파일을 생성하지 않는다 (P-08).
7. 삭제 요청과 처리 완료를 이벤트로 기록한다 (이행 증빙 — P-09).
8. Restricted 데이터에 대한 관리자 조회는 전건 감사 로그를 남긴다.
```

### 9.3 프로파일링 방지 구조 `[제안]`

- 안전 정책 로그(D-10~13)는 **question_id 단위**로 저장하고, `identity_user_id`와의 결합 조회는 장애·민원 대응 등 명시 목적 + 승인 하에서만 허용한다.
- 사용자별 intent 이력의 누적 집계(예: "이 사용자는 RISK 질문 비율이 높음")를 만들지 않는다 — PolicyGuard 차단 이력의 위험 점수화 금지(§14)와 동일 계열.

### 9.4 감사 이벤트 최소 목록 `[제안]`

```text
- 삭제 요청 접수 / 완료          - Identity Account 삭제에 따른 Finance 데이터 파기
- 관리자의 Restricted 데이터 조회 - 데이터 export 실행
- 전송 필드 목록(§8.2) 변경      - 로그 설정 변경
```

### 9.5 품질 검증 샘플

- 기본값: **원문 샘플링 금지, 합성 케이스로 검수** (`finance-legal-risk-review-notes.md` OQ-02 (a)안 채택을 전제). output-examples의 수동 검수는 합성 예시 기준으로 수행한다.
- 동의 기반 한시 샘플링(b안)으로의 전환은 **외부 법무 검토 필요** + 본 문서 개정을 전제로 한다.

---

## 10. Retention and Deletion Policy

보존기간은 canonical 근거 없이 확정하지 않는다. 상태 값은 다음 5종만 사용한다:
`NOT-COLLECTED` / `SESSION-ONLY` / `USER-CONTROLLED` / `RETENTION-TO-BE-DECIDED` / `LEGAL-REVIEW-REQUIRED`

Closed-beta gate:

- Persistent user data must not be enabled for closed beta until an
  owner-approved retention, deletion, backup, and audit treatment exists.
- The treatment may contain internally selected operational periods, but such
  periods must not be labeled legally mandated unless verified.
- `RETENTION-TO-BE-DECIDED` and `LEGAL-REVIEW-REQUIRED` remain valid status
  values where the period, exception, or legal basis is unresolved.

### 10.1 데이터별 보존 상태 `[제안]`

| 데이터 | 보존 필요성 | 기본 보존 원칙 (상태) | 삭제 요청 시 범위 |
|---|---|---|---|
| Identity 참조/Finance Profile (D-01~02) | Finance 데이터 소유 연결·제품 설정 기간 필요 | USER-CONTROLLED (Identity Account 삭제 시 Finance 보유 데이터 파기) + 법정 의무 확인 전 잔존 여부 LEGAL-REVIEW-REQUIRED | Identity Account 삭제 시 Finance 소유 데이터 전체 파기 (예외는 법무 검토 결과 반영) |
| 관심종목 (D-03) | 기능 제공 기간 | USER-CONTROLLED | 개별 삭제 즉시 반영, Identity Account 삭제 시 전체 |
| 투자 일지 (D-04~06, D-09) | 사용자가 통제하는 기록 | USER-CONTROLLED | 개별/전체 삭제, 파생(Review 캐시 포함) 연동 삭제 |
| 질문 원문 (D-07) | 응답 생성 처리 시에만 필요 | **SESSION-ONLY를 기본 제안** — 영속 저장은 OQ-U-02 결정 사항 | 저장 시 대화 삭제 기능과 연동 |
| AI 응답 (D-08) | 대화 연속성 | RETENTION-TO-BE-DECIDED (D-07 결정과 연동) | 동상. Restricted 파생분(§5.1)은 원천 기록(D-04 등) 삭제 시 연동 삭제 (§10.2 원칙 3의 파생 데이터에 해당) |
| 안전 로그 (D-10~13) | 정책 개선·regression·증빙 | RETENTION-TO-BE-DECIDED — 기간 상한을 정하되 수치 확정은 owner + 법무 검토 후 | 사용자 연결 해제 (§9.3 구조상 question_id 단위 유지 가능 — 단 결합 키 파기) |
| 시장 데이터 기록 (D-14) | 표시 정확성 증빙 | RETENTION-TO-BE-DECIDED (개인정보 아님 — 운영 비용 기준) | 해당 없음 (`identity_user_id` 미결합 원칙) |
| 삭제 이벤트 (D-15) | 이행 증빙 | RETENTION-TO-BE-DECIDED + LEGAL-REVIEW-REQUIRED (증빙 보존과 최소화의 균형) | 삭제 대상 아님 (본문 없는 이벤트) — 단 기간 상한 필요 |
| 이미지/OCR/계좌/결제 (D-16~19) | — | **NOT-COLLECTED** | — |

### 10.2 삭제·파기 공통 규칙 `[제안]`

```text
1. 삭제 요청 시 주 저장소에서 즉시 또는 단기 내 삭제하고,
   완료를 이벤트로 기록한다 (D-15).
2. 백업 내 데이터는 백업 수명 주기 내 자연 소멸 방식을 기본으로 하되,
   백업 보존 기간의 상한을 정의한다 (수치는 RETENTION-TO-BE-DECIDED).
   백업에서의 즉시 개별 삭제가 기술적으로 어려운 경우의 처리 기준은
   외부 법무 검토 필요.
3. 삭제된 데이터는 복기·통계·검증 어떤 목적으로도 계속 사용하지
   않는다 (§14 — 파생 캐시·인덱스 포함 연동 파기).
4. 법적 보존 의무가 확인되지 않은 데이터는 "의무가 있을 것"이라는
   추정으로 보존하지 않는다 — 의무 확인은 LEGAL-REVIEW-REQUIRED로
   표시하고, 확인 전 기본값은 최소 보존이다.
5. 민원 건 한시 원문 보존: 미시행 유지 (`finance-legal-risk-review-notes.md` IN-UDP-04, OQ-05).
   외부 법무 검토 완료 + 절차 신설 전에는 민원 대응 목적의
   원문 보존 조치를 시행하지 않는다.
```

---

## 11. Access Control

### 11.1 역할별 접근 원칙 `[제안]`

공통 원칙: **최소 권한, 업무상 필요(need-to-know), Restricted 접근 전건 감사 로그** (P-10·P-11, 서비스 정책 §13 "관리자 원문 접근 최소화").

| 역할 | 접근 가능 | 접근 불가 / 조건 |
|---|---|---|
| 사용자 본인 | 자신의 Finance Profile과 D-03~D-09 (조회·수정·삭제 — §13) | 타인 데이터, 내부 로그 원본, Identity Account·Credential·Token·Session |
| Application Runtime | 처리에 필요한 필드 (요청 단위) | 처리 외 목적의 대량 조회. 내부 Knowledge 원문은 exposure policy 경계 내에서만 로딩 |
| 운영 관리자 | Internal 메타데이터, 감사 이벤트 | Restricted 원문 — 예외 승인 + 감사 로그 필수 |
| 고객지원 | 민원 처리에 필요한 최소 범위 (마스킹 뷰 기본) | 일지·가격·수량 원문 — 사용자 동의 또는 승인 절차 없이는 불가 |
| 개발자 | 개발 환경의 합성/익명 데이터 | 운영 Restricted/Confidential 원문 — 장애 대응 예외도 승인 + 감사 로그 |
| 보안 담당 | 보안 로그, 감사 이벤트, 접근 이력 | 업무상 불필요한 콘텐츠 원문 |
| 데이터 분석 담당 | §7 매트릭스상 허용된 집계·익명 데이터 | 원문 접근 전면 불가 (프로파일링 금지 — P-08) |
| 외부 LLM | §8 전송 규칙을 통과한 필드만 | 그 외 전부. 계정 식별자 불가 |
| 외부 데이터 제공자 | 없음 (수신만 — 시세 데이터 유입) | 사용자 데이터 일체 — 제공처로의 사용자 데이터 유출 경로를 만들지 않음 |

### 11.2 관리자 조회 감사 `[제안]`

- Restricted/Confidential 원문 조회는 사유 입력 + 승인 + 전건 로그를 요구한다. 이 로그는 §9.4 감사 이벤트이며, 비정상 패턴 탐지는 ops-monitoring-guide 요구사항으로 이관 (§16·§17).

---

## 12. Encryption and Security Requirements

구체 알고리즘·인프라 구현은 후속 보안·백엔드 설계로 남긴다.

| 구간 | 요구 수준 | 근거/상태 |
|---|---|---|
| 전송 구간 | 전 구간 HTTPS(TLS) | `[canonical]` 서비스 정책 §13 |
| 저장 구간 (Restricted) | 암호화 또는 강한 접근통제 — 대상 필드: 일지 본문, 입력 가격/수량 | `[canonical]` "암호화 또는 DB 접근통제/암호화 검토" — **암호화 확정 여부는 미결(OQ-U-05), 방식은 후속 보안 설계** |
| 저장 구간 (Confidential) | Finance Profile·관심종목·질문 원문에 접근통제 적용 | `[canonical]` 서비스 정책 §13 |
| 애플리케이션 로그 | 사용자 데이터 필드 마스킹 기본값 (§9.2) | `[제안]` |
| 백업 | 운영 저장소와 동등 이상의 접근통제 + 수명 주기 상한 (§10.2) | `[제안]` — 구현은 후속 설계 |
| 관리자 도구 | 마스킹 뷰 기본, 원문 조회는 승인 경로만 (§11.2) | `[제안]` |
| secret/API key | 증권사 API Key 저장 없음 `[canonical]`. 자체 서비스 시크릿은 코드·로그·repo에 평문 금지 | 서비스 정책 §13 + `[제안]` |
| Identity 보안정보 | Account, Password, Credential, Access Token, Refresh Token, Session은 Finance 저장 금지 | `[canonical]` 서비스 정책 §13 |
| 데이터 export | export 파일에도 저장 등급과 동일 통제 적용, 생성·다운로드 이벤트 기록 | `[제안]` (§13 export 결정과 연동) |

---

## 13. User Controls

현재 구현 여부(canonical에 기능으로 확정되었는가)와 정책 목표(본 문서 제안)를 구분한다.

| 통제 | canonical 확정 | 정책 목표 `[제안]` | 상태 |
|---|:---:|---|---|
| 기록 조회 | ✅ (Journal 기능 자체 — 서비스 정책 §1·§7) | 본인 전체 기록의 조회 | 기능 확정 |
| 기록 수정 | — (명시 없음) | 본인 기록의 수정 허용 | 제안 — 구현 결정 필요 |
| 기록 삭제 | ✅ ("사용자 삭제 기능 제공" — §13) | 개별 + 전체 삭제, 파생 데이터 연동 삭제 (§10.2) | 원칙 확정, 범위는 제안 |
| 관심종목 삭제 | ✅ (삭제 기능 원칙의 적용 대상) | 개별/전체 즉시 삭제 | 원칙 확정 |
| Identity Account 삭제 | — (개별 명시 없음, 삭제권 원칙에서 도출) | Finance가 소유한 §10.1 범위의 데이터 파기 | 제안 — **잔존 의무는 외부 법무 검토 필요** |
| 데이터 export | — | 제공 여부 자체가 미결 (OQ-U-06). 제공 시 §12 통제 적용 | 미결 |
| 대화 기록 저장 선택 | — | D-07/D-08 저장이 결정될 경우, 저장 여부의 사용자 선택권 검토 | OQ-U-02와 연동 |
| 품질 개선 이용 거부 | — | §9.5가 원문 미사용이 기본이므로 현재는 거부 대상 자체가 없음. 동의 기반 샘플링 도입 시 opt-in 구조를 전제 | 조건부 — **외부 법무 검토 필요** |

---

## 14. Prohibited Data Practices

아래는 본 정책 하에서 **어떤 목적으로도 금지**되는 관행이다. 개정 없이는 예외가 없다.

```text
1.  계좌 연동 없이 계좌 정보를 추론·생성하는 것
    (일지·질문에서 보유분·잔고를 역산해 필드화하는 것 포함 — P-03의 우회)
2.  사용자별 투자성향 프로파일링 (P-08, §9.3)
3.  질문·일지 데이터를 광고 타기팅에 자동 사용하는 것 (§7.2)
4.  가격·수량을 일반 로그에 복제하는 것 (P-07, §9.2)
5.  원본 대화를 무기한 보관하는 것 (§9.2, §10)
6.  미승인 이미지/OCR 수집 (P-05, §6.2 — 슬롯 노출 포함)
7.  삭제된 데이터를 추천·분석·검증에 계속 사용하는 것 (§10.2)
8.  PolicyGuard 차단 이력을 사용자 위험 점수로 변환하는 것 (§9.3)
9.  개인정보를 canonical knowledge artifact(렌즈·fixture·red-team 케이스
    등)에 포함하는 것 — red-team 문서의 자리표시자 원칙(OO원/N%/N주)과
    동일 계열이며, 실제 사용자 입력의 케이스 전환은 §9.5 규칙을 따름
10. 시세 데이터 제공처 등 외부로 사용자 데이터를 전달하는 경로 신설 (§11.1)
```

---

## 15. Future Feature Gates

기능 도입 전 충족 조건을 gate로 정의한다. **gate 미통과 상태에서 해당 데이터의 수집·처리를 시작하지 않는다** (§6.2 원칙 3).

Phase 1 remains free of external market-data redistribution. User-entered
values remain user input, not verified market data.

No external market-data display, caching, screenshot use, derived-data use,
redistribution, or AI grounding use may be enabled until provider-specific
license and redistribution terms are documented.

| 기능 | 현재 상태 | 필요한 데이터 | 선행 정책 | 외부 법무 검토 | 보안 검토 | blocking |
|---|---|---|---|:---:|:---:|:---:|
| 이미지 업로드 | 미존재 (P-05) | D-16 | 본 문서 개정 + 서비스 정책 §13 개정 절차 | **필요** (LR-15) | 필요 | **Yes** |
| OCR | 미존재 (P-05) | D-17 | 이미지 gate 통과 + 추출 항목 화이트리스트 | **필요** | 필요 | **Yes** |
| 실시간 시세 / market-data display | 미연동 (Phase 1 — grounding §2) | D-14 | grounding §4 표시 의무 구현 + provider-specific license and redistribution terms documented | **필요** | 권장 | **Yes** (Phase 1.5 개시 전) |
| 계좌 연동 | 전면 금지 (P-03) | D-18 | 서비스 정책 자체의 개정 — 정체성 변경 수준 (LR-13) | **필요** (검토 착수 자체) | 필요 | **Yes** |
| 자동매매 | 전면 금지 (서비스 정책 §2) | — | 도입 대상 아님 — gate 없음 (금지 유지) | — | — | **금지 유지** |
| 유료 구독 | 미설계 (V1 무결제) | D-19 (미래 Commerce 책임 후보) | Commerce 경계 승인 + 유료화 설계 + LR-12 검토 + 결제 데이터 정책 추가 | **필요** | 필요 | **Yes** |
| 맞춤 알림 | 미존재 | D-03 + 알림 설정 | 알림 문구 기준 (copy-guide) + 권유성 판단 (`finance-legal-risk-review-notes.md` §5.5) | **필요** | 권장 | Yes |
| 모델 학습용 데이터 이용 | 금지 (§7.2) | 대상 데이터별 | 동의 구조 + opt-in + 본 문서 개정 | **필요** | 필요 | **Yes** |
| 사용자 행동 분석 | 금지 (P-08 범위) | 행동 이벤트 | 프로파일링 금지와의 경계 정의 + 익명화 기준 | **필요** | 권장 | Yes |

---

## 16. Incident and Breach Handling Requirements

사고 대응 매뉴얼은 작성하지 않는다. `finance-ops-monitoring-guide.md`가 받아야 할 요구사항만 정의한다.

```text
R-IBH-01  데이터 노출 탐지 — Restricted/Confidential 데이터가 응답·로그·
          디버그 경로로 노출되는 것의 탐지 절차 (exposure policy
          Validation Gate의 운영 주기화 — `finance-legal-risk-review-notes.md` IN-OMG-04와 통합)
R-IBH-02  비정상 관리자 접근 — §11.2 감사 로그 기반 이상 패턴 탐지와
          대응 절차 (대량 조회, 사유 없는 조회, 비업무 시간 조회)
R-IBH-03  로그 내 민감정보 혼입 — 마스킹 실패 탐지(로그 스캔),
          발견 시 정화·재발 방지 절차
R-IBH-04  LLM 전송 범위 오류 — §8.2 전송 필드 명세와 실제 전송의
          불일치 탐지, 초과 전송 발생 시 보고·기록 절차
R-IBH-05  삭제 실패 — 삭제 요청 대비 완료 이벤트 누락 감시,
          파생 데이터 잔존 검증
R-IBH-06  보존기간 초과 — §10 상태별 보존 상한 확정 후 초과 데이터
          탐지·파기 절차
R-IBH-07  외부 제공자 장애·오류 — 시세 데이터 오류/스테일 감지 시
          표시 중단 기준 (`finance-legal-risk-review-notes.md` IN-OMG-02와 통합)
R-IBH-08  개인정보 유출 사고 시 통지·신고 의무의 요건과 기한
          — 외부 법무 검토 필요 (본 문서는 자리 표시만 둠)
```

---

## 17. Downstream Document Requirements

### 17.1 → `finance-disclaimer-and-copy-guide.md`

```text
OUT-DCG-01  수집 항목·목적·선택 입력 사실을 사용자에게 표현하는
            문구 요구사항 (특히 가격/수량 "선택" 라벨 — P-02)
OUT-DCG-02  대화·기록이 AI 처리에 사용된다는 안내 문구 요구사항
            (§8.4 외부 법무 검토 결과 반영 자리 포함)
OUT-DCG-03  삭제권·데이터 통제(§13)의 안내 문구 요구사항 —
            단 처리방침 최종 문구는 작성하지 않음 (법무 산출물)
OUT-DCG-04  품질 개선/학습 이용에 대한 오해 방지 문구 —
            "기본 미사용" 상태를 과장 없이 표현
```

### 17.2 → `finance-ops-monitoring-guide.md`

```text
OUT-OMG-01  §9 로그 계층·최소 원칙의 운영 검증 (로그 스캔, 마스킹 검증)
OUT-OMG-02  §11.2 관리자 접근 감사의 모니터링 지표화 (R-IBH-02)
OUT-OMG-03  §8.2 전송 필드 명세의 관리 위치·변경 검증 절차 (R-IBH-04)
OUT-OMG-04  §10 삭제 이행·보존 상한의 감시 (R-IBH-05, R-IBH-06)
OUT-OMG-05  §16 R-IBH-01 ~ R-IBH-08 전체의 절차화
OUT-OMG-06  지표 설계 시 프로파일링 금지(P-08, §9.3) 유지 —
            사용자 단위 장기 지표 생성 금지 (`finance-legal-risk-review-notes.md` IN-OMG-05와 동일)
```

---

## 18. Open Decisions

| ID | 이슈 | 현재 근거 | 선택지 | 권장 방향 `[제안]` | owner | blocking | 외부 법무 검토 |
|---|---|---|---|---|---|:---:|:---:|
| OQ-U-01 | 보존기간 수치 확정 (안전 로그·감사 이벤트·백업 상한) | §10 — 전부 RETENTION-TO-BE-DECIDED | (a) future External Legal Review 후 일괄 확정 (b) 운영 편의 기준 선확정 | (a) — 근거 없는 수치 확정 금지 원칙 유지. 내부 운영 기간은 법정 기간으로 표시하지 않음 | Privacy Owner + Operations Owner | **Yes** (public launch 전) | **필요** |
| OQ-U-02 | 질문 원문/AI 응답의 영속 저장 여부 | §10.1 — SESSION-ONLY 기본 제안 | (a) 미저장 (대화 연속성은 세션 한정) (b) 사용자 선택 저장 (c) 기본 저장 | (a)로 시작, (b)는 §13 선택권 구조와 함께 재검토 | Product Owner + Privacy Owner | Yes (기능 설계에 영향) | 필요 ((b)(c) 시) |
| OQ-U-03 | 관심종목 익명 집계 통계 허용 | §7.1 ⚠️, legal-risk baseline | (a) 전면 금지 (b) 익명 집계만 허용 | (b) — 단 익명화 기준 정의 선행 | Product Owner + Privacy Owner | No | **필요** (익명화 충분성) |
| OQ-U-04 | 일지 데이터의 품질 개선 이용 | §7.1 ❌ | (a) 금지 유지 (b) 동의 기반 opt-in | (a) 유지 — Restricted 데이터는 품질 목적에서도 제외 | Product Owner + Privacy Owner | No | 필요 ((b) 시) |
| OQ-U-05 | Restricted 저장 암호화의 확정 (접근통제만 vs 필드 암호화) | 서비스 정책 §13 "암호화 검토" — 미확정 | (a) 필드 암호화 (b) 강한 접근통제 + 저장소 암호화 | 후속 보안 설계에서 결정 — 본 문서는 "둘 중 하나 필수"만 고정 | Security Owner | Yes (구현 전) | 불필요 (기술 결정) |
| OQ-U-06 | 데이터 export 제공 여부 | §13 — 미결 | (a) 미제공 (b) 일지·관심종목 한정 제공 | (b) 지향 — 사용자 통제 강화. 단 export 파일 보안(§12) 동시 설계 | Product Owner + Security Owner | No | 권장 (이동권 관련 — 확정 판단은 future External Legal Review) |
| OQ-U-07 | 삭제 이벤트(D-15)의 보존 상한 | §10.1 — 증빙 보존 vs 최소화 긴장 | (a) 단기 (b) 분쟁 대비 장기 | future External Legal Review 결과에 따름 — 검토 전 기본값은 최소 보존 (§10.2 원칙 4) | Privacy Owner + Operations Owner | No | **필요** |
| OQ-U-08 | 고객지원의 원문 접근 절차 | §11.1 — 마스킹 뷰 기본 | (a) 사용자 동의 기반 임시 열람 (b) 내부 승인 기반 | (a) 우선 — 동의 UX 설계와 함께 | Product Owner + Privacy Owner | No | 권장 |

---

## 19. 본 문서가 하지 않는 것

| 책임 | 담당 |
|---|---|
| 개인정보처리방침·이용약관 최종 문구 | future External Legal Review + 대외 문서 작업 (본 문서 범위 외) |
| 사용자 대면 안내 문구 요구사항의 문구화 | `finance-disclaimer-and-copy-guide.md` |
| 로그·감사·사고 대응의 운영 절차 | `finance-ops-monitoring-guide.md` |
| 암호화 방식·백업 구현 등 기술 설계 | 후속 보안·백엔드 설계 |
| intent/action/lens/validator 의미 | 각 canonical 문서 |
| 내부 Knowledge Artifact의 노출 통제 | `finance-document-exposure-policy.md` (별개 체계 — §5 주의 참조) |
| 법률 쟁점의 식별·검토 시점 | `finance-legal-risk-review-notes.md` |
