# ② Finance 저널 목록·상세·복기

- 종류: **시안**(F1이 새로 만든 HTML 렌더). 실제 앱 캡처가 아니다.
- 제품·경로: `/app/journal` (view=list), `/app/journal/:id` (view=detail), `/app/journal/:id/review` (view=retro)
- 재료: design-system@021e40e (tokens.css, ds.css) + `_shared/f1.css`를 `index.html`에 모두 인라인. CDN 없음. 폰트는 `_shared/PretendardVariable.subset.woff2`.
- 데이터: 예시 데이터만(합성 이름, 가상 종목).
- 보는 법: `index.html?view=<뷰>&mode=dark`. 상태: 없음.

## 화면 유형과 강도

| 항목 | 내용 |
|---|---|
| 화면 유형(`docs/02` §10.5) | 목록 · 기록 상세 · 입력(복기) |
| Key 영역 | 목록: 없음. 상세: 판단 이유(reading). 복기: 복기 본문 입력 |
| Strong 근거(`docs/02` §7) | 목록 0개("새 기록" Subtle small). 상세 0개("AI와 복기하기" Subtle, 복기 섹션 맨 위: 반복해서 읽는 화면). 복기 "복기 저장": 이 화면의 하나뿐인 완료 행동 |

## 기기별로 달라지는 것

| 기기 | 위계·배치·내비 | 입력 방식·이유 |
|---|---|---|
| 폰 | 목록은 하단 탭, 상세·복기는 뒤로 가기. 상세 순서: 머리 → 판단 이유 → 복기 → 당시 검토 맥락 → 기록 정보. 복기는 원본 → 입력 → 저장(흐름 안) | 복기 입력은 키보드가 올라오므로 저장 버튼을 고정하지 않는다 |
| 태블릿 세로 768 | 상단 내비 유지. 한 열 660. 목록과 상세는 따로 | 세로에서는 나란히 보지 않는다(계약) |
| 태블릿 가로 1024×768 | 레일 + 목록 | 상세. 상세의 뒤로 가기 숨김, 목록 제목은 섹션 제목. 선택 행 brand.subtle + aria-current. 복기는 원본 | 새 복기 1:1 | 승인된 2-pane 두 가지 |
| 데스크톱 1440 | 같은 구성. 상세 최대 720 | — |

## 정본에 없는 요소 (C)

- C: List/Detail 규칙(선택 행, 상세 머리, 빈 선택 안내)
- C: Field(복기 폼 라벨·도움말)

## Owner 결정이 필요하거나 현행과 다른 곳 (D·차이)

- "AI와 복기하기"는 현행 문구 그대로다. `nav-map.md`는 "복기하기"(F1-01 F1-G6)
- 행 메타에 "복기 1건"을 넣었다. 목록 응답에 복기 수가 있는지 확인하지 못했다(없으면 뺀다)
- "복기는 평가가 아니에요" 두 문장은 시안 문구다. 현행 COPY 고지 원문을 써야 한다
- 수정·숨기기 행동은 넣지 않았다(계약 근거 미확인)

## 캡처 (모두 시안, 예시 데이터, 기준 design-system@021e40e (tokens.css, ds.css))

| 파일 | 뷰 | 폭×높이 | 방향 | 모드 |
|---|---|---|---|---|
| `boundary-detail-1023x768.png` | detail | 1023×768 | 가로 | Light |
| `boundary-detail-1024x768.png` | detail | 1024×768 | 가로 | Light |
| `detail-1024x768-light.png` | detail | 1024×768 | 가로 | Light |
| `detail-1440x900-dark.png` | detail | 1440×900 | 가로 | Dark |
| `detail-1440x900-light.png` | detail | 1440×900 | 가로 | Light |
| `detail-390-full-light.png` | detail | 390 × 전체 높이 | 세로 | Light |
| `detail-390x844-light.png` | detail | 390×844 | 세로 | Light |
| `detail-768x1024-light.png` | detail | 768×1024 | 세로 | Light |
| `list-1024x768-light.png` | list | 1024×768 | 가로 | Light |
| `list-1440x900-light.png` | list | 1440×900 | 가로 | Light |
| `list-390-full-light.png` | list | 390 × 전체 높이 | 세로 | Light |
| `list-390x844-light.png` | list | 390×844 | 세로 | Light |
| `list-768x1024-light.png` | list | 768×1024 | 세로 | Light |
| `retro-1024x768-light.png` | retro | 1024×768 | 가로 | Light |
| `retro-1440x900-light.png` | retro | 1440×900 | 가로 | Light |
| `retro-390-full-light.png` | retro | 390 × 전체 높이 | 세로 | Light |
| `retro-390x844-dark.png` | retro | 390×844 | 세로 | Dark |
| `retro-390x844-light.png` | retro | 390×844 | 세로 | Light |
| `retro-768x1024-light.png` | retro | 768×1024 | 세로 | Light |

자동 측정 결과는 `../../_shared/render-metrics.json`에 있다. 이 화면의 모든 캡처와 경계 렌더에서 가로 넘침·44 미만 대상·12px 미만 글자·대비 미달·글자 잘림이 0건이다(정적 렌더, 자체 검증).
