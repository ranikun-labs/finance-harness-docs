# F1-01 Adaptive 공통 계약

- 작성: Fable F1 (Principal Design Architect), 2026-11-24. 모델 `claude-fable-5-2`.
- 성격: 설계 제안 문서다. 제품 정책·Foundation 정본·구현 승인이 아니다.
- 표기: **[사실]** 파일·렌더로 직접 확인 / **[추정]** / **[권고]** / **NOT_VERIFIED** 직접 보지 못함.

## 결론

1. **폭 기준은 두 제품 모두 바꾸지 않는다(A).** Finance 768 / 1024(가로) / 1280, Carelog 768 / 가용 폭 1100 이상이면서 가로. 시안 6묶음을 경계 전후 20개 폭에서 렌더해 두 Host 계약과 같은 전환을 확인했다(§3).
2. **두 제품이 공유할 것은 "값"이 아니라 "역할"이다.** 내비 위치와 경계값은 제품마다 다르게 두고, Layout Primitive 6개와 List/Detail·Sticky Action·Form·Notice 규칙을 공통 계약 후보(C)로 올린다(§4, F1-04).
3. **Finance 구현에 계약과 다른 곳이 1건 있다.** 1280×1366(세로)에서 `layout.ts`는 rail이라고 하는데 실제 화면은 상단 내비다(§2 F-1). 필수 수정이다.
4. **Owner 결정이 필요한 것은 8건이다(§7).** 가장 영향이 큰 것은 Carelog 폰 하단 내비 높이(101px)와 Finance 후속 질문 기능이다.

## 1. 분류 기준 (이 문서와 F1-02~05에서 같은 뜻으로 쓴다)

| 분류 | 뜻 | 이 문서에서의 예 |
|---|---|---|
| A | 기존 Host 계약 유지. 계약 값은 그대로 두고 그 안에서 표현만 정리하는 것도 A | 경계값, 내비 위치, 2-pane 조건, 상단 바 한 줄 정리 |
| B | Owner가 승인한 변경의 적용 | Carelog 반경 16/10, Teal Light |
| C | 두 제품에서 재사용 근거가 있는 공통 확장. v1.1 후보, 별도 승인 필요 | Layout Primitive, Notice, Field |
| D | Owner Gate. 정책·기능·API·인증·데이터·카피 의미·기존 LOCK 변경 | 후속 질문 기능, 하단 내비 계정 줄 이동 |

## 2. 입력 HEAD와 Drift

| 저장소 | 고정 HEAD | §5 기준 SHA | 계약 파일 변화 (기준 → HEAD) | 판단 영향 |
|---|---|---|---|---|
| design-system | `021e40e` | — | 정본. `sha256sum -c MANIFEST.sha256 --quiet` 실패 0건(215항목) | — |
| finance-harness-docs | `7dc249b` | `855053f` | `service-policy/` 변경 0줄. 추가 14개 파일은 모두 분석 폴더 [사실] | 없음 |
| finance-harness-fe | `fda50a6` (dev) | `db97f7f` | `docs/nav-map.md` 6줄, `docs/route-architecture.md` 9줄 변경, `src/constants/layout.ts` 신규 43줄, `layout.test.ts` 신규, `globals.css` +316, `ko.ts` +60, `AppShell`·`TabLayout`·`BottomNavigation` 변경 [사실] | **있음.** 경계값이 `layout.ts`로 코드화됐다(RPL-146). 이 문서는 HEAD의 `layout.ts`를 A의 기준으로 삼는다 |
| carelog-fe | `963f173` (dev) | `c4d67d4` | `docs/design/carelog/README.md` 114줄 변경(LOCK 해제, Foundation 적용 중), `globals.css` 445줄 변경, i18n 변경. `adaptiveHostModel.ts` 변경 없음 [사실] | **있음.** 시각 LOCK이 풀렸고 Teal·Compact·반경 16/10이 들어왔다. 2단 조건은 그대로다 |
| carelog-architecture | `aba36e3` | — | 분석 01~05와 Owner 결정 기록 추가 | B의 근거 |
| react-product-foundation | 미연결 | `0b821ea` | **NOT_VERIFIED.** 연결이 권한 분류기에 거부됐다(Unauthorized Access) | 공통 컴포넌트 이관 상태를 확인하지 못했다. F1-04의 "어디에 둘지" 판단은 추정이다 |

