# 05 구현 인계 계획 (작은 PR 단위) + Pilot A 추천

전제: 구현은 **Writer**가 한다. 이 문서는 범위·AC·검증·순서만 정한다. 사실 기준은 `origin/dev` 89ca509 + PR #49(0409274). 실행·렌더·CI 검증은 이 단계에서 전부 **NOT_RUN**.
용어: 03 문서의 화면 ID(H1 등), 02 문서의 횡단 항목(X1 등), 04 문서의 컴포넌트 우선순위(P0/P1).

## 1. Pilot A 추천

**추천: H1 검토 시작(`/app`)**

| 판단 근거 | 내용 |
|---|---|
| 목적 적합 | Primary Surface, 제품의 첫 인상, 단일 task("질문 입력")라 위계 규칙(Key 1개, 진한 버튼 1개)을 가장 순수하게 검증 |
| Foundation 커버 | page-title·TextField(X5 해결 첫 사례)·SectionHeader·ListRow·PolicyNotice·EmptyState·Button strong/text·Card 사용 → 새 공통 컴포넌트(C1) 실사용 검증 |
| 위험 낮음 | Guest 접근 가능(Auth 의존 없음), 보호 계약(`JournalCreate*`, discriminator) 무관, 서버 호출 없음(질문은 메모리), 영향 파일 `HomePage.tsx`+i18n 거의 없음 |
| 검증 용이 | 390/768/1280 3장 + Light/Dark로 시각 합격 판단, 기존 `HomePage.test.tsx`(236줄) 보존 가능 |
| 후속 파급 | TextField·Notice·ListRow가 H2·J2·J4·A1이 그대로 재사용 |

**버리는 것(트레이드오프)**: H1은 InfoRow·Dialog·subtle "복기하기"·SelectChip을 쓰지 않아 Foundation의 **핵심 차별점(핵심 한 곳만 강조·복기 subtle)** 을 완전히 보여주지 못한다. 그 증명은 Pilot B(J3 저널 상세)가 맡는다.

**대안 비교**
- J3 저널 상세: Foundation 시안 1순위 화면이고 InfoRow·Card inset·subtle 복기하기·Dialog가 모두 나오지만, 수정·삭제(계약 근거 없음 X16)와 #46 3파일 중복, Decision Context snapshot 조건이 겹쳐 **Pilot으로는 변수가 많다**. → Pilot B.
- H2 검토 결과: 가장 큰 화면(631줄)·CTA 우선순위 역전·칩 위치 미결정(Q1). 위험 큼.
- A1 Auth Entry: 작고 Auth 화면이지만 provider 미확정·참조 로그인 비운영 폼 혼재로 대표성 부족.

**Pilot A의 합격 기준(= 전체 롤아웃 Go/No-Go)**: 아래 §4 공통 AC + H1 전용 AC 전부 통과 + Owner 시각 승인(390·768·1280 × Light·Dark 6장).

## 2. PR 순서

의존: `#49 병합 → PR-1 → PR-2(Pilot A) → PR-3(Pilot B) → …`. 각 PR은 **한 목적**이고 revert 단독 가능(데이터 마이그레이션 없음). i18n·테스트·문서(`docs/nav-map.md`는 route 불변이면 수정 불필요)는 같은 PR에 포함.

| PR | 이름 | 범위 | 선행 |
|---|---|---|---|
| PR-0 | 선결 | #49 Draft 해제·병합(Owner/Writer) · #46 처리 결정(**흡수 후 닫기 추천**, 02 §3) · Owner 질의 Q1~Q7 답변 | — |
| PR-1 | 공통 입력·안내 | TextField/Textarea/FieldMessage·Notice 신규, PolicyNotice·EmptyState·PageHeader 재정렬(04 §3.1~3.3) | PR-0 |
| **PR-2** | **Pilot A: H1 검토 시작** | HomePage + i18n(필요 시) | PR-1 |
| PR-3 | Pilot B: J3 저널 상세 | JournalDetailPage(읽기 영역만) + `RecordTagText`(J3 한정 사용) + InfoList 메타 + reading 본문 + 복기하기 subtle + snapshot inset | PR-2 승인 |
| PR-4 | J4 복기 | JournalReviewPage·SavedRetrospectives, TextField·Disclosure·Notice, #46 의도 흡수 | PR-3 |
| PR-5 | J1 저널 목록 | JournalList·JournalRecordCard→ListRow, 필터 SelectChip, StateView | PR-3 |
| PR-6a | H2 검토 결과(구조) | AskPage: 헤더/섹션/Checkbox/CTA 우선순위 역전/StickyActionBar | PR-1, StickyActionBar |
| PR-6b | H2·J2a 선택 근거 칩 | SelectChip "함께 남기기" (위치는 Q1 답) | PR-6a, PR-7b |
| PR-7a | J2 진입 + J2a/b 폼 정렬 | JournalNewPage 진입 선택, 폼 필드 TextField화, ChoiceGroup | PR-1 |
| PR-7b | J2a Decision Context | 패널 위치(폼 아래)·Disclosure·rail(1280)·Checkbox/SelectChip | PR-7a |
| PR-8 | A1·O1·N1 | AuthEntryPage·OnboardingPage·NotFoundPage 정렬 | PR-1 |
| PR-9 | 카피 | "AI와 복기하기" → "복기하기" 등(Q3 승인 시) | Q3 |
| PR-10 | 수정·삭제 스타일 | JournalEditor·LifecycleControls(Q5 승인 시) | PR-3 |
| 보류 | 공개 랜딩 P1, 오프라인 Banner, 이탈 보호 Dialog(`useBlocker`) | 별도 계약·Owner 결정 | — |

