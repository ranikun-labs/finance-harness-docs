# finance-harness-mvp-implementation-handoff-v1.2.md

> **상태**: v1.2 (문서 기반, repo 미확인 — `finance-output-validator.md` 반영 개정)
> **internal-only**: 본 문서는 내부 구현·검증 전용이다. 사용자 대면 정적 리소스나 공개 문서로 노출하지 않는다.
> **수신자**: 금융 하네스 MVP를 구현할 다음 개발 세션 (Claude Code / Codex)
> **최우선 지시**: 본 문서는 문서 기반 구현 계획이다. **어떤 문서도 구현 완료를 의미하지 않으며**, 코드 작업 전 repo 실사가 선행되어야 한다.

---

## 1. 현재 Source of Truth

| 문서 | 역할 |
|---|---|
| `00-service-policy.md` | 최상위 서비스 정책과 금지 원칙 |
| `finance-lens-catalog.md` | finance lens 정의 (10개, 목적/체크리스트/금지 표현) |
| `finance-policyguard-rules.md` | intent 분류(12개)와 guard 처리 규칙 |
| `finance-routing-fixtures-v2.md` | routing/guard regression 기준 (32개 fixture, A안 반영) |
| `finance-prompt-templates-v2.1.md` | intent/action별 응답 템플릿 |
| `finance-market-data-grounding.md` | 시장 데이터 단정 금지와 Phase 1 데이터 경계 |
| `finance-output-validator.md` | **LLM 생성 답변의 최종 검사, rewrite, regression 기준** |
| `finance-harness-reuse-map.md` | 개발 하네스 패턴 재사용 지도 |

충돌 시 우선순위: **`00-service-policy.md` > policyguard > 나머지**. 금지 정책과 다른 문서가 충돌하면 정책이 이긴다.

> ✅ **결정 완료 (A안, 유지)**: RISK_BUY_SELL → `REDIRECT_TO_CHECKLIST` (체크리스트 전환형 — 직접 매수/매도/보유 판단은 차단하고, **판단 거절 선언 후 체크리스트로 전환**). `REFUSE_AND_REDIRECT`는 직접 금지 항목(목표가/손절가/수량/비중/종목추천/계좌 기반) 전용. 문서·fixture·템플릿 매핑 일치 상태.

---

## 2. MVP 한 줄 정의

**사용자의 투자 질문을 매수/매도 판단이 아니라 체크리스트와 기록/복기 루프로 전환하는 AI 금융 하네스.**

핵심 루프: `질문 → 체크리스트 → 기록 → 복기`. 원칙: **판단하지 않고, 기록하게 한다.**

---

## 3. MVP에 포함할 것

| 기능 | 기준 문서 |
|---|---|
| finance lens routing | lens-catalog |
| Finance PolicyGuard (Input/Generation/Output 3단) | policyguard-rules |
| prompt template selection | prompt-templates §14 |
| routing fixture validation | routing-fixtures-v2 |
| market data grounding policy | market-data-grounding |
| journal/review 연결 (기록 저장 + 복기 질문 생성) | 서비스 정책 §7~8, lens-catalog `journal-review` |
| **Output Validator (최종 검사 + rewrite/폴백)** | **output-validator** |
| risk log 최소 구조 | policyguard §10, output-validator §14 |

---

## 4. MVP에서 제외할 것

```text
매수/매도 판단          목표가              손절가
수량/비중 제안          종목추천            계좌연동
자동매매               OCR                실시간 시세 판단
포트폴리오 리밸런싱 조언  수익률 개선/손실 방지 약속
```

- 제외는 "나중에 추가"가 아니라 **정책상 금지**(앞 6개 판단 계열)와 **Phase 1 범위 외**(연동/데이터 계열)로 구분된다. 판단 계열은 어떤 Phase에서도 추가하지 않는다.

---

## 5. 권장 처리 흐름

