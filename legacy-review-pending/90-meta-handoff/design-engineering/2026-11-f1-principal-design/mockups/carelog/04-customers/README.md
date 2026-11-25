# ④ Carelog 고객 목록·상세

- 종류: **시안**(F1이 새로 만든 HTML 렌더). 실제 앱 캡처가 아니다.
- 제품·경로: `/app/customers` (view=list), `/app/customers/:id` (view=detail)
- 재료: design-system@021e40e (tokens.css, ds.css) + `_shared/f1.css`를 `index.html`에 모두 인라인. CDN 없음. 폰트는 `_shared/PretendardVariable.subset.woff2`.
- 데이터: 예시 데이터만(합성 이름, 가상 종목).
- 보는 법: `index.html?view=<뷰>&mode=dark`. 상태: 없음.

## 화면 유형과 강도

| 항목 | 내용 |
|---|---|
| 화면 유형(`docs/02` §10.5) | 목록 · 업무 상세 |
| Key 영역 | 목록: 없음. 상세: 다음 일정(Key Block: 상태 + 제목 + 시각 key-value) |
| Strong 근거(`docs/02` §7) | 0개. 목록 "고객 추가"와 상세 "일정 추가"는 Subtle(자주 여는 조회 화면) |

## 기기별로 달라지는 것

| 기기 | 위계·배치·내비 | 입력 방식·이유 |
|---|---|---|
| 폰 320~393 | 하단 내비(탭 2 + 계정 줄, 101px)가 상세에도 있다. 한 열 | 320×640에서 다음 일정 시각까지는 보이고 Key Block의 "일정 추가"가 첫 화면 밖이다 → F1-G4의 근거 |
| 태블릿 세로 768 | 레일 80. 목록 720, 상세 520 | 세로는 1단 |
| 가로 1024×768 | 레일. 가용 폭 944라서 1단. 상세 560 | 1100 미만 |
| 가로 1440 | 목록 | 상세. 선택 행 brand.subtle + aria-current. 상세 최대 640, 뒤로 가기 숨김 | 가용 1360, 가로 |

## 정본에 없는 요소 (C)

- C: List/Detail 규칙

## Owner 결정이 필요하거나 현행과 다른 곳 (D·차이)

- "AI에게 물어보기"는 현행 유지, 계약 확인 대기(`.anno`)
- 고객 메모 섹션은 규범 순서(다음 일정 → 메모 → 타임라인)대로 그렸다. 현재 화면에서는 보지 못했다
- **F1-G9**: 날짜·시각을 24시간제 `M월 D일 (요일) HH:mm`으로 그렸다(현행은 오전/오후)

## 캡처 (모두 시안, 예시 데이터, 기준 design-system@021e40e (tokens.css, ds.css))

| 파일 | 뷰 | 폭×높이 | 방향 | 모드 |
|---|---|---|---|---|
| `boundary-detail-1179x800.png` | detail | 1179×800 | 가로 | Light |
| `boundary-detail-1180x800.png` | detail | 1180×800 | 가로 | Light |
| `detail-1024x768-light.png` | detail | 1024×768 | 가로 | Light |
| `detail-1440x900-dark.png` | detail | 1440×900 | 가로 | Dark |
| `detail-1440x900-light.png` | detail | 1440×900 | 가로 | Light |
| `detail-320x640-dark.png` | detail | 320×640 | 세로 | Dark |
| `detail-320x640-light.png` | detail | 320×640 | 세로 | Light |
| `detail-375x812-dark.png` | detail | 375×812 | 세로 | Dark |
| `detail-375x812-light.png` | detail | 375×812 | 세로 | Light |
| `detail-390-full-light.png` | detail | 390 × 전체 높이 | 세로 | Light |
| `detail-390x844-light.png` | detail | 390×844 | 세로 | Light |
| `detail-393x852-dark.png` | detail | 393×852 | 세로 | Dark |
| `detail-393x852-light.png` | detail | 393×852 | 세로 | Light |
| `detail-768x1024-light.png` | detail | 768×1024 | 세로 | Light |
| `list-1024x768-light.png` | list | 1024×768 | 가로 | Light |
| `list-1440x900-light.png` | list | 1440×900 | 가로 | Light |
| `list-390-full-light.png` | list | 390 × 전체 높이 | 세로 | Light |
| `list-390x844-dark.png` | list | 390×844 | 세로 | Dark |
| `list-390x844-light.png` | list | 390×844 | 세로 | Light |
| `list-768x1024-light.png` | list | 768×1024 | 세로 | Light |

자동 측정 결과는 `../../_shared/render-metrics.json`에 있다. 이 화면의 모든 캡처와 경계 렌더에서 가로 넘침·44 미만 대상·12px 미만 글자·대비 미달·글자 잘림이 0건이다(정적 렌더, 자체 검증).