### 구현과 계약이 다른 곳 (기존 화면 검수에서 발견)

| ID | 내용 | 근거 | 판정 |
|---|---|---|---|
| F-1 | Finance 1280×1366에서 `getPrimaryNavPlacement(1280, 1366)`은 `rail`인데 실제 렌더는 상단 내비(1280×64)다. 원인은 `globals.css:300`의 `(min-width: 1024px) and (orientation: portrait)` 규칙이 1280 이상에서도 적용되기 때문이다 | `layout.ts:38-42`, `layout.test.ts:21`, `globals.css:300`, 캡처 `app-captures/finance/journal-detail-1280x1366.png`, `metrics.json`의 `start-1280x1366` | 필수 수정(A 위반). F1-05 PR-F0 |
| F-2 | 같은 폭에서 저널은 2-pane이 켜진다(`globals.css:365`의 `(min-width: 1280px)`). 그래서 "상단 내비 + 2-pane"이라는 계약에 없는 조합이 나온다 | 위와 같은 캡처 | F-1을 고치면 함께 해소 |
| C-1 | Carelog Dark에서 일정 행과 다음 일정 카드가 밝은 면으로 남는다(글자 대비 붕괴). Dark는 사용자에게 노출되지 않으므로 잠재 결함이다 | `app-captures/dark-390-finance-carelog.png` | 선택 개선. Dark 노출(D) 전에 반드시 해소 |
| C-2 | Carelog 레일과 주간 띠에 11px 글자가 남아 있다 | `app-captures/carelog/metrics.json`의 `tiny` | 필수 수정(12px 하한). 진행 중인 PR-2 범위 |

## 3. Breakpoint와 Navigation

### 3.1 계약 (A)

| | Finance | Carelog |
|---|---|---|
| 근거 | `finance-harness-fe@fda50a6` `docs/nav-map.md:41-46`, `docs/route-architecture.md:273-290`, `src/constants/layout.ts:8-42` | `carelog-fe@963f173` `docs/design/carelog/README.md` "Responsive foundation", `src/components/layout/adaptiveHostModel.ts:7-13` |
| 폰 | 767 이하. 검토·저널 루트는 하단 탭 2개. 결과·작성·상세·복기는 뒤로 가기만 있고 하단 탭 없음 | 767 이하. 모든 화면에 하단 내비(탭 2개 + 계정 줄) |
| 태블릿 세로 | 768 이상, 그리고 1024~1279 세로. **상단 내비** | 768 이상. 왼쪽 레일 80. 콘텐츠 약 520(768) / 560(1024) |
| 가로·데스크톱 | 1024 이상 가로 또는 1280 이상. 왼쪽 레일 224. Host 최대 1360 | 레일 80 유지 |
| 2-pane | Journal List·Detail, Original·Retrospective 두 가지뿐. Review 질문·결과는 2-pane 아님. 3-pane 금지 | 가용 폭 1100 이상이면서 가로일 때만. 레일 80을 빼므로 뷰포트 1180이 실제 전환점 |
| 콘텐츠 폭 | reading 660, result 760 | readable 520 / 560, 목록·일정은 scan |

### 3.2 v1.0.0 권고와의 차이 (원문은 고치지 않는다)