```text
User Question
  ↓
Input Guard                 (위험 신호 탐지 — policyguard §7)
  ↓
Risk Intent Classification  (12 intent, 최위험 우선 — policyguard §3)
  ↓
┌────────────────────────────────┐
│ RISK_* → Safe Redirect 경로     │
│ SAFE_* → Lens Routing          │
└────────────────────────────────┘
  ↓
Prompt Template Selection   (intent/action → 템플릿 — templates §14)
  ↓
LLM Generation              (Generation Guard 주입 — policyguard §8)
  ↓
Output Validator            (사용자 노출 전 마지막 안전 계층 — output-validator)
  ↓
Safe Answer or Rewrite      (rewrite도 Validator 재통과 필수)
  ↓
Journal Save or Review CTA  (기록 루프 연결)
```

**불변 규칙 2가지:**

- 위험 intent는 lens를 직행하지 않는다. 거절/안내 선언이 먼저다.
- **어떤 응답도 Output Validator를 통과하지 않고 사용자에게 노출되지 않는다.**

---

## 6. Spring Boot 구현 단위 제안

> 코드가 아닌 **책임 정의**다. 실제 클래스/패키지 구조는 repo 컨벤션에 맞춘다.

| 컴포넌트 | 책임 | 입력 | 출력 | 참조 문서 | 주의사항 |
|---|---|---|---|---|---|
| `QuestionController` | 질문 접수 API 진입점 | 사용자 질문 | 안전 응답 | — | 컨트롤러에 정책 로직 두지 않음 |
| `FinanceGuardService` | Input Guard + intent 분류 | 질문 원문 | intent + action | policyguard §3, §7 | 키워드 매칭 단독 분류 금지, 최위험 우선 |
| `FinanceLensRouter` | intent/질문 → lens 목록 | intent, 질문 | lens 목록 | lens-catalog, fixtures-v2 | fixture에 없는 lens 이름 생성 금지 |
| `PromptTemplateService` | action → 템플릿 선택/조립 | intent, action, lens | LLM 프롬프트 | templates-v2.1 §14 | Generation Guard 원칙 항상 주입. **확률적 제어일 뿐 최종 안전은 Validator 책임** |
| `LlmGateway` | LLM 호출 추상화 | 프롬프트 | 원시 응답 | — | 원시 응답을 직접 노출 금지 |
| `OutputValidatorService` | **LLM 응답 최종 검사** (결정적 검사 계층) | question_id, detected_intent, expected_action, generated_answer | safe_answer 또는 rewrite_action | **output-validator** (검사 카테고리 §3, severity §4, rewrite §8) | 원문 답변 전문 로그 저장 최소화 · 차단된 판단성 문장을 조언 이력처럼 저장 금지 · **CRITICAL 위반은 사용자에게 노출 금지** · 스트리밍 응답은 Validator 우회 가능성이 있으므로 MVP에서는 주의 |
| `JournalService` | 기록 저장/복기 질문 생성 | 사용자 기록 | 저장 결과, 회고 질문 | 서비스 정책 §7~8, output-validator §12 | 기록 기반 매매 결론 생성 금지 |
| `RiskLogService` | guard/validator 이벤트 로그 | guard/validator 결과 | 로그 저장 | policyguard §10, output-validator §14 | 원문/가격/수량 최소화 |
| `FixtureTest` | 32개 fixture regression | fixture 정의 | pass/fail | routing-fixtures-v2, output-validator §15 | fixture 없이 배포 금지 |

---

## 7. 최소 도메인 모델

| 엔티티 | 목적 | 주요 필드 후보 | 저장 금지 | 민감정보 주의 |
|---|---|---|---|---|
| `User` | 계정 | id, email, password(해싱) | 증권사 API key, 계좌정보 | 비밀번호 BCrypt/Argon2 |
| `Question` | 질문 이력 | id, userId, text, detectedIntent, createdAt | — | 원문은 사용자 소유 데이터로 접근통제 |
| `ChecklistAnswer` | 생성된 체크리스트 응답 | id, questionId, appliedLenses, content | 매매 판단성 내용 (Validator가 차단) | — |
| `Journal` | 투자 기록/공부 노트 | id, userId, type(공부/투자), content, emotionTag, price(선택), quantity(선택) | 목표가/손절가/AI 추천 수량·비중 필드 자체를 두지 않음 | 투자성향 추론 가능 → 암호화/접근통제 검토, 삭제권 보장 |
| `Watchlist` | 관심종목 | id, userId, symbol, createdAt | 매수가/목표가 필드 금지 | — |
| `RiskLog` (PolicyViolationLog) | guard/validator 이벤트 | questionId, answerId, detectedIntent, detectedViolations, severity, guardAction/validatorAction, blockedTerms, rewriteType, outputShapeValid, disclaimerPresent, createdAt | 질문/답변 원문, 가격/수량 | 디버깅/정책 개선 목적 한정. **조언 이력처럼 보이지 않게 표현 키 수준만 기록** |

