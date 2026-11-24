# ⑤ Carelog 일정 조회·작성

- 종류: **시안**(F1이 새로 만든 HTML 렌더). 실제 앱 캡처가 아니다.
- 제품·경로: `/app/schedule` (view=agenda), `/app/events/new` (view=create)
- 재료: design-system@021e40e (tokens.css, ds.css) + `_shared/f1.css`를 `index.html`에 모두 인라인. CDN 없음. 폰트는 `_shared/PretendardVariable.subset.woff2`.
- 데이터: 예시 데이터만(합성 이름, 가상 종목).
- 보는 법: `index.html?view=<뷰>&mode=dark`. 상태: `?state=error` (view=create).

## 화면 유형과 강도

| 항목 | 내용 |
|---|---|
| 화면 유형(`docs/02` §10.5) | 목록 · 입력 |
| Key 영역 | 조회: 없음(정리 필요 안내와 상태 라벨만). 작성: 폼 |
| Strong 근거(`docs/02` §7) | 조회 0개("+ 일정" Subtle small: 훑어보러 오는 화면). 작성 "일정 추가" 1개 |

## 기기별로 달라지는 것

| 기기 | 위계·배치·내비 | 입력 방식·이유 |
|---|---|---|
| 폰 | 주간 띠(위 고정) → 정리 필요 안내 → 날짜별 카드. 행 = 레일 + 시각 + 제목 + 요약 + 고객 링크 + 상태. 하단 내비 | 시각을 행 맨 앞에 둬 세로로 훑는다 |
| 태블릿 세로 768 | 레일 80. 720 | — |
| 가로 1024×768 | 1단. 720 | 1100 미만 |
| 가로 1440 | 일정 | 폼(또는 선택 안내). 폼 최대 640 | — |

## 정본에 없는 요소 (C)

- C: Notice(정리 필요 안내, 저장 실패)
- C: Field
- C: Choice(추가 방식, native radio)

## Owner 결정이 필요하거나 현행과 다른 곳 (D·차이)

- 주간 띠는 페이지 좌우 여백까지 넓혔다. 7칸이 320과 1180 2단(목록 열 360)에서도 44 이상이 되게 하려는 것이다(320에서 45px)
- 정리 필요 안내 문장, 빈 선택 안내 문장은 시안이다
- 하단 내비 한 줄 비교안: `agenda-one-row-390x844-light.png` (`?nav=one-row`, F1-01 D-7)
- "+ 일정"을 Strong에서 Subtle로 낮췄다(현행 Strong)
- 일정 추가의 고객 선택 단계는 그리지 않았다. 시각 입력은 자리 표시다(Date/Time Picker는 CM-6)
- 행 전체가 일정 상세로 가는 대상이고 고객 이름은 따로 44px 링크다(현행 구조 유지)

## 캡처 (모두 시안, 예시 데이터, 기준 design-system@021e40e (tokens.css, ds.css))

| 파일 | 뷰 | 폭×높이 | 방향 | 모드 |
|---|---|---|---|---|
| `agenda-1024x768-light.png` | agenda | 1024×768 | 가로 | Light |
| `agenda-1440x900-light.png` | agenda | 1440×900 | 가로 | Light |
| `agenda-390-full-light.png` | agenda | 390 × 전체 높이 | 세로 | Light |
| `agenda-390x844-dark.png` | agenda | 390×844 | 세로 | Dark |
| `agenda-390x844-light.png` | agenda | 390×844 | 세로 | Light |
| `agenda-768x1024-light.png` | agenda | 768×1024 | 세로 | Light |
| `agenda-one-row-390x844-light.png` | agenda | 390×844 | 세로 | Light |
| `create-1024x768-light.png` | create | 1024×768 | 가로 | Light |
| `create-1440x900-light.png` | create | 1440×900 | 가로 | Light |
| `create-390-full-light.png` | create | 390 × 전체 높이 | 세로 | Light |
| `create-390x844-light.png` | create | 390×844 | 세로 | Light |
| `create-768x1024-light.png` | create | 768×1024 | 세로 | Light |
| `create-error-390-full-light.png` | create | 390 × 전체 높이 | 세로 | Light |
| `agenda-320x640-light.png` | agenda | 320×640 | 세로 | Light |
| `agenda-375x812-light.png` | agenda | 375×812 | 세로 | Light |
| `agenda-1180x800-light.png` | agenda | 1180×800 | 가로 | Light |

자동 측정 결과는 `../../_shared/render-metrics.json`에 있다. 이 화면의 모든 캡처와 경계 렌더에서 가로 넘침·44 미만 대상·12px 미만 글자·대비 미달·글자 잘림이 0건이다(정적 렌더, 자체 검증).