| v1.0.0 원문 | Host 계약 | 처리 |
|---|---|---|
| `docs/02` L134, `docs/04` §4: 0~767 하단 탭, 768~1099 좁은 레일, 1100 이상 사이드 내비 | Finance는 768부터 상단 내비, 1024 가로부터 레일. Carelog는 768부터 레일 80이고 1100은 2단 조건 | A를 따른다. 원문도 "제품 Host와 기존 계약이 우선"이라고 적고 있다 |
| `docs/04` §5: 읽기 폭 720, 사이드 내비 220, 2-pane 열 360 | Finance 660·760, 레일 224 | A. 폭은 토큰이 아니고 Host가 정한다(`docs/04` §5) |
| `docs/02` §10.5: 결과·업무 처리 화면의 Strong은 하단 고정 | Carelog 폰은 모든 화면에 하단 내비 101px가 있다 | Carelog는 Key Block 안 인라인 Strong으로 둔다(F1-03 §4). Finance 폰은 하단 고정 유지 |
| `docs/02` L29·L134, `docs/10` CM-3: 반경 16/10과 Teal을 "후보" 또는 "LOCK 변경"으로 적음 | Owner 결정(2026-11-24)이 대체: `carelog-architecture@aba36e3` 분석 README L41·L55, `carelog-fe@963f173` README "Where Carelog differs" 표와 그 아래 문단 | B. 원문은 v1.0.0 동결이라 고치지 않고 이 차이만 기록한다 |
| `docs/02` §10.4·`fin-result-d`: 결과 화면의 "확인할 것"은 읽기 전용 Status Display | 현재 FE는 결과 화면에서 체크 상태를 바꿀 수 있다(`ko.ts` "직접 확인한 상태를 표시할 수 있어요") | 기능 계약이 우선(`AI_START_HERE` §3-1). 시안 ①은 Checkbox로 그렸다. 어느 쪽이 맞는지는 D-6 |

### 3.3 경계 검증 [사실, 시안 렌더]

`mockups/_shared/boundary-metrics.json`. Finance는 ①·②, Carelog는 ④·⑥을 각 10개 폭에서 렌더했다.

| 폭×높이 | Finance 내비 | Finance 저널 pane | Finance 결과 보조 열 | Carelog 내비 | Carelog pane |
|---|---|---|---|---|---|
| 767×1024 | 없음(안쪽 화면) / 하단 | 1 | 없음 | 하단 101 | 1 |
| 768×1024 | 상단 | 1 | 없음 | 레일 80 | 1 |
| 1023×768 | 상단 | 1 | 없음 | — | — |
| 1024×768 | 레일 224 | 2 | 없음 | 레일 | 1 |
| 1024×1366 | 상단 | 1 | 없음 | 레일 | 1 |
| 1099×800 / 1100×800 | 레일 | 2 | 없음 | 레일 | 1 / 1 |
| 1179×800 / 1180×800 | — | — | — | 레일 | **1 / 2** |
| 1180×1366 | — | — | — | 레일 | 1 (세로라서) |
| 1279×1366 / 1280×1366 | 상단 / **레일** | 1 / 2 | 없음 / 있음 | — | — |
| 1280×800 | 레일 | 2 | 있음 | 레일 | 2 |

실제 앱(고정 HEAD)도 같은 폭에서 캡처했다. Finance는 F-1 한 곳만 계약과 다르고, Carelog는 1179/1180 전환이 계약과 같다(`app-captures/carelog/customer-detail-1179x800.png`, `-1180x800.png`).

## 4. Layout Primitive (C, 후보 이름)

토큰이 아니라 **역할 이름**이다. 값은 제품 Host가 정한다. 두 제품 구현에 이미 같은 역할이 있어서 이름만 맞추면 된다.

