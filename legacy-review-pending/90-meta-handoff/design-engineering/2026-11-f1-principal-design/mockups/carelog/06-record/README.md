# ⑥ Carelog 업무 기록(일정 상세)

- 종류: **시안**(F1이 새로 만든 HTML 렌더). 실제 앱 캡처가 아니다.
- 제품·경로: `/app/events/:id` (view=overdue: PLANNED·시각 지남, view=occurred: OCCURRED)
- 재료: design-system@021e40e (tokens.css, ds.css) + `_shared/f1.css`를 `index.html`에 모두 인라인. CDN 없음. 폰트는 `_shared/PretendardVariable.subset.woff2`.
- 데이터: 예시 데이터만(합성 이름, 가상 종목).
- 보는 법: `index.html?view=<뷰>&mode=dark`. 상태: `?state=confirm` (view=overdue).

## 화면 유형과 강도

| 항목 | 내용 |
|---|---|
| 화면 유형(`docs/02` §10.5) | 업무 처리 · 기록 상세 |
| Key 영역 | 상태 + 시각(Key Block) |
| Strong 근거(`docs/02` §7) | 정리 필요일 때만 "기록 완료" 1개: 오늘 닫아야 하는 하나뿐인 일이다. 기록됨은 0개 |

## 기기별로 달라지는 것

| 기기 | 위계·배치·내비 | 입력 방식·이유 |
|---|---|---|
| 폰 | Key Block 안 Strong(첫 화면 안). 누르면 그 자리에서 확인 패널(실제 시각)이 펼쳐진다. 고정 하단 없음 | 하단 내비 101px와 고정 하단이 겹치지 않게 |
| 태블릿 세로 768 | 레일. 한 열 520 | — |
| 가로 1024×768 | 1단 560 | — |
| 가로 1440 | 일정(선택 행 표시) | 상세. 닫고 바로 다음 일정을 고른다 | 연속 처리 |

## 정본에 없는 요소 (C)

- C: Field(실제 시각)

## Owner 결정이 필요하거나 현행과 다른 곳 (D·차이)

- 규범 `docs/02` §10.5는 "기록 완료 Strong(하단 고정)"이다. Carelog Host는 폰 전 화면에 하단 내비가 있어 인라인으로 뒀다(F1-01 §3.2)
- 확인 패널의 "원래 예정 시각은 기록에 보존됩니다."는 현행 문장 그대로다. Key Block의 안내 문장("예정 시각이 지났어요. …")은 시안이다
- 취소 처리는 Text 중립. 새 확인 Dialog를 넣지 않았다(현행). 취소 복구는 그리지 않았다(계약 위치 NOT_VERIFIED)
- "미리 준비하기", "기록에 대해 질문"은 현행 유지, 계약 확인 대기
- **F1-G9**: 날짜·시각을 24시간제로 그렸다(현행은 오전/오후)

## 캡처 (모두 시안, 예시 데이터, 기준 design-system@021e40e (tokens.css, ds.css))

| 파일 | 뷰 | 폭×높이 | 방향 | 모드 |
|---|---|---|---|---|
| `occurred-1024x768-light.png` | occurred | 1024×768 | 가로 | Light |
| `occurred-1440x900-dark.png` | occurred | 1440×900 | 가로 | Dark |
| `occurred-1440x900-light.png` | occurred | 1440×900 | 가로 | Light |
| `occurred-390-full-light.png` | occurred | 390 × 전체 높이 | 세로 | Light |
| `occurred-390x844-light.png` | occurred | 390×844 | 세로 | Light |
| `occurred-768x1024-light.png` | occurred | 768×1024 | 세로 | Light |
| `overdue-1024x768-light.png` | overdue | 1024×768 | 가로 | Light |
| `overdue-1440x900-light.png` | overdue | 1440×900 | 가로 | Light |
| `overdue-390-full-light.png` | overdue | 390 × 전체 높이 | 세로 | Light |
| `overdue-390x844-dark.png` | overdue | 390×844 | 세로 | Dark |
| `overdue-390x844-light.png` | overdue | 390×844 | 세로 | Light |
| `overdue-768x1024-light.png` | overdue | 768×1024 | 세로 | Light |
| `overdue-confirm-1440x900-light.png` | overdue | 1440×900 | 가로 | Light |
| `overdue-confirm-390x844-light.png` | overdue | 390×844 | 세로 | Light |
| `overdue-1180x800-light.png` | overdue | 1180×800 | 가로 | Light |

자동 측정 결과는 `../../_shared/render-metrics.json`에 있다. 이 화면의 모든 캡처와 경계 렌더에서 가로 넘침·44 미만 대상·12px 미만 글자·대비 미달·글자 잘림이 0건이다(정적 렌더, 자체 검증).
