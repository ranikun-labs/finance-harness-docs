# 02 계약·Foundation 대비 차이 분석

- 비교 축: **현재** = `origin/dev` 89ca509 코드 / **계약** = UX-Δ·NAV·GUARD·COPY·QUOTA(조항은 01 문서 약칭) / **Foundation** = v1.0.0 규칙(B 기본 차분 + C 핵심만 강조, 배지는 상태에만, 브랜드색은 행동·선택에만, 카드 테두리·그림자 없음, 진한 버튼은 완료/핵심 1개, 복기하기 subtle, Select Chip 라벨 "함께 남기기", WCAG 2.2 AA).
- 공통 컴포넌트·토큰은 PR #49(0409274, Draft, dev에 **아직 미병합**)에 있다. dev의 `Card`는 여전히 `border + shadow-sm`, `Button` 기본은 strong, `--background`는 흰색이다. 아래 "현재" 열의 화면 클래스는 #49가 병합돼도 **자동으로 안 바뀐다**(#49는 화면 파일 diff 0, pr49-review §6). [확인]
- 수치는 `origin/dev` 소스를 grep한 결과다(정확한 위반 수가 아니라 규모 지표). [확인]

## 1. 횡단 차이 (화면 공통, ID로 03·05에서 참조)

| ID | 항목 | 현재 [확인] | 계약 | Foundation | 영향 | 심각도 |
|---|---|---|---|---|---|---|
| X1 | **카드 테두리·그림자** | 화면 파일 26개가 `border`/`border-border`/`border-l-4` 사용, ~10개가 `shadow-sm`/`shadow-xs`. 카드를 `rounded-xl border bg-card`로 직접 짠 곳 다수(Ask 하단 CTA·로딩, Detail 상태 박스, List 상태 박스, Auth, Onboarding 목록, Decision Panel, New 진입 선택 링크, Home 예시 버튼). `JournalRecordCard`는 선택 시 `border-primary/50 bg-primary/5` | 규정 없음 | 카드는 테두리·그림자 없이 page 대비 면으로 구분, 중첩은 inset만, 그림자는 오버레이만 | #49 병합 후에도 화면의 직접 border가 남아 "상자 안 상자" 인상 유지 | 높음 |
| X2 | **브랜드색 장식** | `border-l-4 border-l-primary`(Ask 질문 카드 ×3, Onboarding hero), 예시 기록 카드의 `bg-primary` 세로 막대, eyebrow `text-primary uppercase`(Home·Ask·Auth), `bg-primary` 로고 칩(Onboarding), 체크리스트 체크 시 `bg-primary` 채움 | 규정 없음 | 브랜드색은 행동·선택에만(화면당 3곳 이하, P2) | 한 화면에 5곳 이상. 행동이 아닌 곳이 브랜드색을 써 클릭 가능한 것처럼 읽힘 | 높음 |
| X3 | **진한 버튼 남용** | 화면별 strong 후보: H1(제출 + 빈 상태 CTA 2개 동시 가능), H2 오류(재시도)/빈(질문하러 가기)/**하단 CTA 중 학습 노트가 strong**, J1 헤더 "새 기록" + 빈 상태 CTA, J2 invalidType 링크, J2a/b 제출, J3 **복기하기 strong lg 전폭**, J4 저장, A1 제출/재시도, N1 홈으로, P1 CTA | UX-Δ §1 핵심 task = "판단과 이유를 남긴다" | 진한 버튼은 화면당 1개(완료/핵심), 복기하기는 subtle | 위계가 납작, 3·4번째 버튼이 경쟁 | 높음 |
| X4 | **본문 크기** | 본문·보조가 `text-sm`(14px)이 지배(소스 기준 text-sm 121, text-xs 61: fe-analysis). 사용자가 쓴 글도 `text-sm leading-7`(14/28): J3 reasoning·keyContent, J4 원본 요약. `text-xs`(12px)가 라벨·메타·eyebrow에 사용 | UX-Δ §4.1 "Reasoning은 핵심 user-owned body" | reading 16/27(사용자 글), body 15/24, supporting 14/20, meta 13/18, 최소 12px(역할은 13 이상) | 핵심 글이 가장 작은 축. 12px 메타는 역할 표(13) 아래 | 높음 |
| X5 | **입력 필드 회색/모양** | 입력에 `bg-background`: Home textarea, Auth 입력 2, Review textarea 4, DecisionContext 2, SavedRetrospectives 3. #49 병합 시 `--background`=page(#f2f4f6)가 돼 **입력이 회색 면**이 됨(pr49-review §5). 반경은 `rounded-md/xl`(14/20px) 혼재, 높이 `min-h-11`(44) 혼재 | UX-Δ §7.2 native label/fieldset/aria-invalid/describedby, 44px 이상 target | input fill = surface(card), 외곽선 `--input`(#78838f, 비텍스트 3:1), 반경 control 10, 기본 컨트롤 높이 48 | 입력이 배경에 묻힘 또는 의도 없는 회색. 필드 전용 컴포넌트(TextField)가 없어 화면마다 클래스 복붙 | 높음 |
| X6 | **배지가 메타에 쓰임** | `RecordTagBadge`가 entryType·action·emotion을 Badge로 렌더. J1 행마다 유형+행동 배지 2개, J3 메타 InfoRow 값이 배지, H2 `Badge info`("예시 데이터"), 체크 진행도 `n/m`도 Badge. **감정 FOMO·혼란이 `destructive` tone(빨강)**, 확신·관심이 `info` | GUARD: action은 "기록용 선택, 실행 CTA처럼 강조 금지". COPY B-05 과거 평가 금지 | 배지는 상태에만, 메타는 텍스트(P1) | 빨강 FOMO는 "나쁜 감정" 평가로 읽힘(판단 평가 금지선과 인접). 의미 없는 알약 반복 | 높음 |
| X7 | **상태 표현** | 오류는 `text-destructive text-sm`(fg) 문장, `border-destructive/30 bg-destructive/5` 상자, 로딩은 문장/막대. `ErrorState`(#49)는 아직 사용처 없음 | UX-Δ §7.2 text-based errors, error summary, first-error focus | 상태색은 브랜드와 독립(danger/warning/…), 색만으로 전달 금지, 오류 3단(무슨 일/영향/다음 행동) | 상태 UI가 화면마다 다르게 생김 | 중 |
| X8 | **헤더·제목 위계** | 페이지마다 자체 헤더: H1 `text-3xl/4xl` 히어로, J1 `text-3xl`, Ask `text-2xl font-extrabold` + eyebrow, J2 `text-2xl`, J3 `h1`=16px 앱바 제목 + `h2` 24px 자산명, J4 `h1`=16px. **H2는 질문이 있을 때 h1이 `<p>`로 바뀌고 결과 제목이 h1** (정상 문서 구조지만 앱바 제목이 시각적 h1 역할). `PageHeader`(common)는 어디서도 안 씀 | UX-Δ §7.2 Auth Resume 후 heading focus | page-title 24/32 하나, 섹션 제목은 카드 밖 18/26(SectionHeader) | 화면 간 위계 불일치, Key 영역 판정 어려움 | 중 |
| X9 | **선택 컨트롤** | 체크리스트 = 숫자 네모(`aria-pressed` 버튼, 체크 시 파란 채움); 유형·행동·감정 선택 = `JournalChoiceGroup`(sr-only radio + `has-checked:bg-primary/10 border-primary` 알약); Decision Context 켜기 = 수제 `role="switch"`; 선택 근거 = 체크 토글 | UX-Δ §5 "Context toggle native semantics" | Checkbox(native), Select Chip(`aria-pressed`, 라벨 고정, 44px) | Foundation 컴포넌트 미사용. 라벨 고정 원칙 위반 가능성은 아래 X10 | 중 |
| X10 | **"함께 남기기" Select Chip 없음** | ko 사전에 "함께 남기기"는 "검토 맥락 함께 남기기"(패널 제목)뿐. 사실(fact)별 선택 UI는 H2에 **없고** J2a 패널 안 선택 근거(optionalEvidence)로만 존재 | UX-Δ §5 "Optional evidence는 별도 선택 영역" (위치 미규정) | 검토 결과 사실 행마다 Select Chip "함께 남기기"(라벨 고정, 체크+Subtle 면) | **결정 필요**: 칩을 H2에 둘지 J2a에 둘지(03·05의 열린 질문 Q1) | 중 |
| X11 | **이탈 보호 UX** | 앱 내 이동은 `window.confirm`(BottomNavigation, J2 내부 링크), 브라우저 이탈은 `beforeunload`. 모달 UI 아님 | UX-Δ §7.2 dirty warning 유지 | Dialog(시트/모달) 컴포넌트 있음(#49) | 네이티브 confirm은 Capacitor에서 어색하고 문구 제어 한계. 단 교체는 `useBlocker` 등 라우팅 변경이 필요해 **디자인 PR과 분리** | 중(후순위) |
| X12 | **오프라인·저장 중 표현** | 오프라인 처리 코드 **없음**(`navigator.onLine`/offline grep 0). 저장 중은 폼 하단 `role=status` 문장, 실패는 `role=alert` 문장 + code별 문구(`network_error` 있음) | 계약 없음(Capacitor 목표만) | 상태 UI 규칙 + ErrorState | 오프라인은 **제안(계약 없음)** 으로만 명세, 구현 보류 | 낮음 |
| X13 | **Dark/Light 위험** | #49 이후 alias 충돌: AppShell 바깥 `bg-muted` vs 프레임 `bg-background`가 같은 색, Auth `bg-muted` 박스 vs 카드, BottomNavigation md 세그먼트(컨테이너 `bg-muted`와 선택 탭 `bg-background` 동색). Dark는 page #0f1114 / card #1a1d21 명도차 작음(대비비 약 1.09, 비텍스트 대비는 카드 경계 비해당) | WCAG 1.4.11(컨트롤 경계만) | 면 대비 + inset(#22262b) | 구분이 면 색에만 의존하는 곳 확인 필요. 렌더 검증 NOT_RUN | 중 |
| X14 | **레이아웃 계약** | 콘텐츠 호스트 `max-w-[660px]`(Ask 760). J2a는 항상 단일 열, **Context Rail 없음**, Decision Context는 접기 없음(`details`/`aria-expanded` 없음). J1\|J3, J4 2-pane은 구현됨(≥1024 landscape 또는 ≥1280) | UX-Δ §7.1: 1194 = Editor + Context/Record Info, 1360+ = Editor + Context Rail, Phone = Decision Context collapsible | 데스크톱은 폭만 키운 모바일 카드 금지 | J2a 데스크톱 레이아웃과 Phone 접기 미구현 → 03 명세에 반영 | 중 |
| X15 | **카피 불일치 후보** | 복기 CTA ko = "**AI와 복기하기**"(ko.ts:541). 하단 CTA는 "판단 기록 남기기"/"학습 노트로 남기기" | NAV 이동 관계 = "복기하기". Owner 지시 CTA 유지: "판단 기록 남기기"·"학습 노트로 남기기" | Foundation 복기하기 subtle. SKILL ux-copy는 "AI 질문" 금지어 취급 | "AI와 복기하기"는 계약 라벨("복기하기")과 다르고 AI 관여를 암시. **Owner/카피 확인 필요**(Q3). 변경 시 i18n+테스트 동시 수정 | 중 |
| X16 | **저널 수정·삭제 UI 무스타일** | JournalEditor·LifecycleControls가 클래스 없는 `<button>`·`<p>`·`<form>`(필드 라벨은 기존 key 재사용) | 조항 없음(01 §3) | Pattern 11·12(충돌·삭제 확인)는 시안 | 계약 근거 없는 기능이 가장 거친 모습으로 노출 | 중 (Owner 결정) |

## 2. 화면별 차이

| 화면 | 현재 핵심 | 계약 대비 | Foundation 대비 (우선 수정) |
|---|---|---|---|
| H1 검토 시작 | 히어로 eyebrow+3xl 제목+설명 → **카드(질문 폼)** → 예시 3 → 정책 고지 → 최근 기록. 카드에 `shadow-sm`, textarea `bg-background rounded-xl border`, 제출 `lg` strong + "→", 예시 버튼 3개 `border bg-card` | NAV·GUARD 충족. 질문=메모리만, Guest 가능 OK. 최근 기록은 예시 데이터이며 저널 Auth required 정신과 충돌 가능 [추정] | X1·X2(eyebrow)·X4·X5 해당. Key = 질문 입력 1개. 예시 질문 = SelectChip류 아님(제출 즉시 실행) → "text" 버튼/목록 행 |
| H2 검토 결과 | 질문 카드(좌 파랑 막대) → 체크리스트(숫자 네모) → 사실 카드들(출처·기준일 `dl text-xs`) → 해석 카드(`bg-primary/5`) → 모르는 것(배지 태그) → 시각·고지 → 하단 CTA 카드(테두리, study=strong) | 확인형 문체·CTA 2개 문구 OK. 부분 결과(partial) 표시 OK. Guest→Auth resume OK. **CTA 우선순위가 계약 task와 반대**(X3) | X1·X2·X3·X4·X6(예시 배지)·X9·X10. 4섹션 제목을 카드 밖으로(SectionHeader), 사실=출처·기준일, 해석="사실이 아니라 해석" 표시 |
| O1 온보딩 | 로고 칩 + hero 카드(좌 막대) + "제공하지 않는 것"·"제공하는 것" 목록(테두리) + 시작 CTA + 정책 고지 | COPY ①② 고지 존재 OK(REQUIRED 충족 [확인]). "동의" 증적 아님 주석 있음 | X1·X2·X4. 고지는 inset 1문장(섹션당 1문장, P7). 시작 CTA = 진한 1개 |
| J1 저널 목록 | 헤더(하단 border, 3xl) + "새 기록"(strong, 오류 시 outline) + 필터(서버필터 플래그) + 카드 목록(유형·행동 배지, 이탤릭 질문+파랑 막대) + 상태 박스(테두리) | Auth required OK(401 안내). 2-pane OK | X1·X3(새 기록 vs 빈 상태 CTA 중복)·X6·X2(막대)·X7 |
| J2 작성 진입 | 제목+프롬프트+안내 + **테두리 링크 카드 2개**(`hover:border-primary/50`) | UX-Δ §3.1 OK(독립 surface, Link) | X1(링크 카드→Card+행동) · 선택 카드는 Card(inset) 행 + chevron. 제출류 strong 없음 |
| J2a 판단 기록 | 앱바 링크 → 제목 → (review-derived: 출처 박스 + 되돌아가기) → "기록 유형 변경" 링크 → Decision Context 패널 → 폼 → 상태 문장. 순서: 폼 내부가 대상·행동·이유·감정·기록 시점(접힘) | §4.1 순서 대체로 충족, occurredAt secondary OK([확인] 접힘). Decision Context는 폼 **앞**에 위치 → 계약 hierarchy "6. Decision Context(마지막)"와 어긋남 [확인] | X1·X3·X4·X5·X9(switch)·X14(접기·Rail 없음). Reasoning textarea가 Key |
| J2b 학습 노트 | 구조 유사(무엇을 배웠나요/핵심 정리/더 확인할 것) | §4.2 필드 OK, 초안 안내 문구 OK | 위와 동일. 초안 안내는 inset status |
| J3 저널 상세 | 앱바(←, 16px 제목) → [삭제][수정 영역 bare] → 자산명 h2 24px + 메타 카드(border+shadow, 배지 포함) → 근거 카드(14/28) → **복기하기 strong lg 전폭** → snapshot | NAV(읽기 전용)와 수정 UI 공존(X16). Decision Context snapshot 읽기 전용 OK. 복기 진입 OK | X3(복기 subtle)·X4(reading 16/27)·X6·X1·X16. Key = 판단 한 문장/자산명+행동, 본문 reasoning |
| J4 복기 | 앱바 → [예시 안내] → 원본 요약 카드 | 작성 영역(원본\|복기 2-pane) → 저장됨 목록 | 원본 불변 문구 존재 OK, COPY ⑥ 고지(PolicyNotice) OK | X1(#46이 일부 해소)·X4·X5·X3(저장 strong은 맞으나 w-full 상시)·`details`의 테두리 |
| A1 Auth Entry | 카드 + 상태 3종 + (참조 로그인 폼) + provider 버튼 + 취소 | UX-Δ §6 문구·취소·실패 비파괴 OK. provider 미확정 → 버튼 1개 중립 문구 | X1·X2(eyebrow)·X5·X3. Key = 1줄 안내 + 계속 버튼 |
| P1 랜딩 | 알약 배지(보더)+히어로+CTA+예시 카드 | 계약 화면 규정 없음. COPY ① 고지는 있음(`public.home.note`) [추정] | X1·X2·X6(알약은 로고 대용). 별도 마케팅 디자인 결정 필요 → **이번 범위에서 후순위** |
| N1/P4 NotFound | 제목+문장+strong 버튼 | 규정 없음 | X3(빈 상태 CTA 1개 OK, subtle/strong 정리) |

## 3. #46(저널 상세·복기 비주얼 마감) 평가

- 대상: `origin/codex/finance-visual-polish-20261121` PR #46 HEAD **b73aff3**. `git log origin/dev..b73aff3` = b73aff3 한 커밋(선행 a95716f는 이미 dev에 포함), `git diff origin/dev...b73aff3` = 3파일(+23/−18): `SavedRetrospectives.tsx`, `JournalDetailPage.tsx`, `JournalReviewPage.tsx`. 단 `git diff origin/dev b73aff3`(2-dot)은 15파일로 보이는데 이는 dev가 더 앞서 있어서 생기는 차이이며 PR 효과가 아니다. 이전 점검 노트(open-prs.md)의 "19파일"은 PR 본문 기준 합산으로 보이며 현재 dev 기준과 다르다. [확인]
- 시각 개선 중 **Foundation과 같은 방향(가져갈 것)**:

| 변경 | 효과 | Foundation 정합 |
|---|---|---|
| 복기 저장 목록 `article`의 카드 테두리 → `border-t py-5` 구분선 | 상자 안 상자 제거 | X1 해소 방향 |
| `<details>` 테두리 상자 → `border-y` | 박스 감소 | X1 해소 방향 |
| 상세 본문 `max-w-[720px]` + `whitespace-pre-wrap` | **줄바꿈 보존**, 긴 글 가독 | 사용자 글 보존(기능 개선) |
| textarea `min-h-[120px/80px]`, `p-4`, `leading-6`, `focus-visible:ring-2` | 입력 면적·행간 개선 | 방향 부합, 값은 Foundation 값 아님 |
| 폼 `space-y-6`, 날짜 `text-sm font-medium`, 패널 `px-5 py-6 sm:px-7` | 간격 정돈 | 섹션 간격 40 기준에는 못 미침 |
| 제출 `min-h-12 w-full sm:w-auto` | 터치 48 | 컨트롤 48 정합 |

- **Foundation과 어긋나거나 병합 시 충돌하는 것(가져가지 않을 것)**:
  - `focus-visible:ring-2 focus-visible:ring-ring` 개별 지정 → #49의 전역 `:focus-visible`(2px brand + offset 2)와 **이중 포커스 표시** [확인: 두 규칙 공존, 렌더 NOT_RUN].
  - 여전히 `text-sm`(14px) 본문, `rounded-xl border`(20px, Foundation은 control 10/inset 12), `border-border bg-background` 입력, `${buttonVariants()}` strong.
  - J3의 복기하기 strong lg와 메타 카드 테두리·그림자, 배지는 미변경(X3·X1·X6 그대로).
  - 수정 파일 3개가 03의 J3·J4 PR 대상과 **100% 겹침** → #46이 먼저 병합되면 J3·J4 PR이 충돌 재작업, 아니면 #46이 낡은 클래스를 되돌림.
- **추천(나라면)**: #46을 병합하지 않고 "의도(구분선화, 줄바꿈 보존, 읽기 폭, 최소 높이)만 J3·J4 화면 PR에 Foundation 값으로 흡수한 뒤 #46 닫기". 이유: 값이 Foundation과 달라 어차피 한 번 더 고쳐야 하고, 같은 3파일을 두 번 건드리면 리뷰 비용이 2배. 버리는 것: #46의 즉시 시각 이득(병합 시 바로 보임)과 작성자 기여 이력(→ PR 설명에 인용). 대안은 #46을 먼저 병합하고 화면 PR을 그 위에 쌓는 것인데, 이중 포커스·`rounded-xl` 값이 남아 화면 PR이 사실상 되돌리기가 된다.
- 한계: #46의 `whitespace-pre-wrap`이 J3 reasoning 줄바꿈을 보존하는 것은 코드 diff로만 확인, **렌더·테스트 NOT_RUN**. 390/834/1194/1440 전후 캡처는 PR 본문 주장이며 직접 열람하지 않았다.

## 4. 계약상 충족된 것(회귀 금지 목록)

감정 태그 5종 고정(`EMOTION_TAGS`) · 행동 라벨 4종 기록용 · 추천/목표가/손절/수량/비중 UI 없음 · 질문=탭 메모리 · Review 전체 자동 저장 없음 · 복기는 원본 불변 문구 · COPY ⑥ 고지(J4) · occurredAt 접힘 secondary · `journalType` discriminator·`JournalCreate*` 보호 계약 · auth 취소 시 Review Result 복귀 · 헤딩 포커스(Auth resume). 디자인 PR은 이 목록의 테스트를 깨면 안 된다. [확인: 코드·테스트 파일 존재, 실행은 NOT_RUN]