| Primitive | 역할 | Finance 값 (A) | Carelog 값 (A) | 분류 |
|---|---|---|---|---|
| AppHost | 앱 전체 틀과 최대 폭 | 최대 1360 | 최대 없음 | C(이름) / A(값) |
| PrimaryNav | 주 내비. `placement = bottom / top / rail` | 위 §3.1 | bottom / rail | C(이름) / A(값) |
| ContentHost | 한 열의 읽기 폭. `measure = reading / result / readable / scan` | 660 / 760 | 520·560 / 720 | C(이름) / A(값). Carelog scan 720은 아래 참고 |
| PaneGroup | 목록·상세 또는 원본·작성. `active = Host 판정` | 승인된 2쌍 | 가용 1100 + 가로 | C(이름) / A(조건) |
| AsideColumn | 본문 옆 보조 열. 본문 끝에 있던 블록만 옮긴다 | 결과 화면 "기록으로 이어가기" 300, 1280 이상 | 쓰지 않음(상세 pane 안은 한 열) | **D-10**(Finance 한정, Host 계약에 없는 구성). §5.3 |
| StickyAction | 고정 하단 행동 1줄 | 폰·태블릿의 결과·작성 | 쓰지 않음 | A |

- **Carelog scan 폭 720 [권고, A]**: 현재 목록·일정은 폭 제한이 없어서 1024~1179에서 행이 944px까지 늘어나고 시각이 오른쪽 끝으로 간다(`app-captures/carelog/schedule.png`). README는 읽기 폭 520·560만 적고 scan 폭은 정하지 않았다. 720으로 묶으면 시각·제목·상태가 한눈에 들어온다. 트레이드오프: 1024 가로에서 오른쪽 여백이 생긴다. 2-pane이 켜지는 1180부터는 영향이 없다.

## 5. List / Detail

### 5.1 공통 규칙 (C)

| 항목 | 규칙 | 근거 |
|---|---|---|
| 좁은 폭 | 목록과 상세를 한 번에 하나만 보여 준다. 상세에서 뒤로 가면 목록의 스크롤 위치와 필터가 그대로다 | `docs/02` §8 "목록 + 상세" |
| 넓은 폭 | 목록·상세를 나란히. 선택 행은 `brand.subtle` 면 + `aria-current`. 테두리·그림자 없음 | `docs/02` §4·§10.2 |
| 선택 없음 | 상세 자리에 한 문장 안내. Strong 없음 | 두 제품 현행과 같다 |
| 상세의 뒤로 가기 | 나란히 보일 때는 숨긴다. 목록이 옆에 있어서 뜻이 없다 | 현재 Finance는 2-pane에서도 뒤로 화살표와 "기록 상세" 제목 줄이 남는다(`app-captures/finance/journal-detail.png`) |
| 제목 | 화면에 `page-title`은 하나. 나란히 보일 때 목록 제목은 `section-title`로 낮춘다 | `docs/06` H1 |
| 회전 | 선택이 있으면 좁아질 때 상세를 보여 준다. 넓어지면 목록과 그 상세가 함께 보인다. 작성 중 입력은 유지한다 | Finance는 URL(`/journal/:id`)이 선택을 가진다. Carelog는 `adaptiveCustomerId` |
| 스크롤 | 목록 열만 따로 스크롤하고 상세는 문서 스크롤을 쓴다 | 시안 구현. FE 구조는 Host 결정 |

### 5.2 제품별 (A)

- Finance: Journal List·Detail(①열 0.38fr, 최소 300), Original·Retrospective(1:1). 그 밖의 화면은 한 열이다.
- Carelog: 일정·고객 루트에서 목록 + 선택 상세. 상세 pane 안은 한 열, 최대 640.

### 5.3 Finance 결과 화면의 보조 열 (D-10, 결정 전 시안)

이 구성은 `nav-map.md`·`route-architecture.md` §7·UX-Δ §7.1 어디에도 없는 새 구성이라 Owner 결정이 필요하다. 2-pane 금지에 걸리지 않는다고 보는 이유는 다음과 같다. 보조 열에는 본문 끝에 있던 "기록으로 이어가기" 블록만 들어간다. 질문과 결과를 나누지 않고, 새 정보도 없다. `nav-map.md`의 "Desktop 최대 2 major surfaces"와 `docs/04` §4의 "본문 | 보조" 안에 있다. 1280 미만에서는 본문 끝과 고정 하단으로 돌아간다. **Owner가 2-pane으로 본다면 뺀다.** 보조 열을 없애면 태블릿 구성(한 열 + 고정 하단)이 그대로 데스크톱에 쓰이고, 다른 시안과 문서에는 영향이 없다.