권장 병렬: PR-3·PR-5·PR-8은 파일이 겹치지 않아 병렬 가능(`TabLayout`/`BottomNavigation`은 어떤 PR도 건드리지 않음 — 이미 3개 PR이 충돌한 전력이 있음, open-prs.md).

## 3. PR별 범위·AC·검증

### PR-1 공통 입력·안내 (P0)
- **범위**: `src/components/ui/text-field.tsx`·`field-message.tsx`·`notice.tsx` 신규(+test), `common/PolicyNotice·EmptyState·PageHeader` 재정렬. 화면 파일 변경 없음(PolicyNotice·EmptyState·PageHeader 사용처는 시각만 변경, props 호환 유지).
- **AC**:
  1. TextField: label↔input, `aria-invalid`, `aria-describedby`(hint+error), 오류 `role=alert`, 높이 48/반경 10/외곽선 `--input`/fill=card, 포커스 전역 스타일, 별도 ring 없음.
  2. Notice: tone 6종, 상태색은 `*-fg on *-bg` 4.5:1(Light·Dark) — `contrast.test.ts`에 쌍 추가.
  3. PolicyNotice·EmptyState에 `border`·`shadow`·`text-sm/text-xs` 없음(grep 가능).
  4. 기존 PolicyNotice·EmptyState·PageHeader 사용처 테스트 변경 없이 통과(props 호환), 변경이 필요하면 PR 설명에 이유 기재.
  5. 도메인 key·i18n을 컴포넌트가 알지 않는다.
- **검증**: `pnpm typecheck && pnpm lint && pnpm format:check && pnpm test && pnpm build`(= `pnpm verify`). 필요 시 `pnpm test:e2e`. 390/768/1280 Light·Dark 컴포넌트 스크린샷(Storybook 없음 → 임시 harness 또는 H1에서 대체) [NOT_RUN].
- **위험**: PolicyNotice destructive tone 사용처 존재 여부 미조사 → PR에서 grep 후 이관.

### PR-2 Pilot A: H1 검토 시작
- **범위**: `src/pages/HomePage.tsx`(+i18n·`HomePage.test.tsx`).
- **변경 요지**: eyebrow 삭제 · 헤더 page-title · 질문 TextField(textarea, helper·error) · 제출 strong 1개 · 예시 질문 = ListRow(text) · PolicyNotice inset · 최근 기록 = ListRow 2행(배지·막대 없음) · 빈 상태 = EmptyState(진한 버튼 없음) · 카드/테두리/그림자 0.
- **AC (H1 전용)**:
  1. 진한 버튼 DOM 1개(제출). 빈 상태에서도 strong 2개 동시 노출 없음.
  2. Key 영역 1(질문 입력). eyebrow/브랜드색 장식 0. 브랜드색 사용 = 제출 버튼·포커스·(선택 시) 뿐.
  3. `border`·`shadow-*`·`bg-background`(입력) 클래스 없음. 입력 fill = card.
  4. 본문 최소 14px(supporting) 이상, meta 13px 이상, 12px 이하 글자 없음.
  5. 빈 제출 → 필드 `aria-invalid` + `role=alert` + 포커스 이동(기존 동작 보존). Cmd/Ctrl+Enter 제출 보존.
  6. 예시 질문 클릭 = 기존과 동일하게 즉시 검토 시작(`holdTransient`/`ref` 흐름 불변).
  7. 390/768/1280에서 가로 스크롤 0, 터치 대상 ≥ 44, Light·Dark 모두 4.5:1(텍스트)·3:1(입력 외곽선).
  8. 추천·목표가·비중 등 금지 UI 0, 고지 문구 1문장 유지, 보류 항목(관심·보유 종목) 슬롯 0.
  9. 기존 `HomePage.test.tsx` 의미 보존(셀렉터 변경 시 role/name 기반으로).