- **스키마 수준 방어**: 금지 개념(목표가, 추천 수량)은 컬럼 자체를 만들지 않는 것이 최선의 Guard다.

---

## 8. API 후보

| API | 목적 | Guard | LLM | 저장 |
|---|---|:---:|:---:|:---:|
| `POST /api/questions` | 질문 접수 → 체크리스트 응답 | ✅ (전체 흐름) | ✅ | Question + ChecklistAnswer |
| `POST /api/journals` | 기록/공부 노트 저장 | ⚠️ (입력값 라벨링만) | ❌ | Journal |
| `GET /api/journals` | 기록 목록 | ❌ | ❌ | 조회 |
| `GET /api/journals/{id}` | 기록 상세 | ❌ | ❌ | 조회 |
| `POST /api/questions/{id}/review` | 기록 기반 복기 질문 생성 | ✅ (RISK_ACCOUNT_BASED_ADVICE 경계) | ✅ | 복기 결과 |
| `GET /api/watchlist` | 관심종목 조회 | ❌ | ❌ | 조회 |
| `POST /api/watchlist` | 관심종목 등록 | ❌ | ❌ | Watchlist |

- LLM 호출이 있는 API(`questions`, `review`)는 **Output Validator 통과 없이 응답을 반환할 수 없다.**
- `review`는 복기 질문 생성까지만 — 기록 기반 매도/보유 결론(RF-10)은 Validator 차단 대상 (output-validator §12).

---

## 9. Fixture Test 우선순위

| 순위 | 대상 | fixture | 이유 |
|:---:|---|---|---|
| 1 | Risk 질문 차단/redirect | RF-01~10 | 정책 위반 시 법적 리스크 직결 — 최우선 |
| 2 | Prompt injection 차단 | PI-01~05 | 우회 경로가 뚫리면 1번이 무력화됨 |
| 3 | Data grounding redirect | DG-01~05 | Phase 1 사실 단정 방지 |
| 4 | Safe checklist routing | SF-01~07 | 제품 핵심 가치(체크리스트 품질) 검증 |
| 5 | Ambiguous clarification | AM-01~05 | 경계 케이스 정밀도 |

- Regression 실패 기준은 `finance-routing-fixtures-v2.md` §10과 `finance-output-validator.md` §15의 **합집합**을 적용한다.

---

## 10. 구현 PR 순서 (권장)

> ⚠️ **repo 상태 확인 전에는 확정 PR로 간주하지 않는다.** 실사 결과에 따라 순서/범위 조정 가능한 **권장 순서**다.

```text
PR 1 → PR 2 → PR 3 → PR 4 → PR 5 → PR 6 → PR 7 → PR 8
```

| PR | 목표 | 수정 예상 영역 | 검증 방법 | 하지 말 것 |
|---|---|---|---|---|
| **PR 1. 문서 반영** | 문서 8종(validator 포함)을 repo `docs/`에 반영 | docs/ | 문서 간 이름 정합성 교차 확인 | 코드 수정 |
| **PR 2. Intent 분류 + PolicyGuard skeleton** | 12 intent 분류 + Input Guard 골격 | FinanceGuardService | RF/PI 계열 intent 분류 정확성 | 분류 없이 LLM 직행 경로 |
| **PR 3. Lens routing** | intent → lens 매핑 | FinanceLensRouter | SF 계열 expected_lenses 일치 | 신규 lens 이름 생성 |
| **PR 4. Template selection** | action → 템플릿 조립 + Generation Guard 주입 | PromptTemplateService | 템플릿 매핑표 전수 확인 | 템플릿 밖 자유 생성 허용 |
| **PR 5. Fixture tests** | 32개 fixture regression 구현 | FixtureTest | §9 우선순위대로 통과 | fixture 임의 완화/삭제 |
| **PR 6. Output Validator** | output-validator 기준 최종 검사/rewrite/폴백 구현 | OutputValidatorService | validator §15 실패 기준 + MVP 최소 검사(§16) 통과 | Validator 우회 경로, CRITICAL 노출 |
| **PR 7. Journal/Review 연결** | 기록 저장 + 복기 질문 생성 | JournalService, API | RF-10/AM-05 경계 + validator §12 검증 | 기록 기반 행동 지시 생성 |
| **PR 8. Market data grounding guard** | RISK_DATA_GROUNDING 처리 + 입력값 라벨링 | Guard/Template/Validator 확장 | DG 계열 + validator §10 통과 | 출처 없는 현재형 단정 허용 |