## 6. Dialog / Sheet, Form, 밀도, Touch Target

| 영역 | 계약 | 분류 |
|---|---|---|
| 확인 대화상자 | `docs/05` §3 그대로. 폰은 화면 가운데 패널(좌우 16), 태블릿 이상은 최대 440. 전체 화면 모달은 쓰지 않는다 | A(명세 유지) |
| Bottom Sheet | 폰에서 파괴적이지 않은 선택(기록 유형 고르기 등)에만. 768 이상에서는 같은 내용을 Dialog로. 반경 `radius.sheet` 24 | C. 명세가 캔버스 v1.1에만 있다(FD-1, FD-7) |
| Carelog 기록 완료 확인 | 인라인 `inset` 패널 유지. 새 Dialog를 만들지 않는다 | A(현행 구조) |
| 이탈 보호 | Finance는 지금 `window.confirm`이다. Dialog로 바꾸려면 라우팅 변경이 필요해서 디자인 PR과 분리한다 | D-7 아님. 구현 과제로 F1-05에 둔다 |
| Form 폭 | 한 열. 폰은 전폭, 태블릿 이상은 ContentHost 폭 안. 라벨은 필드 위 | C(Field) |
| Form 저장 버튼 | 폰은 전폭. 768 이상은 내용 폭(최소 200) 왼쪽 정렬(`docs/05` §2 "데스크톱은 내용 폭 왼쪽 정렬") | A |
| 키보드(폰) | 입력이 Key인 화면(검토 시작, 복기)은 저장 버튼을 고정하지 않고 흐름 안에 둔다. 고정 하단을 쓰는 화면은 `scroll-padding-bottom` 96 | A(`docs/04` §5) |
| 입력 글자 | 16px 고정(iOS 확대 방지) | A |
| Safe Area | 하단 내비·고정 하단·작성 바에 `env(safe-area-inset-bottom)`. 가로 레일은 왼쪽 inset | A |
| 밀도 | Finance Comfortable, Carelog Compact. 사용자 전환 없음. 반응형과 무관 | A / B(Carelog Compact 적용) |
| Touch Target | WCAG 2.2 AA 2.5.8은 24px. Ranikun 계약은 44px(시각 36 + 위아래 4 확장). 시안 렌더 134회(화면 94 + 경계 40)에서 44 미만 0건 | A |
| 포커스 | 2px `color.focus`, offset 2 | A |

## 7. 아직 분류되지 않았던 항목의 분류와 Owner Gate