- **검증**: `pnpm verify`; 390/768/1280 × Light·Dark 6장 + Owner 시각 승인; 키보드만으로 제출까지(Tab 순서·포커스 링 확인); 스크린리더 라벨 수동(VoiceOver) NOT_RUN 가능성 명시. 비교 이미지는 PR 본문에 첨부(Before=dev, After).
- **롤백**: 단일 PR revert. 영향 파일 1~2개.

### PR-3 Pilot B: J3 저널 상세
- **범위**: `JournalDetailPage.tsx`, `DecisionContextPanel.tsx`의 `DecisionContextSnapshotView`, `RecordTagText`(신규, J3만), 관련 i18n·테스트. **JournalEditor·LifecycleControls 파일은 건드리지 않음**(PR-10).
- **AC**: ① 진한 버튼 0, "복기하기" = subtle 1개, 복기 0건이면 복기 섹션 맨 위에 빈 문구+버튼 ② 본문 reading 16/27, `max-w 720`, `whitespace-pre-wrap`(줄바꿈 보존) ③ 메타 = InfoList(배지 0, FOMO·혼란 색 제거) ④ 카드 테두리·그림자 0, snapshot = inset ⑤ 상태 4종(로딩/없음/오류/성공) 문구·role 보존 ⑥ J1\|J3 2-pane CSS 보존 ⑦ COPY: "복기는 과거 평가가 아님" 맥락 유지, 평가형 표현 0.
- **검증**: `pnpm verify`; 기존 `JournalDetailPage.test.tsx` 통과; 390/768/1280 × Light·Dark; 감정 5종·행동 4종 모두 렌더 확인.

### PR-4 J4 복기
- #46에서 흡수: 구분선화(저장 목록 `border-t`), `details` → Disclosure, 입력 최소 높이, 간격. 버림: `ring-2` 재정의·`rounded-xl`·`text-sm`.
- **AC**: 입력 = TextField, 포커스 이중 표시 0, COPY ⑥ 고지(inset) 유지, 저장 strong 1개, 저장 중·실패·저장됨 상태·`retrospectiveDirty` 이탈 보호 동작 보존, 원본 불변 문구 유지, 2-pane CSS(`--muted` 의존 제거로 면 색 변화 확인).
- **검증**: `pnpm verify` + `JournalReviewPage.test.tsx`(485줄)·`SavedRetrospectives.test.tsx` 통과.

### PR-5 J1 목록 · PR-6a/6b H2 · PR-7a/7b J2 · PR-8 Auth/온보딩/NotFound
각 PR 공통 AC(§4) + 03 문서 해당 화면 표. 특이 AC만 기록한다.
- **PR-5**: "새 기록" vs 빈 상태 CTA 중복 제거(strong은 빈 상태에서만) · 401 StateView · 행에 배지 0 · 선택 행 `aria-current`.
- **PR-6a**: "판단 기록 남기기" strong / "학습 노트로 남기기" neutral(기존 반대) · 하단 고정 + `scroll-padding-bottom` · 체크리스트 Checkbox · 질문 key-value(좌 막대 삭제) · `Badge` = 상태(해석)만. 자동 저장·결과 복제 UI 없음. auth resume 테스트(`AskPage.test.tsx` 309줄) 보존.
- **PR-6b**: Q1 답 이후. 라벨 **고정 "함께 남기기"**(상태에 따라 안 바뀜), `aria-pressed`, 맥락 OFF 시 `aria-disabled`+`aria-describedby`, 사실 6개 이상 데이터로 반복 확인.
- **PR-7a**: Entry Choice Link 의미 유지, 폼 필드 TextField, ChoiceGroup, 기록 시점 접힘 유지, 검증 요약·첫 오류 포커스 보존, `JournalCreateCommand`/Request/Port·`create_failed`·`invalid_result`·discriminator **무변경**(diff에 해당 파일 0).
- **PR-7b**: Decision Context를 폼 아래로, 390 접힘, 1280 본문+rail(DOM 순서=시각 순서), 최소 맥락 4개는 묶음 표기(개별 해제 UI 없음).
- **PR-8**: Auth 문구는 UX-Δ §6 사용, provider 이름 하드코딩 0, 취소 → 원래 화면 보존, 성공 후 제목 포커스 유지.

## 4. 공통 AC (모든 화면 PR에 복사)