- PR 5(fixture) → PR 6(validator)이 PR 7~8보다 앞선다: **검증·검사 기반 없이 기능을 확장하지 않는다.**
- PR 6 이전에는 어떤 LLM 응답도 사용자-facing 환경에 노출하지 않는다 (개발 환경 한정 확인만).

---

## 11. Output Guard / Validator 처리

**MVP Output Guard의 source of truth는 `finance-output-validator.md`다.** PolicyGuard의 Output Guard(3단 중 마지막)는 해당 문서의 검사 카테고리(§3), severity(§4), rewrite 정책(§8)으로 구체화되었다.

핵심 원칙:

- **LLM 응답은 사용자 노출 전 반드시 Output Validator를 통과해야 한다.**
- Prompt Template은 **확률적 제어**이고, Output Validator는 **결정적 검사 계층**이다. 템플릿 준수를 신뢰하고 검사를 생략할 수 없다.

MVP 최소 검사 (output-validator §16):

- [ ] 금지 표현 검사 (거절 문맥 지칭은 오탐 예외 — validator §6)
- [ ] 직접 매수/매도/보유/정리 행동 지시 검사
- [ ] 목표가/손절가/수량/비중 검사
- [ ] 종목추천 검사
- [ ] 데이터 단정 검사 (validator §10)
- [ ] 투자 권유 아님 고지 검사
- [ ] 체크리스트 구조 검사 (validator §9)
- [ ] prompt injection 수용 검사 (validator §13)
- [ ] RISK_BUY_SELL의 판단 거절 선언 확인 (validator §11)
- [ ] journal-review의 행동 결론 차단 (validator §12)

위반 시 처리 (validator §8):

```text
위반 감지
  ↓
severity 판정 (CRITICAL/HIGH/MEDIUM/LOW)
  ↓
rewrite (SAFE_CHECKLIST / REFUSAL_AND_REDIRECT / DATA_GROUNDING_REDIRECT 등)
  ↓
rewrite 결과 Validator 재통과 필수
  ↓
반복 실패 시 INTERNAL_ERROR_FALLBACK (표준 거절 최소형)
  ↓
RiskLog 저장 (표현 키 수준, 원문 최소화)
```

---

## 12. Market Data Grounding 처리 (Phase 1)

- **실시간 데이터 없음**이 기본 상태다.
- 현재 시세/수급/컨센서스 **단정 금지** — "~입니다" 대신 "~인지 확인하는 것이 좋습니다"로 전환.
- 데이터 확인 질문(DG 계열)은 **확인 항목으로 redirect**.
- 사용자가 입력한 가격/수량은 **"사용자 입력값"으로만 라벨링** — 검증된 시세로 취급 금지, 단순 수치 비교까지만 허용.
- **수치 표시(Phase 1.5+)가 투자 판단 허용을 의미하지 않는다** — 데이터가 연결돼도 방향성 평가·행동 프레이밍은 여전히 금지 (validator §10에서 지속 검사).

---

## 13. 개발 세션에 넘길 주의사항