| ID | 항목 | 분류 | 근거 | 권고 |
|---|---|---|---|---|
| D-1 | Compact 줄높이 9개 | **D(확인 한 줄)** | 정본은 9개를 모두 정했다(`docs/02` §6 L81-89, `tokens/density.compact.tokens.json`, `preview/tokens.css`). 그런데 `carelog-fe` README는 "reading 24와 body 21만 확정, 나머지는 Comfortable 값 유지"라고 적는다 | "정본 값이 확정값"이라고 확인. 시안은 정본 값으로 그렸다 |
| D-2 | 레일 D-4(PLANNED = info) | **A** | `carelog-fe@963f173` README "Status rails" 행이 이미 현행 계약이고 `docs/02` §1 L17과 같다 | 유지 |
| D-3 | Dark Teal 본값(#3cb7ab / #8fe0d6 / #0f2e2c) | **C** | 정본 `brand.carelog-b.dark`는 "신규 후보"(`docs/02` L30). carelog-fe는 토큰만 갖고 노출하지 않는다 | v1.1에서 확정. 시안 Dark는 이 값으로 그렸다 |
| D-4 | Dark on-strong #0b1220, Dark brand-border | **C**(값) / **D**(노출) | carelog-fe README가 "proposals (unconfirmed)"라고 적는다 | on-strong은 채택 권고(7.63:1). brand-border는 쓰는 곳이 생길 때 정한다 |
| D-5 | Dark 노출 | **D** | 두 제품 모두 `.dark`를 켜는 코드가 없다 [사실: grep] | C-1을 고친 뒤 결정 |
| D-6 | 결과 화면 "확인할 것"을 바꿀 수 있게 둘지 | **D** | §3.2 마지막 행 | 바꿀 수 있게 유지(현행). 맥락 요약이 바로 갱신된다 |
| D-7 | Carelog 폰 하단 내비의 계정 줄을 탭 줄로 합치기(101 → 57px) | **D** | 계정 기능 위치는 제품 결정. 320×640에서 본문 높이가 539px다. 실제 앱은 타임라인 첫 행이 잘리고(`app-captures/carelog/customer-detail-320x640.png`), 시안은 Key Block의 "일정 추가"가 첫 화면 밖이다(`carelog/04-customers/captures/detail-320x640-light.png`). 비교 시안 `05-schedule/captures/agenda-one-row-390x844-light.png` | 합치기 권고. 기능과 `BOTTOM_TABS`는 그대로다 |
| D-8 | Finance 후속 질문(시안 ③) | **D** | 경로·UX 계약·Review API가 없다. 정책 Matrix에 "반복·후속 분석"만 있다 | F1-02 §5 |
| D-9 | 문구 변경 | **D**(변경할 때) | ① "AI와 복기하기"(`ko.ts:542`. `nav-map.md`는 "복기하기"): 시안은 현행 유지. ② 검토 시작 제목: 시안은 규범 시안 문구 "무엇을 검토할까요?", 현행은 "지금 판단하려는 것을 검토해 보세요". ③ 결과 화면 고정 하단 문장 "작성 화면에서 다시 고칠 수 있어요"(규범 `fin-result-d` 문구)는 작성 화면을 보지 못한 상태의 기능 약속이다 | ①은 유지. ②③은 Owner·카피 확인 뒤 적용 |
| D-10 | Finance 결과 화면 1280 이상 보조 열 | **D** | §5.3. Host 계약에 없는 구성 | 채택 권고. 거절되면 한 열 + 고정 하단 유지 |

## 8. 검증 범위

| 항목 | 결과 | 종류 |
|---|---|---|
| 시안 렌더 94장(6화면 12뷰, 320~1440, Light·Dark, 상태 8)과 경계 40회 | 가로 넘침 0, 44 미만 0, 12px 미만 0, 대비 미달 0, 글자 잘림 0, Strong 2개 이상 0, `page-title` 2개 이상 0, `key-value` 2개 이상 0, 카드 안 카드 0 | 정적 렌더, 자체 검증 |
| 경계 40회의 내비 위치·pane 수 | §3.3 | 정적 렌더, 자체 검증 |
| 독립 검수 1차 지적(R-2) | 주간 띠 셀이 1180×800 2단에서 41px였다. 띠를 페이지 가장자리까지 넓혀 고쳤고 320·1180 일정 렌더를 추가했다(320에서 45px) | 수정 뒤 재측정 |
| 실제 앱 342장(Finance 9경로, Carelog 9경로 × 17폭 + Dark 2폭) | F-1, F-2, C-1, C-2 | 정적 렌더, fixture 데이터 |
| 키보드·포커스 순서, 스크린리더, 200% 확대, 실기기, 회전 동작 | **NOT_RUN** | — |
| Carelog 인증 화면, Assistant 화면 | **NOT_VERIFIED** (fixture 인증 상태에서 일정으로 넘어가거나 진입 조건이 있어 직접 보지 못했다) | — |
| 독립 검수 | F1-00에 기록 | 별도 Reviewer |
