# Finance Disclaimer and Copy Guide

## 0. Metadata

```text
status                : canonical internal operational baseline v1.0
document type         : Disclaimer and Copy Guide
document_id           : finance-disclaimer-and-copy-guide
authority             : canonical internal operational baseline
lifecycle_status      : active
implementation_status : internal-control-baseline
runtime_loadable      : false
routable              : false
exposure_class        : internal-controlled (보수 분류 — 금지 표현 목록 포함)
distribution_boundary : repo-only — Runtime package에 포함하지 않음
external_review       : not externally reviewed
legal_advice          : false
legal_approval        : false
public_launch_gate    : unresolved
paid_launch_gate      : unresolved
decision owner        : Product Owner / Professional Standards Owner /
                        Operations Owner (prototype stage: may be held by
                        Project Owner)
parent references     :
  [0-A] 00-service-policy.md (§0~§4, §6, §11~§12),
        finance-policyguard-rules.md (§5), finance-output-validator.md,
        finance-market-data-grounding.md (§4~§6),
        finance-prompt-templates.md (§13 공통 금지 표현)
  [0-C] finance-routing-contract.md, finance-output-examples.md
  [0-D] finance-document-exposure-policy.md
  [05] finance-legal-risk-review-notes.md
              (R-COPY-01 ~ R-COPY-07, IN-DCG-01 ~ IN-DCG-04 수용)
              finance-user-data-policy.md
              (OUT-DCG-01 ~ OUT-DCG-04 수용)
non-goals             : §2.2 참조
```

- 충돌 시 우선순위: `00-service-policy.md` > `finance-policyguard-rules.md` > 기타 0-A > 0-C/0-D > **본 문서**. 본 문서는 canonical의 표현 금지선을 사용자 대면 카피 영역으로 전개할 뿐 완화하지 않는다.
- **역할 경계**: 본 문서는 **AI 응답 외의 사용자 대면 표면**(화면 문구·버튼·알림·마케팅)을 다룬다. AI 응답 자체의 구조와 표현은 `finance-prompt-templates.md`와 `finance-output-validator.md`가 소유하며, 본 문서는 이를 **대체하지 않는다.**
- **내부 운영 기준 한정**: 본 문서는 canonical internal operational baseline이다. 법률 자문, 법무 승인, 외부 검토 의견, 공개 출시 승인, 유료 출시 승인이 아니다.
- **법률 최종 문구 아님**: 본 문서의 모든 문구 예시는 방향을 보여주는 internal draft 표현이며, 대외 게시 전 §15 workflow(필요 시 future External Legal Review 포함)를 거쳐야 한다.
- **00-core 우선**: `00-core` safety and Runtime contracts take precedence over this document. Unresolved legal and provider questions remain unresolved.
- **External Legal Review**: unassigned; future public-launch and paid-launch gate.

### 표기 규칙 (3구분)

| 표기 | 의미 |
|---|---|
| `[canonical]` | canonical 문서에 확정된 표현 원칙 — 인용만 함 |
| `[제안]` | 본 문서가 보존하는 내부 카피 운영 기준 |
| `외부 법무 검토 필요` | 표현의 허용 한계를 내부에서 확정할 수 없는 항목 |

---

## 1. Document Status

| 항목 | 값 |
|---|---|
| document type | Disclaimer and Copy Guide |
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

**Finance Harness의 사용자 대면 문구가 투자 추천·판단 승인·수익 보장·개인화 자문처럼 읽히지 않도록, 고지·버튼·CTA·화면 제목·알림·오류 문구·마케팅 문구의 내부 작성 기준을 정의한다.**

- 대상 독자: Product Owner, Professional Standards Owner, Operations Owner,
  and copy reviewers.
- 핵심 관점: AI 응답은 PolicyGuard와 Validator가 지키지만, **화면·버튼·배너는 그 검사 밖에 있다.** 응답이 아무리 안전해도 버튼이 "매수하기"면 서비스 정체성이 무너진다 (`finance-legal-risk-review-notes.md` §8 — 편집형 표면의 어휘 기준 부재가 잔여 쟁점).

### 2.2 Authority hierarchy

