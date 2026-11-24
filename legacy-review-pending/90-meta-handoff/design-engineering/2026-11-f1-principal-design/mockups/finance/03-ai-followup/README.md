# ③ Finance AI 대화(후속 질문) — 전체가 D

- 종류: **시안**(F1이 새로 만든 HTML 렌더). 실제 앱 캡처가 아니다.
- 제품·경로: 경로 없음. 검토 결과에서 들어온다고 가정
- 재료: design-system@021e40e (tokens.css, ds.css) + `_shared/f1.css`를 `index.html`에 모두 인라인. CDN 없음. 폰트는 `_shared/PretendardVariable.subset.woff2`.
- 데이터: 예시 데이터만(합성 이름, 가상 종목).
- 보는 법: `index.html?view=<뷰>&mode=dark`. 상태: `?state=streaming`.

## 화면 유형과 강도

| 항목 | 내용 |
|---|---|
| 화면 유형(`docs/02` §10.5) | 입력(묻기) |
| Key 영역 | 내 질문(reading), 답의 구조(자료·해석·모르는 것) |
| Strong 근거(`docs/02` §7) | "질문 보내기": 이 화면에 온 이유가 묻기다. 기록으로 이어가기는 Text(폰)·Subtle(보조 열) |

## 기기별로 달라지는 것

| 기기 | 위계·배치·내비 | 입력 방식·이유 |
|---|---|---|
| 폰 | 뒤로 가기 + "기록으로 이어가기" Text. 턴이 세로로 쌓인다. 작성 바는 고정 하단(safe area) | 한 손. 답 아래 행동은 Text 작은 버튼 |
| 태블릿 세로 768 | 상단 내비 유지. 한 열 760 | — |
| 태블릿 가로 1024×768 | 레일 + 한 열 | — |
| 데스크톱 1440 | 본문 760 + 보조 열 300(기록으로 이어가기, 고지). 작성 바는 본문 열 폭 | — |

## 정본에 없는 요소 (C)

- C: 단계 목록(진행 중)

## Owner 결정이 필요하거나 현행과 다른 곳 (D·차이)

- **D-10**: 1280 이상의 보조 열은 ①과 같은 결정에 묶인다
- 기능·경로·Review API·사용량 표시·대화 보존이 모두 Owner 결정(F1-01 D-8, F1-02 §5)
- 턴 2의 "답하지 않는 질문이에요"와 대체 제안 버튼 문구는 시안이다. 정책 문구는 PolicyGuard·카피 가이드 소유

## 캡처 (모두 시안, 예시 데이터, 기준 design-system@021e40e (tokens.css, ds.css))

| 파일 | 뷰 | 폭×높이 | 방향 | 모드 |
|---|---|---|---|---|
| `thread-1024x768-light.png` | thread | 1024×768 | 가로 | Light |
| `thread-1440x900-light.png` | thread | 1440×900 | 가로 | Light |
| `thread-390-full-light.png` | thread | 390 × 전체 높이 | 세로 | Light |
| `thread-390x844-dark.png` | thread | 390×844 | 세로 | Dark |
| `thread-390x844-light.png` | thread | 390×844 | 세로 | Light |
| `thread-768x1024-light.png` | thread | 768×1024 | 세로 | Light |
| `thread-streaming-1440x900-light.png` | thread | 1440×900 | 가로 | Light |
| `thread-streaming-390-full-light.png` | thread | 390 × 전체 높이 | 세로 | Light |

자동 측정 결과는 `../../_shared/render-metrics.json`에 있다. 이 화면의 모든 캡처와 경계 렌더에서 가로 넘침·44 미만 대상·12px 미만 글자·대비 미달·글자 잘림이 0건이다(정적 렌더, 자체 검증).
