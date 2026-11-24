# 03 화면별 디자인 명세 (Foundation v1.0.0 적용)

범위: 01 인벤토리의 실제 존재 화면. 새 기능·새 route·새 필드를 만들지 않는다. 계약에 없는 것은 **[제안]**, 결정 필요는 **[Q#]**(05 문서 열린 질문).
시안 이미지·Figma는 만들지 않았다(이 단계는 명세). 렌더·대비·스크린샷 검증은 전부 **NOT_RUN**.

## 0. 공통 규칙 (모든 화면에 적용, 화면별 표에서는 반복하지 않는다)

### 0.1 값 (Foundation v1.0.0, token-baseline·SOL_REVIEW §5 기준)

| 구분 | Light | Dark |
|---|---|---|
| page / card / inset / control | #f2f4f6 / #ffffff / #f2f4f6 / #e9ecef | #0f1114 / #1a1d21 / #22262b / #2a2f35 |
| 글자 strong / body / secondary / tertiary | #191f28 / #333d4b / #4e5968 / #5b6673 | #f2f4f6 / #d1d6db / #b4bcc5 / #98a2ad |
| brand strong / deep / subtle (Finance) | #1d64d8 / #174fb0 / #e8f0fd | #4f95ff / #9cc2ff / #172a4d |
| on-strong | #ffffff | #0b1220 |
| 상태 fg (success/warning/danger/conflict/info) | #0a6b3b / #8a4a00 / #c42336 / #6b3fb4 / #2a4f86 | #5fd08f / #f0b44c / #ff8a95 / #b79cf0 / #8fb2ea |
| 입력 외곽선 `--input` | #78838f(3:1) | #7f8995 |

- 타입 역할: page-title 24/32, section-title 18/26, content-title 16/24, key-value 22/30, **reading 16/27(사용자가 쓴 글)**, body 15/24, supporting 14/20, meta 13/18, label 13/18(600). 최소 12px. `text-xs`/`text-sm` 직접 사용 대신 `type-*` 유틸.
- 간격: 섹션 사이 **40**, 섹션 제목→카드 **12**, 카드 padding **20**, 관련 항목 **8**. 컨트롤 높이 48, 터치 최소 44.
- 반경: badge 6 / control 10 / inset 12 / card 16 / sheet 24. 그림자는 Dialog 오버레이만.
- 강조 문법: **B** = 중립 면(card/inset)·secondary 글자·text/neutral 버튼. **C** = 화면의 Key 영역 1개(큰 타입 또는 reading 블록) + 진한 버튼 최대 1개 + 선택 상태(brand subtle).
- K1(Key 영역 수): 세는 것 = page-title·reading 블록·key-value·key-block, 세지 않는 것 = 버튼·메타. 화면당 **1개, 최대 2개**로 가정 [추정: 한도 원문 NOT_VERIFIABLE].
- 상태 배지는 상태에만(success/warning/danger/conflict/info/neutral+텍스트). 유형·행동·감정·날짜는 **텍스트(InfoRow/메타)**.
- 포커스: 전역 `:focus-visible` 2px brand + offset 2. 화면에서 `ring`/`outline-none` 재정의 금지.
- 상태 문구 3단: 무슨 일 / 내 입력·데이터는 어떻게 되나 / 다음 행동. 금지 표현(COPY B-01~B-09, "AI 질문" 포함)은 쓰지 않는다.
- Dark 공통 주의: ① 진한 버튼은 on-strong #0b1220 글자(흰 글자 3.71:1 미달 방지) ② 면 구분은 page→card→inset 명도 3단에만 의존하므로 카드 안 구분선(hairline `--border`)을 같이 쓴다 ③ danger는 solid가 #ff7a87+어두운 글자 ④ 이미지·아이콘을 흰색 고정으로 두지 않는다.

### 0.2 반응형 (요청 폭 390 / 768 / 1280)

| 폭 | 해당 계약 viewport | 내비 | 콘텐츠 |
|---|---|---|---|
| 390 | Phone 393 | H1·J1은 하단 2탭, 나머지는 context 내비(뒤로) | 단일 열, gutter 20, 하단 고정 Primary 허용(검토 결과·작성) |
| 768 | Tablet Portrait(834의 하위) | 상단 검토/저널 | 단일 열 readable(콘텐츠 호스트 660, 검토 결과 760), 480 cap 없음, gutter 24 |
| 1280 | Landscape 1194~Desktop 1360 사이 | 좌측 rail(≥1024 landscape/≥1280) | 최대 2 major surface. **승인 2-pane = J1\|J3, J4 원본\|복기**. 그 외는 단일 열 또는 본문+보조 열(Context Rail) |

1024 portrait는 상단 내비 유지(기존 CSS 보존). 3-pane·Review Question\|Result 강제 2-pane 금지. DOM 순서 = 시각 순서(UX-Δ §7.2).

### 0.3 공통 상태 어휘

| 상태 | 표현 | 컴포넌트 | 접근성 |
|---|---|---|---|
| 로딩 | 자리 유지 skeleton 또는 상태 문장 1줄, 3초 넘으면 보조 문구 | StateView(loading) [신규 04] | `role=status`, 포커스 이동 없음 |
| 빈 | 제목 + 다음 행동 1개 | EmptyState | 제목은 헤딩 아님(`p`) |
| 오류(복구 가능) | 아이콘+제목+설명+다시 시도 | ErrorState | `role=alert` |
| 비인증 | Auth Entry로 보내는 안내 + 계속 버튼 + 보존되는 것 명시 | StateView(auth) | 제목 포커스 |
| 저장 중 | 버튼 `aria-busy`·라벨 "저장 중", 입력 보존 | Button | `aria-live=polite` 1곳 |
| 저장 실패 | 폼 상단 Notice(danger) + 입력 보존 + 다시 시도 | Notice | `role=alert`, 첫 오류로 포커스 |
| 이탈 보호 | dirty일 때 Dialog(취소=초기 포커스, "나가기"=danger-text) | Dialog | ESC=취소 [구현은 X11처럼 후순위] |
| 오프라인 | 상단 Banner "연결이 끊겼어요. 쓰던 내용은 이 화면에 남아 있어요" + 저장 버튼은 `aria-disabled`+이유 연결 | Banner [제안, 계약 없음] | `role=status` |

---

## H1. 검토 시작 `/app`

| 항목 | 명세 |
|---|---|
| 계약 | NAV 검토 시작, UX-Δ §6(Guest 가능), GUARD(Home dashboard 금지·추천 금지) |
| 정보 위계 | **Key = 질문 입력**(1개). 그 아래 예시 질문 → 정책 고지(1문장) → 최근 기록(2줄, 예시 데이터) |
| B / C | B: 헤더(page-title 24 + 설명 supporting), 예시 목록, 고지(inset), 최근 기록 행. C: 질문 TextField 레이블 + **"검토 시작" 진한 버튼 1개**. eyebrow(`✦ … uppercase` 브랜드 글자) 삭제 |
| 구조 | PageHeader(제목만, 내비 탭 화면이므로 뒤로 없음) → Card[ Label(content-title) · TextField(textarea, helper) · FieldMessage(error) · Button strong 전폭(390)/우측 정렬(1280) ] → SectionHeader "이런 질문으로 시작해 보세요" + 목록 행 3(text 버튼, 한 행 56px 이상, 테두리 없음, divider) → PolicyNotice(inset) → SectionHeader "최근 기록" + InfoRow형 행 2 + "모두 보기"(text) |
| 사용 컴포넌트 | PageHeader, Card, TextField, FieldMessage, Button(strong/text/neutral), SectionHeader, ListRow [신규], PolicyNotice(Foundation 정렬), EmptyState |
| 390 | 단일 열, 제출 전폭 48px, 하단 고정 아님(입력이 키보드에 가려지므로 일반 흐름 + `scroll-padding-bottom`) |
| 768 | 읽기 폭 660 중앙, 제출 우측 정렬 자동 폭 |
| 1280 | rail + 본문 660. **본문 폭을 늘리지 않는다**(Trading terminal 금지). 우측 보조 열 없음 |
| Light/Dark | textarea fill = card(흰/#1a1d21), 외곽선 `--input`. Dark에서 placeholder = tertiary(4.5:1 확인 NOT_RUN) |
| 상태 | 빈 제출 오류(필드 아래 `role=alert` + `aria-invalid`, 포커스 이동) · 예시 질문 제출 직후 이동(로딩은 H2) · 최근 기록 없음 = EmptyState(진한 버튼 **쓰지 않음**: 이미 질문 제출 버튼이 있으므로 text 버튼으로 입력창 포커스) · 비인증 OK(Guest) · 오프라인 Banner [제안] · 저장 개념 없음(질문은 메모리) |
| 보류 | 관심·보유 종목 카드 **슬롯 없음**(#47·#48 보류) |
| 문구 근거 | 헤더 설명은 현행 i18n 유지, 신규 문구 없음 |

## H2. 검토 결과 `/app/ask?ref=`

| 항목 | 명세 |
|---|---|
| 계약 | UX-Δ §3.2(기록으로 이어가기 CTA 2개), §6(Guest, resume), §1(Review 결과는 transient, 자동 저장 금지), GUARD(확인형 문체), COPY ⑤(AI 생성 고지) |
| 정보 위계 | **Key = 내가 던진 질문**(key-value 22/30, 카드 없이 헤더 영역, 좌 막대 삭제) → 확인할 것(체크리스트) → 확인된 사실(출처·기준일) → 해석(“사실이 아니라 해석이에요” 상태 라벨) → 아직 모르는 것 → 시각·고지 → 기록으로 이어가기 |
| B / C | B: 4개 섹션 제목을 **카드 밖** SectionHeader, 사실·해석·모름 = 같은 Card 문법, 해석은 `info` 상태 라벨 1개. C: 질문 key-value, 하단 **"판단 기록 남기기" strong 1개**(계약 핵심 task). "학습 노트로 남기기"=neutral(또는 subtle). 현행처럼 학습 노트가 strong인 것은 **뒤집는다** |
| 구조(390) | PageHeader(뒤로 ←, "검토 결과") → 질문(key-value) → [partial이면 Notice(warning) "일부만 확인됐어요" + 영향 + 다음 행동] → SectionHeader+체크리스트(Checkbox ×n, 진행도는 텍스트 "2/4 확인", 배지 아님) → 사실 Card(InfoRow: 출처 stack·기준일 row) → 해석 Card(+상태 라벨) → 모르는 것 Card(태그는 텍스트, 배지 아님) → 시각·고지(PolicyNotice inset) → **하단 고정 영역**: 판단 기록 남기기(strong) + 학습 노트로 남기기(neutral), 다시 검토(text) |
| 칩 [Q1] | Select Chip "**함께 남기기**"(라벨 고정, `aria-pressed`, 켜짐=체크+brand subtle). **추천 위치 = 1차 J2a의 Decision Context 선택 근거 영역**(상태가 이미 거기 있음). H2 사실 행 칩은 Owner 승인 시 2차 |
| 사용 컴포넌트 | PageHeader, SectionHeader, Card, Checkbox, InfoRow, Badge(info 1개: "해석"), PolicyNotice, Button strong/neutral/text, StickyActionBar [신규], ErrorState, StateView |
| 390 | 단일 열 + 하단 고정 CTA(높이 ≥ 56+safe-area, `scroll-padding-bottom` ≥ 고정 높이: WCAG 2.4.11). CTA 2개는 세로(판단 기록 strong 위, 학습 노트 neutral 아래) |
| 768 | 단일 열 760, CTA는 본문 끝 일반 흐름(고정 해제) 또는 우측 정렬 |
| 1280 | rail + 본문 760 + (선택) 우측 sticky "기록으로 이어가기" 카드(최대 2 surface). Question\|Result 강제 2-pane 아님 |
| Light/Dark | 해석 Card는 면 색 그대로(brand 색 tint 금지), 상태 라벨만 info fg. 체크 채움은 `--primary`지만 Dark on-strong 어두운 글자 확인 |
| 상태 | **빈**(ref 없음/새로고침) = EmptyState "질문이 사라졌어요" + 검토 시작으로(text) · **로딩** = 질문 유지 + 3단계 진행(문장 목록, 현재 단계만 foreground) · **부분 결과** = warning Notice · **오류** = ErrorState(다시 시도 strong 1 + 질문 수정 neutral) + 질문은 그대로 · **비인증** CTA 선택 시 Auth Entry로(보존 안내 1문장: "검토 결과는 그대로 둘게요") · **오프라인** Banner + 결과는 메모리 유지 · 이탈 보호 없음(저장할 입력 없음) · 저장 중 없음 |
| 문구 | "질문과 확인 상태는 항상, 확인된 사실은 내가 고른 것만 기록에 붙어요. 검토 전체는 저장되지 않아요."(SOL_REVIEW A, Guard 3). 결과 문체는 확인형 유지 |

## O1. 온보딩 `/app/onboarding`

| 항목 | 명세 |
|---|---|
| 계약 | NAV 온보딩, COPY ①② REQUIRED(성격·비제공 고지). 동의 증적 아님(주석 유지) |
| 위계 | **Key = hero 한 문장**(page-title). "제공하는 것"(앞) → "제공하지 않는 것"(뒤)은 같은 무게, 고지는 inset 1문장 |
| B / C | B: 목록 2개(InfoList 문법, 아이콘 없음·구분선), 로고 칩 제거(텍스트 앱 이름). C: "시작하기" strong 1개 |
| 컴포넌트 | PageHeader(뒤로 없음), SectionHeader, Card, InfoList/InfoRow(stack), PolicyNotice, Button strong |
| 390/768/1280 | 단일 열 / 읽기 폭 600 중앙 / 동일(2열 금지) |
| Light/Dark | 제공 안 함 항목에 danger 색 사용 금지(중립), 체크/엑스 아이콘 + 텍스트 병행 |
| 상태 | 정적 화면. 오프라인 영향 없음. 비인증 OK(내비 없음) |

## J1. 저널 목록 `/app/journal`

| 항목 | 명세 |
|---|---|
| 계약 | UX-Δ §6(Auth required), GUARD(List\|Detail 2-pane 승인) |
| 위계 | **Key = 기록 목록 자체**(Key 영역 0~1). 헤더 page-title "저널" + 새 기록 |
| B / C | B: 행 = 제목(content-title) + 날짜·유형 meta 텍스트 + 질문 1줄(supporting, 이탤릭·막대 삭제), 배지 없음, 행 사이 divider, 선택 행 = brand subtle 면(색+굵기). C: "새 기록" **subtle 또는 strong 중 하나**: 목록이 있으면 neutral/text(핵심 행동은 기존 기록 읽기), **빈 상태에서만 strong 1개** → "새 기록"과 빈 상태 CTA 중복 제거 |
| 컴포넌트 | PageHeader, ListRow, SelectChip(필터, 서버필터 플래그 on일 때), EmptyState, ErrorState, StateView(auth), Button |
| 390 | 단일 열, 행 높이 ≥ 72(2줄), 탭 하단 |
| 768 | 단일 열 660, 상단 탭 |
| 1280 | **목록(0.38fr, min 300) \| 상세** 2-pane(기존 CSS 보존). 목록 선택 = 오른쪽 상세 교체, 키보드 ↑↓ 후보 [제안] |
| Light/Dark | 선택 행은 색만이 아니라 `aria-current` + 굵기. 필터 칩 켜짐 = 체크 아이콘 |
| 상태 | 로딩(행 skeleton 3) · 빈(전체): "아직 남긴 기록이 없어요" + 진한 1개 "기록 남기기" · 빈(필터): 필터 해제(text) · 오류 · **401**: StateView(auth, 이유 + 계속하려면 로그인) · 더 불러오기 로딩/오류(ErrorState inline) · 목록 끝 meta · 삭제됨 목록(접힌 섹션, 계약 외 기능 → 스타일만) |

## J2. 작성 진입 선택 `/app/journal/new`

| 항목 | 명세 |
|---|---|
| 계약 | UX-Δ §3.1(독립 surface, Link/Button, 에디터 안 persistent tab 금지) |
| 위계 | **Key = 선택 2개**(같은 무게) |
| B / C | B 전부. 선택지 = ChoiceCard(카드 전체가 Link, 제목+설명+chevron, 테두리 없음). C 없음(진한 버튼 0개 — 선택 자체가 행동) |
| 컴포넌트 | PageHeader(뒤로: 저널), Card(link), 비인증 안내 Notice(inset) |
| 390/768/1280 | 세로 2카드 / 가로 2카드 / 가로 2카드(최대 폭 660) |
| 상태 | 비인증 안내(`authNotice`/`authUnavailable`) 유지 · **잘못된 type**: ErrorState형 "기록 유형을 확인할 수 없어요" + 선택지 2개(text 버튼 2, strong 쓰지 않음) |

## J2a. 판단 기록 작성 `/app/journal/new?type=investment`

| 항목 | 명세 |
|---|---|
| 계약 | UX-Δ §4.1 순서: **대상 → 행동 → 이유 → 감정(선택) → 기록 시점 → Decision Context**, §5, §7.1, GUARD(행동 라벨은 기록용) |
| 위계 | **Key = 이유(Reasoning) textarea**(reading 16/27, 가장 큰 입력 면 min 160). 대상·행동은 짧은 입력 |
| B / C | B: 대상 TextField, 행동 = Radio 4(관심/관망/매수/매도, Select Chip 형태 허용·**동일 중립 스타일**, 매수/매도에 색 구분 금지), 감정 = Select Chip 5(선택 사항, 중립), 기록 시점 = InfoRow + "변경"(text, 접힘 유지). C: "저장" strong 1개(하단 고정 390) |
| Decision Context | 폼 **아래**로 이동(§4.1 6번). Card + 켜기 Checkbox/Switch(native 의미) + 최소 맥락 InfoList(질문·체크리스트 버전·확인 상태·capturedAt은 묶음, 개별 해제 불가 표기) + 선택 근거 영역(SelectChip "함께 남기기" ×n). OFF면 칩 `aria-disabled` + `aria-describedby`("검토 맥락 없이, 내가 쓴 내용만 기록에 남아요"). 390에서 **접힘**(disclosure, 기본 펼침 여부 [Q2]) |
| 구조 | PageHeader(뒤로 / review-derived면 "검토 결과로") → 제목(page-title) → [출처 inset: 원래 질문 + "검토 결과로 돌아가기"] → 폼 섹션들 → Decision Context → 저장 영역 |
| 컴포넌트 | PageHeader, TextField, Textarea, FieldMessage, RadioGroup/ChoiceGroup [정렬], SelectChip, Checkbox, InfoList, Disclosure [신규], Notice, StickyActionBar, Button strong/text, Dialog(이탈 보호 후순위) |
| 390 | 단일 열, 저장 하단 고정. 오류 요약(JournalValidationSummary)은 폼 상단 `role=alert` + 첫 오류 필드로 포커스 |
| 768 | 단일 열 660(480 cap 없음), 저장 우측 정렬 |
| 1280 | **본문(에디터) + Context Rail**(UX-Δ §7.1, 최대 2 surface): rail에 Decision Context·기록 시점. 저장은 rail 하단 sticky 또는 본문 끝 [Q4]. DOM 순서 = 에디터 → rail |
| Light/Dark | 필드 fill = card, 외곽선 `--input`. 선택 칩 켜짐 = brand subtle + 체크(Dark #172a4d / deep #9cc2ff) |
| 상태 | 입력 중(dirty) → 이탈 보호 · **검증 오류**(필드별 + 요약, `aria-invalid`/`describedby`) · **저장 중**(버튼 라벨 "저장 중", 필드 readonly 아님→입력 보존, 중복 제출 차단) · **저장 실패**(code별 문구 유지: invalid_request/invalid_result/idempotency_conflict/network_error/create_failed → Notice danger + "입력은 그대로예요" + 다시 시도) · **auth resume 후**: 제목 포커스 + status 문장 · **오프라인**: Banner + 저장 `aria-disabled` + 이유 · 비인증: AuthRequiredSurface(Auth Entry 이동) · 보호 계약(`JournalCreateCommand`·port·discriminator·Production Create OFF)은 시각 PR에서 변경 금지 |
| 문구 | "이 판단을 내릴 때 참고한 검토 맥락도 함께 남깁니다. 질문, 체크리스트 버전, 확인 상태와 기록 시점을 당시 상태 그대로 남깁니다. AI 검토 결과 전체를 저장하지는 않습니다."(UX-Δ §5) |

## J2b. 학습 노트 작성 `/app/journal/new?type=study`

J2a와 동일 문법. 차이만 기록한다.

| 항목 | 명세 |
|---|---|
| 계약 | UX-Δ §4.2: 무엇을 배웠나요? → 핵심 정리 → 더 확인할 것(선택) → 기록 시점 |
| Key | **핵심 정리 textarea**(reading). "무엇을 배웠나요?"는 짧은 TextField |
| 초안 | review-derived면 상단 inset status: "검토 결과에서 가져온 초안입니다. 저장하기 전에 자유롭게 수정하거나 지울 수 있습니다."(`role=status`, prefill 후 접근 가능한 알림) |
| 컴포넌트 | 더 확인할 것 = 항목 추가형 TextField 리스트(현행 기능 보존, 항목당 삭제 text 버튼) |
| 상태 | J2a와 동일. Decision Context 없음 |

## J3. 저널 상세 `/app/journal/:id`

| 항목 | 명세 |
|---|---|
| 계약 | NAV(저장된 원본 읽기 view), UX-Δ §5(snapshot, live 동기화 없음), 복기 진입 |
| 위계 | **Key = 이유/핵심 정리 본문**(reading 16/27, `max-w-[720px]`, 줄바꿈 보존). 헤더에 자산명(page-title)+행동(텍스트) |
| B / C | B: 메타 = InfoList(유형·기록 시점·시간대·행동·감정·생성/수정 일시, 값은 텍스트, 배지 없음), Decision Context snapshot = inset Card(읽기 전용, "당시 상태 그대로" meta). C: "**복기하기**" **subtle 1개**(진한 버튼 0개 — 목록 복귀·읽기가 주 흐름, 복기 0건이면 복기 섹션 맨 위에 빈 문구+subtle 버튼, SKILL_REVIEW 필수3) |
| 구조 | PageHeader(뒤로: 저널, 수정·삭제는 trailing 아이콘 버튼 라벨 있음 [제안: Foundation Pattern 12]) → 제목 → 본문 Card(reading) → (학습) 더 확인할 질문 리스트 → 메타 InfoList → 당시 검토 맥락(inset) → 복기 섹션(SectionHeader + 저장된 복기 요약 + subtle) |
| 컴포넌트 | PageHeader, Card, Card(inset), InfoList/InfoRow, SectionHeader, Button(subtle/text/danger-text), Dialog(삭제 확인, destructive: 초기 포커스=취소, `alertdialog`), ErrorState, StateView |
| 390 | 단일 열, 복기 subtle 전폭 아님(자동 폭, 좌측 정렬) |
| 768 | 단일 열 660 |
| 1280 | **J1\|J3 2-pane**(우측 상세, 폭 약 0.62fr). 상세 안에서 메타는 우측 보조 열로 분리 금지(2 surface 초과) |
| Light/Dark | 본문 Card = card 면, 메타/snapshot = inset. 감정 FOMO·혼란을 danger 색으로 칠하지 않는다(X6) |
| 상태 | 로딩(헤더 유지 + skeleton) · 없음(`journal_not_found`: EmptyState + 목록으로 text) · 오류(code별 3종 ErrorState) · 삭제 확인(Dialog) · 삭제됨(숨김·복원 문구: 현행 `retained` 기능 보존) · **수정 중**: 인라인 폼으로 전환(TextField 재사용), 충돌(`conflict`)=Notice(warning) + "내 입력은 남아 있어요" + 최신 불러오기, 이탈 보호 · 저장 중/실패 = J2a 규칙 |
| 계약 외 | 수정·삭제·복원은 조항 없음(01 §3). **스타일만 적용, 새 흐름 금지, Owner 확인 [Q5]** |

## J4. 복기 `/app/journal/:id/review`

| 항목 | 명세 |
|---|---|
| 계약 | NAV·GUARD(원본 불변, Original\|Retrospective 2-pane), COPY ⑥ 고지 REQUIRED, UX-Δ(복기는 원본을 덮어쓰지 않음) |
| 위계 | **Key = 복기 본문 textarea**(reading). 원본은 읽기 맥락(보조) |
| B / C | B: 원본 요약(inset Card, "원본은 그대로예요" meta), 선택 항목 `Disclosure`("결과와 판단 과정 돌아보기(선택)", 테두리 상자 아님: #46의 border-y 의도 흡수), 저장된 복기 목록 = 행 + divider. C: "복기 저장" strong 1개 |
| 구조 | PageHeader(뒤로: 상세) → PolicyNotice(COPY ⑥ "복기는 과거 평가·행동 지시가 아님", inset) → 원본 요약 → 복기 입력(TextField ×n) → 저장 → 저장된 복기(최신순) |
| 컴포넌트 | PageHeader, Card/inset, PolicyNotice, Textarea, Disclosure, Button strong, ErrorState, Notice |
| 390 | 단일 열: 원본 요약 → 입력 → 저장 → 목록. 저장 하단 고정 가능 |
| 768 | 단일 열 660 |
| 1280 | **원본(좌) \| 복기 입력(우)** 2-pane(기존 CSS 보존, 각 pane 독립 스크롤 없음→main이 스크롤 소유). 저장된 복기 목록은 우측 pane 하단 |
| Light/Dark | 원본 pane 배경 = page 대비 inset, 복기 pane = card. 현행 `background: var(--muted)` 의존 제거(#49에서 muted=control로 변함 → pane 면 색이 의도보다 진해짐 [확인 필요, NOT_RUN]) |
| 상태 | 예시 복기(`exampleOnly` 라벨) · 로딩 · 없음 · 오류 · **저장 중** · **저장 실패**(Notice + 입력 보존 + 다시 시도) · **저장됨**(Notice success 1줄, 입력 시작 시 해제 — pr-review F4) · 이탈 보호(`retrospectiveDirty`) · 비인증 AuthRequiredSurface · 오프라인 Banner |
| 문구 | CTA = "복기하기"(NAV). 현행 "AI와 복기하기"는 [Q3] |

## A1. Auth Entry `/auth`

| 항목 | 명세 |
|---|---|
| 계약 | UX-Δ §6(OAuth-first, provider-neutral, 취소→Review Result 복귀, 실패 비파괴), ID(전화번호 없이 가입) |
| 위계 | **Key = 안내 1문장**: "계속하려면 로그인해 주세요. 처음 이용하는 경우 계정이 함께 만들어집니다."(UX-Δ §6 문구) |
| B / C | B: 카드 1개 + 보존 안내(inset), eyebrow·밑줄 헤더 삭제. C: provider 계속 버튼 **strong 1개**. "취소"=text |
| 컴포넌트 | PageHeader(뒤로=취소), Card, Notice, TextField(참조 로그인 폼은 비운영 한정), Button |
| 390/768/1280 | 단일 열, 카드 폭 440 중앙 / 동일 / 동일 |
| 상태 | 로딩(`aria-busy`) · **실패**(Notice danger + "검토 결과는 그대로예요" + 다시 시도) · **사용 불가**(Notice warning, 계속 버튼 `aria-disabled`+이유) · 이전 목적지 있음("선택한 기록으로 이어서 갈게요" 유지) / 없음 · 취소 → 원래 화면 · 성공 후 resume + 제목 포커스 · 비운영 참조 폼은 inset 박스 + "비운영" 라벨 · exact provider 미확정 → 버튼 문구 중립 유지 |

## P1. 랜딩 `/:locale` (후순위)

계약에 화면 규정이 없어 **Owner 마케팅 결정 후**로 둔다. 토큰·컴포넌트 정렬만: 알약 보더 배지(→ 텍스트 로고), 예시 카드(inset), CTA strong 1, COPY ①② 고지 inset 1문장. SEO/Pre-render 구조(h1·메타)는 변경 금지(STEP 6·12).

## N1. NotFound / P4

EmptyState(제목 + 설명 + text 또는 strong 1개 "검토 시작으로"). 앱 NotFound는 내비 없음(현행 유지).

---

## 부록: 상태 커버리지 체크 (○=명세 있음 −=해당 없음)

| 화면 | 로딩 | 빈 | 오류 | 비인증 | 저장 중 | 저장 실패 | 이탈 보호 | 오프라인 |
|---|---|---|---|---|---|---|---|---|
| H1 | − | ○ | ○(검증) | − | − | − | − | ○제안 |
| H2 | ○ | ○ | ○ | ○ | − | − | − | ○제안 |
| O1 | − | − | − | − | − | − | − | − |
| J1 | ○ | ○ | ○ | ○ | − | − | − | ○제안 |
| J2 | − | − | ○ | ○ | − | − | − | − |
| J2a/b | − | − | ○ | ○ | ○ | ○ | ○ | ○제안 |
| J3 | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○제안 |
| J4 | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○제안 |
| A1 | ○ | − | ○ | − | − | ○ | − | ○제안 |