본 문서의 모든 카피 기준은 아래 우선순위에 종속된다.

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
- 공통 서비스 고지 / AI 응답 고지        - Learn / Checklist 화면
- Journal / Journal Review               - Watchlist / 관심종목 기반 질문
- 시장 데이터 표시 (Phase 1.5+)          - 버튼과 CTA / 알림
- 빈 상태 / 오류·데이터 부재 문구         - 유료화 문구 (향후)
- 마케팅·스토어·랜딩 페이지              - 데이터 수집·AI 처리 안내 요구사항
```

제외 (non-goals):

```text
- 개인정보처리방침·이용약관 최종본 (future External Legal Review 산출물)
- 법률 자문
- Runtime Prompt Template의 재작성 (templates 소유)
- PolicyGuard 규칙 변경
- 실제 화면 설계 (UX 산출물)
- 마케팅 캠페인 제작
```

---

## 3. Canonical Copy Boundaries

아래 금지선은 canonical에 확정되어 있으며, **카피 영역에도 동일하게 적용된다.** 본 절 전체가 `[canonical]`이다.

| # | 금지선 | 근거 |
|---|---|---|
| B-01 | 매수·매도·보유 권유 표현 ("사세요/파세요/보유하세요/정리하세요") | 서비스 정책 §2·§3, policyguard §5.1 |
| B-02 | 목표가·손절가·수량·비중 제시 및 그 유도 표현 | 서비스 정책 §2, policyguard §5.2~5.3 |
| B-03 | 종목·상품 추천 (업종 단위 포함) | 서비스 정책 §2, routing-contract §3 |
| B-04 | 수익 보장·손실 방지·승률 표현 ("수익 가능/손실 방지/확실합니다") | 서비스 정책 §2·§12, policyguard §5.4 |
| B-05 | 판단 승인·과거 평가 (긍정 평가 포함 — "잘 샀다"도 금지) | 서비스 정책 §8, routing-contract §7 (journal-review 금지 우회 경로), validator §12 |
| B-06 | 개인 맞춤 포트폴리오 조정 암시 | 서비스 정책 §2 |
| B-07 | 데이터 근거 없는 현재형 단정 ("외국인이 사고 있다") — 출처+기준시각 없는 현재형 문장 금지 | grounding §4·§6 |
| B-08 | 면책 문구로 위험 답변을 정당화하는 구조 — 고지는 금지 표현을 허용하지 않음 | PI-01 계약, validator §3 (missing disclaimer는 별개 항목), `finance-legal-risk-review-notes.md` LR-14 |
| B-09 | 내부 PolicyGuard·Validator·red-team 규칙의 노출 — intent/action 이름, 금지어 목록, 차단 규칙 상세를 사용자 대면 문구에 포함 금지 | exposure policy §4.1~§4.2, storage decision §8 |
| B-10 | 익절/손절 구간 등 행동 프레이밍이 붙은 가격 비교 | 서비스 정책 §6, policyguard §5.5 |

- **거절 문맥 예외** `[canonical]`: 금지 항목을 **지칭·설명**하는 표현("목표가는 제공하지 않습니다")은 허용된다. 제시형("목표가는 OO원")은 어떤 문맥에서도 금지다 (templates §13, fixtures §9).

---

## 4. Copy Classification `[제안]`

모든 사용자 대면 카피는 아래 5분류 중 하나로 판정한다. R-COPY-03(목록 외 완곡 표현의 판정 절차)의 이행 장치다.

| 분류 | 의미 | 사용 절차 |
|---|---|---|
| `REQUIRED` | 해당 화면·상황에서 **누락되면 안 되는** 문구 (고지 등) | 누락 시 배포 불가. §14 체크리스트의 blocking 항목 |
| `ALLOWED` | 본 가이드의 허용 프레임에 부합 — 자유 사용 | §15 workflow의 self-review + 금지 표현 검사만으로 배포 가능 |
| `CAUTION` | 맥락에 따라 위험해질 수 있는 표현 — 조건부 | Product Owner + Professional Standards Owner 검토 필수 (§15 4~5단계). 조건 이탈 시 PROHIBITED로 재판정 |
| `PROHIBITED` | §3 금지선 또는 본 가이드 금지 목록 해당 | 사용 불가. 발견 시 즉시 수정 + §16 모니터링 회귀 사례 등록 |
| `LEGAL-REVIEW-REQUIRED` | 허용 한계를 내부에서 확정할 수 없는 표현 | future External Legal Review 완료 전 공개 사용 불가. 검토 결과를 본 문서 개정으로 반영 |

- 판정이 애매하면 **더 제한적인 분류로 보수 판정**한다 (Input Guard의 보수 분류 원칙과 동일한 방향 — policyguard §7.2).

---

## 5. Common Disclaimer Architecture

### 5.1 고지의 6개 역할 구분 `[제안]`

하나의 만능 고지로 모든 역할을 겸하게 하지 않는다. 역할별로 위치와 반복 조건이 다르다.

| 고지 유형 | 역할 | 위치 | 반복 조건 |
|---|---|---|---|
| ① 서비스 성격 고지 | "판단 도구가 아니라 체크리스트·기록 도구" — 서비스 정체성 (서비스 정책 §0) | 온보딩, 랜딩, 설정 내 상시 접근 가능 위치 | 최초 1회 + 상시 접근 |
| ② 투자 판단 비제공 고지 | "매수·매도 판단, 목표가, 종목 추천을 제공하지 않음" | 온보딩 + AI 대화 진입점 | 주요 기능 첫 진입 시 |
| ③ 데이터 최신성·출처 고지 | 표시 수치의 출처·기준시각·지연 여부 (grounding §4 `[canonical]` — 표시 의무) | 수치가 표시되는 모든 화면, 수치 인접 위치 | **수치 표시마다 — 생략 불가** |
| ④ 사용자 입력값 고지 | "이 값은 사용자가 입력한 값" 라벨 (grounding §8 `[canonical]`) | 입력가·수량이 표시되는 모든 위치 | 표시마다 |
| ⑤ AI 생성 결과 고지 | 응답이 AI 생성이며 투자 권유가 아님 (templates 5단 구조의 고지 `[canonical]` — 응답 내부는 templates 소유) | 응답 영역 하단 고정 (화면 레벨 보조 표시) | 응답마다 (응답 내부) + 화면 상시 |
| ⑥ 기록·복기 기능 고지 | "복기는 과거 평가·행동 지시가 아님" (서비스 정책 §8) | Journal Review 진입점 | Review 세션 시작 시 |

### 5.2 고지 공통 원칙

```text
1. [canonical] 고지는 금지 답변을 허용하지 않는다.
   고지가 붙어 있어도 B-01~B-10 위반 표현은 동일하게 금지다.
   "면책 문구를 추가했으니 더 구체적으로 말해도 된다"는 구조는
   존재하지 않는다 (B-08).
