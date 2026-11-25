# ① Finance 검토 시작·결과

- 종류: **시안**(F1이 새로 만든 HTML 렌더). 실제 앱 캡처가 아니다.
- 제품·경로: `/app` (view=start), `/app/ask?ref=` (view=result)
- 재료: design-system@021e40e (tokens.css, ds.css) + `_shared/f1.css`를 `index.html`에 모두 인라인. CDN 없음. 폰트는 `_shared/PretendardVariable.subset.woff2`.
- 데이터: 예시 데이터만(합성 이름, 가상 종목).
- 보는 법: `index.html?view=<뷰>&mode=dark`. 상태: `?state=loading`, `?state=partial`.

## 화면 유형과 강도

| 항목 | 내용 |
|---|---|
| 화면 유형(`docs/02` §10.5) | 입력(시작) · 결과(결과) |
| Key 영역 | 시작: 질문 입력 1곳. 결과: 질문(머리 제목) + 함께 남길 사실 고르기 |
| Strong 근거(`docs/02` §7) | 시작 "검토 시작": 이 화면의 하나뿐인 행동이고, 그걸 하러 왔고, 반복해도 도움이 크다. 결과 "판단 기록 남기기": 결과를 기록으로 넘기는, 화면을 끝내는 행동이다(UX-Δ §1 핵심 task) |

## 기기별로 달라지는 것

| 기기 | 위계·배치·내비 | 입력 방식·이유 |
|---|---|---|
| 폰 320~393 | 시작은 하단 탭. 결과는 뒤로 가기만 있고 탭이 없다. Strong은 고정 하단 전폭 1줄 | 엄지 영역. 키보드가 올라오는 시작 화면은 버튼을 고정하지 않는다 |
| 태블릿 세로 768 | 상단 내비 한 줄(제품 이름·검토/저널·로그인). 읽기 폭 660/760 가운데. 결과의 고정 하단은 "무엇이 남는지" 문장 + 내용 폭 버튼 | 터치와 키보드 혼용. 전폭 버튼을 쓰지 않는다 |
| 태블릿 가로 1024×768 | 왼쪽 레일 224. 결과는 한 열 + 고정 하단 유지 | 가로 폭이 800이라 보조 열을 두지 않는다 |
| 데스크톱 1440 | 레일 + 본문 760 + 보조 열 300("기록으로 이어가기", sticky). 고정 하단 없음 | 질문·결과 2-pane이 아니다. 본문 끝 블록만 옆으로 옮겼다(F1-01 §5.3) |

## 정본에 없는 요소 (C)

- C: Navigation 상단형(`data-f1="C:Navigation-top"`)
- C: Notice(부분 결과)
- C: 단계 목록(로딩)
- FN-12 반영: "기록으로 이어가기" 제목 아래 판단 기록 카드와 학습 노트 카드
- FN-1·FN-2 반영: 사실 섹션 머리 "기록에 함께 남길 사실 1개 고름", 고정 하단 문장

## Owner 결정이 필요하거나 현행과 다른 곳 (D·차이)

- "확인할 것"을 결과 화면에서 바꿀 수 있게 그렸다(현행 FE 동작). 규범 시안 `fin-result-d`는 읽기 전용이다(F1-01 F1-G3)
- **F1-G8**: "검토 맥락도 판단 기록에 함께 남기기" Checkbox, 사실 행 "함께 남기기" 칩, 저장 범위 요약을 결과 화면에 둔 것은 규범 시안 `fin-result-d`와 `docs/05` §3b를 따른 것이다. UX-Δ §5·§7.1과 현행 FE는 이 선택을 작성 화면에 둔다. Owner 승인 전에는 구현하지 않는다(F1-01 §7.2)
- **F1-G7** 거절 시 대체 구성: `captures/result-noaside-1440x900-light.png` (`?aside=off`)
- **F1-G6**: 제목 "무엇을 검토할까요?"는 규범 시안 문구다. 현행은 "지금 판단하려는 것을 검토해 보세요". 바꾸려면 Owner·카피 확인이 필요하다
- **F1-G6**: 고정 하단 문장 "작성 화면에서 다시 고칠 수 있어요"는 규범 시안 `fin-result-d`의 문구다. 작성 화면을 보지 못했으므로 기능 약속으로 읽지 않는다
- **F1-G7**: 1280 이상의 보조 열은 Host 계약에 없는 구성이다. 결정 전 시안이다
- 입력 도움말 "질문은 이 탭에만 머물러요. 새로고침하면 다시 시작해요."와 예시 질문 머리 문구는 시안이다. 예시 질문 3개는 현행 문장 그대로다

## 캡처 (모두 시안, 예시 데이터, 기준 design-system@021e40e (tokens.css, ds.css))

| 파일 | 뷰 | 폭×높이 | 방향 | 모드 |
|---|---|---|---|---|
| `boundary-result-1279x1366.png` | result | 1279×1366 | 세로 | Light |
| `boundary-result-1280x1366.png` | result | 1280×1366 | 세로 | Light |
| `result-1024x768-light.png` | result | 1024×768 | 가로 | Light |
| `result-1440x900-light.png` | result | 1440×900 | 가로 | Light |
| `result-320x640-dark.png` | result | 320×640 | 세로 | Dark |
| `result-320x640-light.png` | result | 320×640 | 세로 | Light |
| `result-375x812-dark.png` | result | 375×812 | 세로 | Dark |
| `result-375x812-light.png` | result | 375×812 | 세로 | Light |
| `result-390-full-light.png` | result | 390 × 전체 높이 | 세로 | Light |
| `result-390x844-dark.png` | result | 390×844 | 세로 | Dark |
| `result-390x844-light.png` | result | 390×844 | 세로 | Light |
| `result-393x852-dark.png` | result | 393×852 | 세로 | Dark |
| `result-393x852-light.png` | result | 393×852 | 세로 | Light |
| `result-768x1024-light.png` | result | 768×1024 | 세로 | Light |
| `result-loading-390x844-light.png` | result | 390×844 | 세로 | Light |
| `result-partial-390x844-light.png` | result | 390×844 | 세로 | Light |
| `start-1024x768-light.png` | start | 1024×768 | 가로 | Light |
| `start-1440x900-dark.png` | start | 1440×900 | 가로 | Dark |
| `start-1440x900-light.png` | start | 1440×900 | 가로 | Light |
| `start-390-full-light.png` | start | 390 × 전체 높이 | 세로 | Light |
| `start-390x844-light.png` | start | 390×844 | 세로 | Light |
| `start-768x1024-light.png` | start | 768×1024 | 세로 | Light |
| `result-noaside-1440x900-light.png` | result (F1-G7 거절 시 대체 구성) | 1440×900 | 가로 | Light |

자동 측정 결과는 `../../_shared/render-metrics.json`에 있다. 이 화면의 모든 캡처와 경계 렌더에서 가로 넘침·44 미만 대상·12px 미만 글자·대비 미달·글자 잘림이 0건이다(정적 렌더, 자체 검증).