| # | 기준 | 확인 방법 |
|---|---|---|
| 1 | 진한 버튼(strong/danger-strong) ≤ 1/화면 상태 | DOM에서 `variant`·class 검사 + 시각 |
| 2 | 카드 테두리·그림자 0(Dialog 제외), 중첩은 inset만 | `grep -nE "border|shadow-"` 변경 파일 |
| 3 | 브랜드색 = 행동·선택·포커스만, 장식 막대·eyebrow 색 0 | 시각 + grep `border-l-primary` 등 |
| 4 | 배지는 상태에만, 메타는 텍스트 | 시각 |
| 5 | 타입 역할 사용(`type-*`), 12px 이하 글자 0, 사용자 글 reading 16/27 | grep `text-xs|text-sm|text-\[` |
| 6 | 입력 fill = card, 외곽선 `--input`, 개별 ring·`bg-background` 0 | grep + 시각 |
| 7 | WCAG 2.2 AA: 텍스트 4.5:1, 비텍스트 3:1, 터치 ≥ 44(2.5.8 최소 24는 하한), 포커스 가림 없음(2.4.11), 상태를 색만으로 전달 안 함(1.4.1) | contrast.test 확장 + 수동 |
| 8 | ko/en 문구 parity, 새 문구는 해요체·결과를 말하는 확인 문구, 금지 표현(COPY B-01~B-09) 0 | i18n diff |
| 9 | 390·768·1280 × Light·Dark 가로 스크롤 0, 의미 있는 정보 손실 0 | 스크린샷 6장 |
| 10 | 보호 계약 무변경: `JournalCreateCommand`·`JournalCreateRequest`·`JournalCreatePort`·`HttpJournalCreatePort`·`POST /finance/journals`·`create_failed/invalid_result`·`investment|study`·Production Create OFF·Primary IA(검토/저널)·`EMOTION_TAGS` 5종·route 문자열 하드코딩 금지 | diff 파일 목록 |
| 11 | 계약에 없는 기능·필드·문구·route 추가 0, 보류(#47·#48) 슬롯 0 | PR 설명 체크리스트 |
| 12 | 증거: Before(dev)/After 스크린샷, `pnpm verify` 결과, 미실행 항목 NOT_RUN 명시 | PR 본문 |

기본 검증 명령(`package.json` scripts, 원문 확인): `pnpm verify`(= prettier --check · eslint · tsc -b --noEmit · vitest run · build+prerender 검증), 필요 시 `pnpm test:e2e`. 실기기 Safari·VoiceOver·Capacitor는 별도 NOT_RUN.

## 5. 열린 질문 (Owner 확인, 추천 포함)

| ID | 질문 | 추천(나라면) |
|---|---|---|
| Q1 | "함께 남기기" 칩 위치: H2 사실 행 vs J2a 선택 근거 영역 | **J2a에 1차 적용**(상태가 이미 거기, UX-Δ §5의 "별도 선택 영역"과 일치, 재작업 최소). H2는 2차. 버리는 것: Foundation 시안과 동일한 H2 첫인상 |
| Q2 | Decision Context 접힘 기본값, 켜기 컨트롤을 native checkbox로 | Phone 기본 **접힘+요약 1줄**, 켜기는 native checkbox(§7.2) |
| Q3 | "AI와 복기하기" → "복기하기" | 변경(NAV 라벨·AI 관여 암시 제거). 카피 가이드 CAUTION 판정 필요 |
| Q4 | 1280 J2a 저장 위치(rail sticky vs 본문 끝) | 본문 끝(Tab 순서 단순), rail은 맥락만 |
| Q5 | 저널 수정·삭제·복원 UI의 계약 근거 | 근거 확인 전 **스타일만**, 흐름 변경·확장 금지 |
| Q6 | 비인증 H1의 "최근 기록(예시 데이터)" 유지 여부 | 유지하되 "예시" 라벨 필수, Auth required 저널과 혼동 방지. 대안: 삭제(위험 낮음) |
| Q7 | 공개 랜딩(P1) 디자인 시점 | 앱 Pilot 승인 후 별도 트랙 |

## 6. 이번 인계 문서가 모르는 것

- Foundation 원문(02-foundation·04-guidelines·05-specs·06-qa-checklist)과 Figma/시안 HTML, `design/claude-export/` 내용을 읽지 않았다. 값은 3개 지정 파일과 `ds/project/*.md` 요약 기준.
- K1 Key 영역 한도·Select Chip 세부 CSS·DTCG 파일은 NOT_VERIFIABLE.
- 모든 렌더·대비·접근성·테스트·CI는 NOT_RUN. PR #49의 CI·dev 병합 상태는 직접 확인하지 않았다(브랜치 HEAD 0409274만 확인).