2. [제안] 고지 생략 금지 조건 — 다음 위치에서 고지는 REQUIRED이며
   화면 축약(모바일 반응형 포함)을 이유로 생략·접힘 처리하지 않는다:
   수치 인접 고지(③·④), AI 응답 고지(⑤).
3. [제안] 고지 약화 금지 — canonical 고지 취지("투자 권유가 아닌
   체크리스트")를 유지하며, 축약 시에도 "권유 아님"이 탈락하지 않는다
   (`finance-legal-risk-review-notes.md` IN-DCG-03).
4. [제안] 고지의 최종 문구 확정은 future External Legal Review gate다
   (`finance-legal-risk-review-notes.md` §8 — 고지 문구 충분성).
   본 문서의 문구는 내부 방향 예시다.
```

Public-copy hard gate:

- No public landing page, application-store listing, SNS campaign, paid-plan
  copy, or other public marketing copy may ship until the public-facing copy
  has been separately reviewed.
- Internal examples remain labeled `INTERNAL DRAFT — NOT LEGALLY APPROVED`.
- This gate is not public-launch approval and does not create legal approval.

---

## 6. Feature-by-feature Copy Guide

공통 전제: "허용 프레임"은 화면 제목·리드 문구·설명 텍스트의 방향이고, 예시는 확정 카피가 아니다.

| 기능 | 허용 프레임 `[제안]` | 주의 표현 (CAUTION) | 금지 표현 (PROHIBITED) | 필수 고지 (REQUIRED) | CTA 원칙 | 외부 법무 검토 |
|---|---|---|---|---|---|---|
| Learn | "개념을 이해한다" — 예: "리밸런싱 알아보기" | "실전 활용" 류 — 행동 연결 인상 | "이 개념으로 수익 내기", 개념→종목 연결 문구 | ①·⑤ | "공부 노트로 저장" 계열 (서비스 정책 §11) | 정기 발행 콘텐츠 확장 시 (`finance-legal-risk-review-notes.md` §5.1) |
| Checklist | "확인할 항목을 본다" — 예: "판단 전 체크리스트" | 완료율·게이지 표시 문구 — 점수·신호로 읽히지 않게 (`finance-legal-risk-review-notes.md` §5.2) | "체크 통과 = 매수 가능", 항목 충족의 판정 문구 | ②·⑤ | "체크리스트 계속 보기" 계열 | 결과 요약 기능 추가 시 |
| Journal | "내 생각을 기록한다" | 입력 유도 압박 문구 ("정확히 입력해야 분석 가능") | "수익률 관리", "포트폴리오 관리" 류 명명 | ①·④ + 가격/수량 "선택" 라벨 (P-02, OUT-DCG-01) | "투자 기록으로 저장" 계열 | — |
| Journal Review | "기록을 돌아보는 질문을 받는다" | "패턴 발견" 류 — 진단 인상 (§9 참조) | "AI 진단", "잘한 판단/실수 분석", 행동 제안 섹션 명 | ⑥·⑤ | "AI와 복기하기" 계열. "다음 행동 받기" 금지 | 복기 통계 확대 시 (`finance-legal-risk-review-notes.md` §5.4) |
| Watchlist | "관심종목을 저장하고 질문 진입점으로 쓴다" | 등락 색상 외 시각 강조 문구 | "주목 종목", "유망 종목", "추천 정렬" (`finance-legal-risk-review-notes.md` §5.5) | ③ (시세 표시 시) | "관심종목 등록/체크리스트 보기" | 알림 도입 시 |
| 시장 데이터 (Phase 1.5+) | "참고용 수치를 출처·기준시각과 함께 본다" | "실시간" 강조 — 지연 시세면 사용 불가 | "저평가/고평가", "기회", 수치+방향성 결합 문구 (validator §10) | ③ — 수치마다 | 데이터 화면에 매매 유도 CTA 금지 | **표시 개시 자체** (LR-05·LR-06) |
| 관심종목 기반 질문 | "이 종목의 체크리스트를 본다" | "이 종목 어때?" 류 유도 문구 — 평가 요청 인상 | "분석받기", 조합 평가 문구 (`finance-legal-risk-review-notes.md` §5.9) | ②·⑤ | "체크리스트 보기" 고정 — "분석받기" 금지 | 조합 단위 응답 시 |
| 알림 (향후) | 사실 통지 한정 — "저장한 기록이 N건" 류 | 시세 변동 알림 문구 — 시점 신호화 위험 | "지금 확인하세요"(긴급성), "기회" 프레이밍 | 알림 내 ③ (수치 포함 시) | 알림 탭 목적지는 체크리스트/기록 — 매매 화면 아님 (매매 화면 자체가 없음) | **도입 시 필수** — 권유성 판단 (`finance-legal-risk-review-notes.md` §5.5) |
| 유료 Plan (향후 공개) | 사용량·분석 범위·Lens·반복·후속 분석·편의 기능의 차이를 사실로 설명 | "더 깊은 분석"을 투자 판단 강화·성과 개선으로 읽히게 하는 표현 (`finance-legal-risk-review-notes.md` §5.10) | "유료로 더 정확한 판단", "프리미엄 추천", "더 나은 투자 판단" | ①·② (플랜 소개 내) | "플랜 보기" — "수익 기회 잠금해제" 금지 | **유료화 설계 시 필수** (LR-12) |

---

## 7. Button and CTA Guide

서비스 정책 §11 `[canonical]`: **버튼은 매매 행동이 아니라 기록 행동을 유도한다.** 아래는 그 원칙의 유형별 전개다 `[제안]`.

| 목적 | 권장 CTA 후보 | 금지 CTA 후보 | 위험 이유 | 검토 owner |
|---|---|---|---|---|
| 시작 | "시작하기", "질문 만들어보기" | "지금 진입", "투자 시작" | "진입"은 매매 어휘 — 시점 판단 인상 (B-01·B-02 계열) | Product Owner + Professional Standards Owner |
| 저장 | "공부 노트로 저장", "투자 기록으로 저장" `[canonical]` | "포지션 저장", "매수 기록 완료" | 매매 실행의 완료 인상 | Product Owner |
| 복기 | "AI와 복기하기" `[canonical]`, "기록 돌아보기" | "AI 진단 받기", "다음 행동 확인" | 진단·처방 인상 (B-05·B-06) | Product Owner + Professional Standards Owner |
| 체크리스트 | "체크리스트 계속 보기" `[canonical]`, "확인 항목 보기" | "매수 조건 확인", "통과 여부 보기" | 체크리스트의 판정 도구화 | Product Owner + Professional Standards Owner |
| 다시 질문 | "추가 질문하기" `[canonical]`, "질문 바꿔보기" | "진짜 답 듣기", "솔직한 의견 듣기" | 우회 유도 — PI 계열 프레이밍의 UI화 | Professional Standards Owner |
| 관심종목 | "관심종목 등록", "체크리스트 보기" `[canonical]` | "추천 종목 담기", "유망주 등록" | 추천 어휘 (B-03) | Product Owner + Professional Standards Owner |
| 데이터 확인 | "출처·기준시각 보기", "확인할 항목 보기" | "목표가 확인" `[canonical 금지]`, "적정가 보기" | 가격 제시 요구의 UI화 (B-02) | Professional Standards Owner |
| 알림 | "기록 알림 설정" | "매수 타이밍 알림", "급등 알림" | 시점 신호 제공 (B-01·B-04) | Product Owner + Professional Standards Owner + future External Legal Review |
| 유료 플랜 | "플랜 보기", "기록 공간 늘리기" | "수익 기회 보기", "AI 추천 받기", "맞춤 포트폴리오 받기" `[canonical 금지 계열]` | 유료 = 판단 제공의 구조화 (LR-12) | Product Owner + future External Legal Review |

- 신규 CTA 추가 시 §14 체크리스트 + §15 workflow를 통과해야 한다 (R-COPY-06 이행).

---

## 8. Approval and Encouragement Boundary

R-COPY-02(격려와 판단 승인의 경계)의 이행 절이다. `finance-legal-risk-review-notes.md` OQ-04 (a)안 — **기록 행위에 대한 격려만 허용** — 을 기준선으로 제안한다 `[제안]`.

| 유형 | 판정 | 예시 방향 |
|---|---|---|
| 기록 행위에 대한 중립적 격려 | ALLOWED | "기록을 남겼습니다", "3번째 기록이에요" — 행위 사실의 확인 |
| 학습 진행에 대한 격려 | ALLOWED | "이 개념을 공부 노트에 정리했어요" — 학습 행위 대상 |
| 투자 판단에 대한 승인 | **PROHIBITED** | "좋은 판단입니다", "현명한 선택이에요" |
| 과거 매매에 대한 평가 | **PROHIBITED** (긍정 포함) | "잘 매수했습니다", "그때 판단이 옳았네요" |
| 향후 행동 지시 | **PROHIBITED** | "이제 정리할 때입니다", "다음엔 이렇게 하세요" |

**금지 이유** — "좋은 판단입니다" 류가 금지되는 근거:

- canonical이 복기를 "과거 행동 평가가 아니라 기록 기반 회고 질문 생성"으로 한정하며(서비스 정책 §8), routing-contract §7은 journal-review의 금지 우회 경로에 **"과거 평가(긍정 포함)"**를 명시한다. 긍정 평가도 평가다.
- 승인 표현은 서비스가 사용자의 매매 판단을 **검증해 주는 주체**라는 인상을 만든다. 이는 "판단하지 않고 기록하게 한다"는 불변 원칙과 정면 충돌하며, 사후에 "서비스가 내 매수를 승인했다"는 주장의 근거로 소비될 수 있다 (LR-01·LR-04의 오인 경로).
- 격려가 매매 결과와 연동되면("수익 났네요, 축하해요") 성과 프레이밍(B-04)으로 전이된다. 격려의 대상은 **항상 기록·학습 행위**여야 하고, 판단·결과여서는 안 된다.

---

## 9. Analysis, Diagnosis, Personalization Terms

R-COPY-04의 이행 절이다. "허용 가능 맥락"이라도 §4 분류상 CAUTION이며, 검토 절차를 전제로 한다.

Until separately reviewed, public use of the following terms is prohibited:

- `personalized`
- `custom`
- `맞춤`
- `개인화`
- `portfolio review`
- `portfolio assessment`
- `포트폴리오 평가`
- `AI investment assistant`
- `AI 투자 어시스턴트`

The bounded internal expression `저장한 기록을 바탕으로 한 복기 질문` may
appear only as `INTERNAL DRAFT — NOT LEGALLY APPROVED` copy and must not imply a
recommended next action.

| 어휘 | 허용 가능 맥락 | 위험 맥락 | 대체 표현 `[제안]` | 법무 검토 |
|---|---|---|---|:---:|
| 분석 | 일반명사적 서술 최소 사용 ("시장을 분석하는 관점들") — CAUTION | "AI 분석 결과", "종목 분석" — 리포트 격상 (`finance-legal-risk-review-notes.md` §6.1 권위 표현) | "확인 관점", "체크리스트" | 권장 |
| 진단 | **사용하지 않음** — PROHIBITED 제안 | "기록 진단", "투자 습관 진단" — 개인 평가 인상 | "돌아보기", "복기 질문" | 사용 재개 시 필요 |
| 평가 | 서비스가 아닌 사용자 주어일 때만 ("스스로 평가해볼 질문") — CAUTION | 서비스 주어의 평가 ("AI가 평가한") — B-05 | "확인", "점검" | — |
| 추천 | 거절·정책 설명 문맥만 ("추천을 제공하지 않습니다") `[canonical 예외]` | 그 외 전부 — B-03, PROHIBITED | "진입점", "질문 만들기" | — |
| 맞춤 | public use prohibited until separately reviewed | "맞춤 체크리스트", "맞춤 진단" — 개별 자문 인상 (LR-04) | INTERNAL DRAFT only: "저장한 기록을 바탕으로 한 복기 질문" | **필요** |
| 개인화 | public use prohibited until separately reviewed | "당신만을 위한" — `finance-legal-risk-review-notes.md` §6 | INTERNAL DRAFT only: "저장한 기록을 바탕으로 한 복기 질문" | **필요** |
| 전망 | **사용하지 않음** — PROHIBITED 제안 (사실 단정·방향성 평가 계열) | "시장 전망", "실적 전망" — B-07, grounding §6 | "확인할 항목", "볼 관점" | — |
| 예측 | **사용하지 않음** — PROHIBITED 제안 | "AI 예측" — 성과 암시 (B-04 인접) | 마케팅에서는 "예측하지 않는 도구"로 부정형 사용만 CAUTION 허용 | 권장 |
| 신호 | **사용하지 않음** — PROHIBITED 제안 (routing-contract §7 supply-demand "수급의 신호화" 금지와 동일 계열) | "매수 신호", "수급 신호" | "확인 항목", "체크 포인트" | — |
| 점수 | **사용하지 않음** — PROHIBITED 제안 (체크리스트 점수화 — `finance-legal-risk-review-notes.md` §5.2) | "투자 점수", "체크 점수" | "확인한 항목 수" (수량 사실만, 판정 없이) — CAUTION | 사용 재개 시 필요 |

---

## 10. Market Data Copy

Phase 1(데이터 미연결)과 Phase 1.5+(표시 개시)를 구분한다. grounding §4의 표시 의무가 `[canonical]` 기반이다.

| 상황 | 카피 원칙 | 방향 예시 (확정 문구 아님) |
|---|---|---|
| 출처 | 수치 인접에 출처 상시 표기 (③ 고지 — REQUIRED) | "출처: {제공처명}" |
| 기준시각 | 수치 인접에 기준시각 상시 표기 — 축약 레이아웃에서도 생략 금지 (§5.2) | "{날짜 시각} 기준" |
| 지연 여부 | 실시간이 아니면 지연임을 명시 `[canonical]` | "지연 시세" 라벨 |
| 데이터 부재 (Phase 1 기본) | **현재 수치가 없을 때 실제 값처럼 표현하지 않는다.** placeholder를 수치 형태로 두지 않음 | "시세 정보를 제공하지 않습니다" / "데이터 미연결" |
| 데이터 오류 | 오류 의심 수치는 표시하지 않고 상태 문구로 대체 (`finance-legal-risk-review-notes.md` §8 이상치 — 기준은 ops-monitoring-guide) | "데이터를 불러올 수 없습니다" |
| 스테일 데이터 | 갱신 실패 시 오래된 값을 최신처럼 두지 않음 — 기준시각 강조 또는 표시 중단 | "{시각} 이후 갱신되지 않았습니다" |
| 사용자 입력값 | "입력값/기록 기준" 라벨 필수 (④ 고지 — grounding §8 `[canonical]`) | "내 기록 기준: {값} (입력값)" |
| 수치와 AI 해석의 분리 | 데이터 블록과 체크리스트 블록의 시각·문구 분리 (서비스 정책 §5 `[canonical]`, `finance-legal-risk-review-notes.md` §8) | 데이터 영역 제목 "참고 수치" / AI 영역 제목 "확인할 항목" |
| 단순 비교 | 중립 수치 비교까지만 — "익절/손절 구간" 프레이밍 금지 (B-10) | "입력가 대비 단순 차이 +N%" (서비스 정책 §6 형식) |

---

## 11. User Data and AI Processing Copy Requirements

`finance-user-data-policy.md`의 OUT-DCG-01~04를 카피 요구사항으로 전개한다. **처리방침 최종 문구는 작성하지 않으며**, 아래는 화면 문구가 충족할 방향이다 `[제안]`.

```text
C-DATA-01  가격·수량 입력 필드에 "선택" 라벨을 상시 표기한다 (P-02).
           미입력으로도 기록이 저장됨을 빈 상태 문구로 안내한다.
C-DATA-02  관심종목·일지의 사용 목적을 기능 안내에서 과장 없이 표현한다
           — "기록과 복기를 위해 저장됩니다" 방향.
           통계·마케팅 이용을 암시하는 문구 금지 (user-data-policy §7).
C-DATA-03  AI 처리 안내 — "질문/복기에 필요한 범위만 AI 처리에
           사용됩니다" 방향 (§8 전송 최소화의 사용자 표현).
           외부 LLM 제공사 관련 고지의 최종 표현은
           외부 법무 검토 필요 (user-data-policy §8.4) — 검토 전
           확정 문구를 게시하지 않는다.
C-DATA-04  모델 학습 — "회원님의 기록을 모델 학습에 기본적으로
           사용하지 않습니다" 방향. 단 이는 internal baseline이므로
           (user-data-policy §7.2), 정책 확정 전 대외 약속형 표현
           ("절대/영원히")을 쓰지 않는다.
C-DATA-05  삭제권·사용자 통제 — 기록·관심종목·계정 삭제 기능의 존재를
           설정 화면에서 안내한다 (P-09). 삭제 범위의 상세 표현은
           user-data-policy §10 확정 후 반영.
C-DATA-06  이미지/OCR·계좌 연동 — 현재 미존재 기능을 "준비 중" 등으로
           예고하지 않는다 (기능 gate 미통과 — user-data-policy §15).
           문의 응대 문구는 "제공하지 않습니다"로 고정.
C-DATA-07  대화 저장 여부 — 미확정 사항 (user-data-policy OQ-U-02).
           확정 전에는 "대화가 저장됩니다/저장되지 않습니다" 어느 쪽의
           단정 문구도 게시하지 않는다.
```

---

## 12. Error, Refusal, and Fallback Copy

원칙 `[제안]`: 사용자에게는 **상황과 다음 행동**만 전달한다. **PolicyGuard의 intent/action 이름, 금지어 목록, 차단 규칙 상세를 문구에 노출하지 않는다** (B-09 — exposure policy §4). AI 응답 내부의 거절 구조는 templates(§7~§12)가 소유하며, 본 절은 **화면 레벨 상태 문구**만 다룬다.

| 상황 | 문구 원칙 | 방향 예시 (확정 문구 아님) | 금지 |
|---|---|---|---|
| 정책상 제공 불가 | 무엇을 제공하지 않는지 + 대안 (체크리스트/기록) 제시. 거절 문맥 예외(§3)의 범위 내 표현 | "매수·매도 판단은 제공하지 않아요. 대신 확인할 항목을 정리해드릴게요." | 내부 규칙 인용 ("RISK_BUY_SELL로 분류되어…"), 차단 로직 설명 |
| 데이터 부재 | 단정 불가 사실 + 직접 확인 안내 (§10) | "현재 데이터가 연결되어 있지 않아요." | 추정치 제시 |
| 데이터 최신성 확인 불가 | 기준시각 불명 시 수치 비표시 원칙 유지 | "최신 데이터를 확인할 수 없어 표시하지 않아요." | 오래된 값의 무표기 표시 |
| 질문이 모호함 | 명확화 유도 — 서비스가 할 수 있는 일의 방향 제시 (templates §11~§12의 화면 표현) | "어떤 도움이 필요한지 골라주세요: 개념 설명 / 체크리스트 / 기록 복기" | "질문이 잘못됐습니다" 류 사용자 귀책 표현 |
| 내부 오류 | 사과 + 재시도 안내. 오류 상세·경로·스택 비노출 (exposure policy — 경로 노출 금지) | "일시적인 문제가 발생했어요. 잠시 후 다시 시도해주세요." | 파일 경로, 내부 컴포넌트명, validator 동작 서술 |
| 안전한 질문으로 재구성 | 재구성 제안을 서비스 가치("질문 보정")로 표현 | "이 질문을 체크리스트로 바꿔서 볼 수 있어요." | "당신의 질문은 위험합니다" 류 낙인 표현 |

---

## 13. Marketing and Public Copy

서비스 정책 §12 `[canonical]`가 기반이다. 채널별로 전개한다 `[제안]`.
No public landing page, application-store listing, SNS campaign, paid-plan
copy, or other public marketing copy may ship until the public-facing copy has
been separately reviewed.

Paid launch is blocked pending a separate paid-service review. Paid or premium
copy must not imply:

- deeper investment judgment
- greater accuracy
- improved returns
- reduced losses
- personalized advice
- portfolio advice
- premium recommendations
- rankings or signals

Product Policy에서 말하는 분석 깊이는 Lens, 분석 범위, 반복·후속 분석 또는
편의 기능의 내부 차등이다. 공개 카피는 이를 더 정확한 투자 판단, 더 높은 수익,
전문가보다 나은 판단 또는 성공 가능성 향상으로 표현해서는 안 된다.

### 13.1 전 채널 공통 금지 계열 (PROHIBITED)

```text
- "수익률 개선 / 수익률을 높여주는"     - "손실 감소 / 손실을 줄여주는" (보장형)
- "급등주 탐색 / 급등주 추천"           - "AI 종목 추천 / AI가 고른 종목"
- "시장을 이기는 방법"                  - "당신만을 위한 투자 전략"
- "정확한 매수 시점 / 매수 타이밍 포착"  - "자동 투자 / 리딩"
```

- 근거: 서비스 정책 §12 금지 문구 + 금융소비자보호 관련 "단정적 판단 제공, 이익 보장·손실 보전 오인 표현" 서술 (서비스 정책 §12 인용 범위 내).

### 13.2 채널별 원칙

| 채널 | 허용 프레임 | 채널 고유 주의점 | 검토 |
|---|---|---|---|
| 랜딩 페이지 | "질문을 체크리스트로 바꿔주는 AI 투자 노트" `[canonical 허용 문구 계열]` | 히어로 카피의 과장 압력이 가장 큰 지점 — "수익" 어휘 전면 배제 | Product Owner + future External Legal Review |
| 앱스토어 설명 | 기능 사실 서술 (기록/체크리스트/복기) | 스토어 키워드·태그에 "종목추천/급등주" 류 삽입 금지 — 검색 유입 목적이라도 금지 (LR-07) | Product Owner + future External Legal Review |
| SNS | 개념 교육·기록 습관 콘텐츠 | 시황 코멘트 형식 금지 — 계정이 시장 단정을 발화하면 B-07 위반의 대외 버전 | Product Owner + Professional Standards Owner |
| 배너 | 기능 안내 한정 | 종목명 + 화살표/수익률 이미지 조합 금지. 편집형 종목 큐레이션 금지 기조 (`finance-legal-risk-review-notes.md` OQ-06 (a)안) | Product Owner + Professional Standards Owner |
| 뉴스레터 | 개념 설명·서비스 소식 | 정기 발행물의 시장 논평화 금지 — 확장 시 **future External Legal Review 필요** (`finance-legal-risk-review-notes.md` §5.1) | Product Owner + future External Legal Review |
| 유료 플랜 소개 | 량·편의 확장 서술 (§6 유료 행) | "유료 = 더 강한 판단" 구조 금지 (LR-12) — **유료화 설계 시 future External Legal Review 필요** | Product Owner + future External Legal Review |

---

## 14. Review Checklist `[제안]`

카피 배포 전 검사 항목. blocking=Yes 항목의 Fail은 배포 불가다.

| 검토 항목 | Pass 기준 | Fail 예시 | reviewer | blocking |
|---|---|---|:---:|:---:|
| §3 금지선 위반 없음 | B-01~B-10 해당 표현 부재 (거절 문맥 예외만 허용) | "목표가 확인" 버튼 | Professional Standards Owner | **Yes** |
| REQUIRED 고지 존재 | §5.1 위치별 고지 누락 없음 | 시세 수치에 기준시각 없음 | Product Owner + Professional Standards Owner | **Yes** |
| 판단 승인·평가 표현 없음 | §8 기준 — 격려는 기록·학습 행위 대상만 | "좋은 판단이에요" 토스트 | Product Owner + Professional Standards Owner | **Yes** |
| §9 어휘 경계 준수 | 진단/전망/예측/신호/점수 미사용, 맞춤/개인화는 future External Legal Review 완료 전 미사용 | "AI 진단 리포트" 화면 제목 | Product Owner + Professional Standards Owner | **Yes** |
| CTA 원칙 준수 | §7 금지 CTA 부재, 기록 행동 유도 | "지금 진입" 버튼 | Product Owner + Professional Standards Owner | **Yes** |
| 데이터 카피 준수 | §10 — 부재 시 수치형 placeholder 없음, 입력값 라벨 존재 | 미연결 상태에 "0원" 표시 | Product Owner | **Yes** |
| 내부 규칙 비노출 | intent/action/금지어 목록/경로 등 내부 정보 부재 (B-09) | 오류 문구에 내부 분류명 | Professional Standards Owner + Security Owner | **Yes** |
| 데이터 안내 정합 | §11 C-DATA-01~07 — 미확정 사항의 단정 없음 | "대화는 저장되지 않습니다" (미확정 단정) | Product Owner + Privacy Owner | Yes |
| 마케팅 금지 계열 부재 | §13.1 목록 + 유사 완곡 표현 보수 판정 | "실수를 줄여주는 AI" | Product Owner + future External Legal Review | **Yes** |
| LEGAL-REVIEW-REQUIRED 처리 | 해당 표현이 검토 완료 전 미사용 | 검토 전 "맞춤 리포트" 카피 집행 | Product Owner + future External Legal Review | **Yes** |

---

## 15. Copy Change Workflow `[제안]`

```text
1. 신규 카피 작성
   - 작성자는 본 가이드 §3~§13을 참조해 초안 작성
2. 작성자 self-review
   - §14 체크리스트 자가 점검, §4 분류 자가 판정
3. 금지 표현 검사
   - §3 금지선 + §9 어휘 + §13.1 마케팅 금지 계열 대조
   - 목록에 없는 완곡 표현은 보수 판정 (CAUTION 이상)
4. Product Owner 검토
   - 기능 맥락 적합성, §6 기능별 기준 확인
5. Professional Standards Owner 검토
   - CAUTION 이상 분류 카피 필수. REQUIRED 고지 배치 확인
6. 필요 시 future External Legal Review
   - LEGAL-REVIEW-REQUIRED 분류, §6·§13의 "외부 법무 검토" 열 해당 시
   - 검토 결과는 본 문서 개정으로 반영 (구두 승인으로 종결하지 않음)
7. 승인
   - blocking reviewer 전원 Pass 시 승인 기록
8. 배포
9. 회귀 사례 등록 검토
   - Fail 이력이 있었던 표현 유형은 §16의 모니터링 대상으로 등록
   - 유형이 AI 응답에도 해당하면 red-team candidate 제안으로 연결
     (승격은 red-team-cases §16 Promotion Gate 경유 — 자동 승격 금지,
      canonical fixture 변경 아님)
```

- 본 workflow는 **canonical 문서(서비스 정책 §11~§12)의 목록을 완화할 수 없다** (R-COPY-07). 금지 목록의 완화는 canonical 개정 절차의 소관이며 본 가이드 범위 밖이다. 본 가이드가 할 수 있는 것은 **추가 제한**뿐이다.

---

## 16. Downstream Requirements

`finance-ops-monitoring-guide.md`로 넘기는 요구사항.

```text
OUT-OMG-C-01  금지 카피 노출 모니터링 — 배포된 화면·알림·마케팅 표면에서
              §3·§13.1 계열 표현의 정기 스캔 절차 (AI 응답은 validator
              소관 — 여기서는 응답 외 표면)
OUT-OMG-C-02  고지 누락 감시 — REQUIRED 고지(§5.1 ③④⑤)의 화면별 존재
              검증을 배포 검증 항목화
OUT-OMG-C-03  CTA 회귀 감시 — 신규 배포에서 §7 금지 CTA 계열 등장 여부
              점검 (§15 workflow 우회 배포의 탐지)
OUT-OMG-C-04  데이터 기준시각 누락 감시 — Phase 1.5+ 수치 표시에 출처/
              기준시각/지연 라벨 누락 탐지 (user-data-policy R-IBH-07,
              `finance-legal-risk-review-notes.md` IN-OMG-02와 통합 운영)
OUT-OMG-C-05  refusal/fallback 문구 품질 — §12 상태 문구의 노출 빈도와
              내부 정보 비노출 검증 (B-09 위반 스캔)
OUT-OMG-C-06  마케팅 카피 승인 이력 — §15의 승인 기록 보관과 미승인
              집행 탐지 (LR-07 증빙)
```

---

## 17. Open Decisions

| ID | 이슈 | 현재 근거 | 선택지 | 권장 방향 `[제안]` | owner | blocking | 외부 법무 검토 |
|---|---|---|---|---|---|:---:|:---:|
| OQ-C-01 | 고지 최종 문구의 확정 주체·시점 | §5.2 — 방향 예시만 존재 | (a) future External Legal Review 후 일괄 확정 (b) 내부 확정 후 사후 검토 | (a) — MVP 공개 전 (`finance-legal-risk-review-notes.md` §10 blocking 항목과 연동) | Product Owner + future External Legal Review | **Yes** | **필요** |
| OQ-C-02 | "맞춤/개인화" 어휘의 사용 여부 | §9 — LEGAL-REVIEW-REQUIRED | (a) 검토 후 제한 사용 (b) 전면 미사용 유지 | 검토 전 (b), 검토 결과로 재판정 | Product Owner + future External Legal Review | Yes (해당 카피에 한함) | **필요** |
| OQ-C-03 | "분석" 어휘의 서비스 내 사용 | §9 — CAUTION | (a) 전면 미사용 (b) 일반명사 최소 사용 | (b) — 단 "AI 분석" 조합은 금지 유지 | Product Owner + Professional Standards Owner | No | 권장 |
| OQ-C-04 | 체크 완료 수의 표시 방식 | §9 "점수" 금지, `finance-legal-risk-review-notes.md` §5.2 | (a) 미표시 (b) 수량 사실만 표시 ("4개 항목 확인") | (b) — 판정·색상 등급 없이 | Product Owner + Professional Standards Owner | No | 불필요 |
| OQ-C-05 | 알림 문구 기준의 확정 시점 | §6 알림 행 — 기능 미존재 | (a) 기능 gate와 동시 확정 (b) 선행 확정 | (a) — user-data-policy §15 gate와 동기화 | Product Owner + future External Legal Review | Yes (알림 도입 시) | **필요** |
| OQ-C-06 | 거절 화면 문구의 어조 (사과형 vs 안내형) | §12 | (a) 사과형 (b) 중립 안내형 | (b) — 거절은 오류가 아니라 서비스 정체성. 사과형은 "원래는 해줘야 하는데 못 해준다"는 인상 | Product Owner + Professional Standards Owner | No | 불필요 |
| OQ-C-07 | 카피 가이드와 output-examples의 어휘 정합 점검 주기 | §2.1 역할 경계 — 응답은 templates 소유 | (a) 분기별 상호 점검 (b) 변경 시에만 | (a) — 화면 어휘와 응답 어휘의 괴리 방지 | Product Owner + Professional Standards Owner | No | 불필요 |

---

## 18. 본 문서가 하지 않는 것

| 책임 | 담당 |
|---|---|
| AI 응답 내부의 구조·거절 문구 | `finance-prompt-templates.md` `[canonical]` |
| 응답의 금지 표현 검사 | `finance-output-validator.md` `[canonical]` |
| 서비스 금지선·CTA/마케팅 금지 목록의 원 정의 | `00-service-policy.md` `[canonical]` — 본 가이드는 전개만 |
| 데이터 수집·이용·삭제 정책의 실체 | `finance-user-data-policy.md` |
| 카피 모니터링의 운영 절차 | `finance-ops-monitoring-guide.md` |
| 처리방침·약관·고지의 법률 최종 문구 | future External Legal Review |
| 화면 레이아웃·비주얼 설계 | Product Owner |
