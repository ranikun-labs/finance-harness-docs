# 01 화면·흐름 인벤토리 (Finance FE)

- 기준: finance-harness-fe `origin/dev` 89ca509 (git show/archive 읽기 전용, 작업 트리 미수정). 공통 컴포넌트는 `origin/claude/adoring-galileo-ptj1g7` 0409274(PR #49, Draft) 기준.
- 계약 약칭: **UX-Δ** = `finance-harness-docs/service-policy/finance-ux-delta-auth-journal-handoff.md`(조항 §), **PP** = `finance-product-policy.md`, **NAV** = FE `docs/nav-map.md`, **GUARD** = FE `docs/product-policy.md`, **COPY** = `05-product-legal-ops/finance-disclaimer-and-copy-guide.md`, **QUOTA** = `finance-ai-usage-and-quota-policy.md`, **ID** = `finance-onboarding-and-identity-policy.md`.
- 표기: [확인] 코드·문서 직접 확인 / [추정] 근거 부족한 해석 / [NOT_RUN] 실행·렌더 미검증 / [보류] 계약 미확정.
- 제품 정책(PP)은 가입·Plan·Usage 원칙 문서이고 화면 UX는 UX-Δ가 소유한다. 그래서 화면 근거는 대부분 UX-Δ·NAV·GUARD다. [확인]

## 1. 화면 목록

### A. 공개 웹 (`PUBLIC_ROUTE_PATHS`, Pre-render 대상, 앱 셸·하단 내비 없음)

| ID | 경로 | 화면 | 구현 상태 [확인] | 계약 근거 |
|---|---|---|---|---|
| P0 | `/` | 기본 로케일 redirect(`/ko`) 전용, 콘텐츠 없음 | RootRedirect | routes.ts 헤더, route-architecture |
| P1 | `/:locale` | 랜딩(공개 홈) | PublicHomePage 실 UI(제목·CTA·예시 카드) | NAV "공개 웹 `/:locale/*`" → route-architecture. 화면 내용 계약은 **없음**. COPY §5.1 ①②(서비스 성격·판단 비제공 고지) 적용 [추정: 랜딩 위치는 COPY ①의 "랜딩"] |
| P2 | `/:locale/features` | 기능 소개 | FeaturesPage placeholder | frontend-roadmap §3 "공개 Home·Features·Learn은 locale별 placeholder" |
| P3 | `/:locale/learn/*` | 학습 콘텐츠 | LearnPage placeholder, 하위 slug는 SPA fallback | 동일 |
| P4 | `/:locale/*` | 공개 NotFound | PublicNotFoundPage | route-architecture |

### B. 인증

| ID | 경로 | 화면 | 상태 [확인] | 계약 근거 |
|---|---|---|---|---|
| A1 | `/auth` | Auth Entry(provider-neutral, OAuth-first, 하단 내비 없음) | AuthEntryPage. 상태: 로딩 / 실패(+재시도) / 사용 불가 / 참조 로그인 폼(비운영) / 이전 목적지 있음·없음 / 취소 | UX-Δ §6(Auth Entry·resume·취소 시 Review Result 복귀·성공 후 무조건 `/app` 금지), NAV "인증 진입", ID §3(전화번호 없이 가입, Kakao-first이나 Kakao-only 아님). **provider·protocol·session은 계약 밖(UX-Δ §6, §8)** |

### C. 웹앱 (`/app/*`, AppShell, 모바일 우선·Capacitor 공통)

| ID | 경로 | 화면 | 내비 | 상태 [확인] | 계약 근거 |
|---|---|---|---|---|---|
| H1 | `/app` | **검토 시작**(Primary Surface) | 검토/저널 | 질문 입력 / 빈 제출 검증 / 예시 질문 3개 / 정책 고지 / 최근 기록(예시 데이터) 또는 빈 상태 | NAV(검토 시작 = 판단 전 질문), UX-Δ §1·§6(Guest 가능), GUARD(Home dashboard 금지). 최근 기록·예시 질문은 계약 조항 없음(디자인 원본 유래) [추정] |
| H2 | `/app/ask?ref=` | **검토 결과**(내부 route) | Phone 숨김, Tablet 이상 표시 | 질문 없음(빈) / 로딩 / 결과 / 부분 결과 / 오류 / (dev 전용 LocalGeneralAnalysis) | NAV(질문은 탭 메모리만, 새로고침 시 재시작), UX-Δ §1·§3.2(기록으로 이어가기 CTA 2개)·§6(Guest 가능, CTA 선택 시 Auth→resume), GUARD(확인형 문체, 추천 금지) |
| O1 | `/app/onboarding` | 온보딩 | **내비 없음**(AppShell 직속) | 단일 정적 화면 | NAV "첫 진입 안내", COPY §5.1 ①②(성격·비제공 고지 REQUIRED) |
| J1 | `/app/journal` | **저널 목록** / 빈 상태 | 검토/저널, Landscape·Desktop은 목록\|상세 2-pane | 로딩 / 빈(전체) / 빈(필터) / 오류 / 401 로그인 필요 / 더 불러오기·오류 / 목록 끝 / 삭제됨 목록 | NAV, UX-Δ §6(Auth required), GUARD(승인 2-pane = Journal List\|Detail) |
| J2 | `/app/journal/new` | **작성 진입 선택**(Entry Choice) | 검토/저널(Phone은 context 내비) | 정상 / 잘못된 type 안내 / 비인증 안내 | UX-Δ §3.1(독립 surface, Link/Button) |
| J2a | `/app/journal/new?type=investment` | **판단 기록 작성**(Focused Editor) | context | 입력 / 검증 오류 요약 / 제출 중 / 저장 실패(code별) / 이탈 보호 / auth resume 안내 / review-derived(Decision Context 패널) | UX-Δ §4.1·§5·§6, 보호 계약 §9(JournalCreate*, 내부 discriminator) |
| J2b | `/app/journal/new?type=study` | **학습 노트 작성** | context | 위와 동일 + review-derived 초안 안내 | UX-Δ §4.2 |
| J3 | `/app/journal/:id` | **저널 상세**(저장된 원본 view) | 검토/저널 | 로딩 / 오류(code별) / 없음 / 성공 + (수정·삭제·Decision Context snapshot) | NAV(읽기 전용 view), UX-Δ §5(snapshot) |
| J4 | `/app/journal/:id/review` | **복기** | 검토/저널, Landscape·Desktop 원본\|복기 2-pane | 예시 / 로딩 / 오류 / 없음 / 작성 / 저장 중 / 저장 실패 / 저장됨 목록 | NAV(원본을 바꾸지 않고 별도 복기), GUARD(승인 2-pane = Original\|Retrospective), COPY §5.1 ⑥(복기 기능 고지 REQUIRED, "복기는 과거 평가·행동 지시가 아님") |
| N1 | `/app/*` | 앱 NotFound | 없음 | 정적 | route-architecture |

### D. 셸·공통 표면

| ID | 대상 | 상태 [확인] |
|---|---|---|
| S1 | AppHeader(앱 이름 / 로그인 링크), LocaleSwitcher | 비인증 시 로그인 링크 노출 |
| S2 | Primary Navigation: Phone 하단, Tablet Portrait 상단, Landscape·Desktop 좌측 rail | `BOTTOM_TABS` 2개(검토/저널), 이탈 보호 시 `window.confirm` 사용 |
| S3 | 비운영 참조 세션 배너(`finance-reference-session`) | 비운영 전용. 디자인 대상 아님, 토큰만 적용 |
| S4 | Lazy loading fallback / PageSkeleton | role=status |

## 2. 사용자 흐름

```text
[랜딩 P1] → [온보딩 O1] → [검토 시작 H1] → 질문 제출 → [검토 결과 H2]
H2 ─ 다시 검토 ─→ H1
H2 ─ 판단 기록 남기기 ─→ (Guest면 A1 → 같은 목적지 resume) → J2a (+ Decision Context 후보)
H2 ─ 학습 노트로 남기기 ─→ (Guest면 A1 → resume) → J2b (+ 검토에서 가져온 초안)
J2a/J2b ─ 저장 성공 ─→ J3
J1 ─ 새 기록 ─→ J2 ─ 유형 1회 선택 ─→ J2a|J2b      (Direct Entry, Auth required)
J1 ─ 기록 선택 ─→ J3 ─ 복기하기 ─→ J4 ─ 저장 성공/뒤로 ─→ J3
A1 ─ 취소/실패 ─→ 원래 H2(검토 결과 보존)
```

- 비인증 진입: H1·H2는 Guest 가능, J1·J2a·J2b·J3·J4는 Auth required(`AuthRequiredSurface`). [확인] AppRouter, UX-Δ §6.
- 주 내비 두 개(검토/저널)는 NAV·GUARD 고정. 설정·프로필·알림 route는 **존재하지 않는다**. [확인]

## 3. 코드엔 있으나 계약 조항을 못 찾은 것 (디자인 범위 처리)

| 항목 | 위치 | 확인 결과 | 디자인 처리 |
|---|---|---|---|
| 저널 **수정**(JournalEditor) | J3 | UX-Δ·NAV에 조항 없음. NAV는 상세를 "읽기 전용 view"로 정의. 스타일 없는 bare `<button>`·`<p>`로 구현됨 [확인] | 현행 기능 보존, Foundation 컴포넌트로 스타일만. 신규 UX 결정 금지. **Owner 확인 필요** |
| 저널 **삭제·복원**(JournalLifecycleControls, DeletedJournalList) | J1·J3 | 동일(조항 없음). Foundation 시안 Pattern 12(숨김·복원, 보존기간 미표기)가 있으나 시안일 뿐 계약 아님 [확인] | 위와 동일. 삭제는 Dialog(danger) 확인 패턴 적용 후보 |
| 검토 시작 **최근 기록**(예시 데이터) | H1 | NAV 조항 없음. 예시 데이터를 비인증 사용자에게 노출 | "예시" 라벨 유지, 저널 Auth required와 충돌하지 않게 유지 [추정] |
| **Decision Context 패널**(켜기/끄기, 선택 근거) | J2a | UX-Δ §5 근거 있음(기본 ON 후보, OFF 가능). persistence DTO는 §8 보류 | 표현만 정렬 |
| 일반 분석 LocalGeneralAnalysis | H2(dev 전용) | 개발 플래그(`VITE_LOCAL_GENERAL_ANALYSIS`) + DEV 한정 | 디자인 대상 제외, 토큰만 |

## 4. 보류 표시 (계약 미확정, 화면·슬롯을 만들지 않는다)

| 항목 | 상태 | 근거 |
|---|---|---|
| 보유 종목 화면(#47 합성 보유 종목 로컬 실험) | **보류** | 계약 미확정(Owner 지시). 로컬 루프백 실험, 실제 인증·소유권 검증 아님 |
| 관심 종목 카드(#48 참조 세션) | **보류** | 동일. 홈에 카드가 얹히는 안이라 H1 레이아웃에 슬롯을 예약하지 않는다 |
| 설정·프로필·구독/Trial 화면 | 보류 | route 없음. PP §5·Trial 정책은 원칙만 있고 UI 조항 없음 |
| 한도(Quota) 소진 화면 | 상태 명세만(03 문서), 구현 보류 | QUOTA §5 "남은 Usage·소진 상태를 명확히 표시", 수치·API는 BE 계약 대기 |

## 5. 읽지 못한 것 / NOT_RUN

- `design/claude-export/` 시안 HTML 15개, Foundation 원문(02-foundation, 04-guidelines, 05-specs, 06-qa-checklist)은 이 세션에서 읽지 않았다. Foundation 값은 지정된 3개 파일(SOL_REVIEW §5, token-baseline, SKILL_REVIEW delta)과 `ds/project/*.md` 요약 기준이다. K1(Key 영역 수 판정) 한도는 원문 NOT_VERIFIABLE.
- 렌더·스크린샷·e2e·vitest·실기기는 이 단계에서 **전부 NOT_RUN**(문서 작성 단계).