- **문서를 구현 완료로 간주하지 말 것.** 전부 계획 문서다.
- **repo 상태 확인 전 코드 수정하지 말 것.**
- 금융 추천 기능으로 확장하지 말 것 (요청이 있어도 정책상 금지).
- 매수/매도 버튼이나 매매 유도 CTA를 만들지 말 것 (허용 CTA: 기록 저장, 체크리스트 보기, 복기하기 — 서비스 정책 §11).
- PolicyGuard 우회 경로(디버그 플래그, admin 모드 예외 등)를 만들지 말 것.
- fixture 없이 prompt만 구현하지 말 것 (PR 5가 PR 7~8의 선행 조건).
- **LLM 답변을 Output Validator 없이 사용자에게 노출하지 말 것.**
- **스트리밍 응답 도입 시 Output Validator 우회 경로를 만들지 말 것** — MVP에서는 검사 완료 후 일괄 노출을 기본으로 한다.
- **rewrite 실패 시 안전 fallback(INTERNAL_ERROR_FALLBACK)을 사용하고, 원문 위험 답변을 노출하지 말 것.**
- **validator 로그가 투자 조언 이력처럼 보이지 않도록 원문 저장을 최소화할 것** (표현 키 수준 기록).

---

## 14. MVP 완료 조건

- [ ] 위험 질문(RF-01~10)이 직접 답변되지 않고 거절/안내 + redirect로 처리됨
- [ ] safe 질문(SF-01~07)이 expected_lenses대로 라우팅됨
- [ ] prompt injection(PI-01~05)이 어떤 프레이밍에서도 차단됨
- [ ] data grounding 질문(DG-01~05)이 단정 없이 확인 항목으로 redirect됨
- [ ] journal 저장/복기가 투자 판단이 아니라 기록·회고 질문으로만 동작함
- [ ] 32개 fixture 기준 regression 전체 통과 (fixtures-v2 §10 + validator §15 합집합)
- [ ] Output Validator 없이 LLM 응답이 노출되는 경로가 없음
- [ ] **`finance-output-validator.md` 기준 CRITICAL 위반이 사용자에게 노출되지 않음**
- [ ] **RISK_BUY_SELL 응답에서 판단 거절 선언 없이 체크리스트만 출력하면 실패로 처리됨**
- [ ] **목표가/손절가/수량/비중/종목추천이 출력되지 않음**
- [ ] **데이터 출처 없는 현재 시세/수급/컨센서스 단정이 출력되지 않음**
- [ ] **journal-review가 매수/매도 행동 결론으로 끝나지 않음**
- [ ] **prompt injection 수용 흔적이 출력되지 않음**
- [ ] RiskLog가 원문/민감정보 최소화 원칙으로 저장됨
- [ ] RISK_BUY_SELL = `REDIRECT_TO_CHECKLIST`, 직접 금지 항목 = `REFUSE_AND_REDIRECT` 매핑이 문서·fixture·구현에서 일치 유지됨 (A안 확정)

---

## 15. v1.2 변경 요약

| 변경 | 내용 |
|---|---|
| Source of truth 갱신 | `finance-output-validator.md` 추가 (8종 체계), fixtures-v2 / templates-v2.1 버전 표기 반영 |
| §11 전면 개정 | "별도 validator 문서 없음" 문구 제거 → **`finance-output-validator.md`를 Output Guard의 source of truth로 확정**. 최소 검사 10개 항목, severity 기반 rewrite/폴백 흐름 반영 |
| 처리 흐름 갱신 | Output Validator를 "사용자 노출 전 마지막 안전 계층"으로 명시, Safe Answer or Rewrite 단계 추가 |
| 구현 단위 갱신 | `OutputGuardService` → `OutputValidatorService`로 명명 정리, 입출력/참조/주의사항을 validator 문서 기준으로 구체화 (스트리밍 우회 주의 포함) |
| PR 순서 갱신 | PR 6에 Output Validator 단독 단계 신설 (총 8개 PR), "권장 순서"로 명시 |
| 완료 조건 갱신 | validator 기준 6개 조건 추가 (CRITICAL 미노출, 거절 선언, 데이터 단정, journal 결론, injection 수용 등) |
| 정책 유지 | **RISK_BUY_SELL A안 기준 유지** — `REDIRECT_TO_CHECKLIST`, 직접 판단 차단, 거절 선언 후 체크리스트 전환 |
