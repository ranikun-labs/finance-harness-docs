# F1 시안 6묶음

Finance 3개, Carelog 3개. 각 폴더의 `index.html`은 Foundation v1.0.0 `tokens.css`·`ds.css`(design-system@021e40e)와 `_shared/f1.css`를 인라인한 자체 포함 HTML이다. 외부 CDN을 쓰지 않는다.

| # | 폴더 | 화면 | 분류 |
|---|---|---|---|
| ① | `finance/01-review` | 검토 시작·결과 | A + C + D(F1-G3·G6·G7·G8) |
| ② | `finance/02-journal` | 저널 목록·상세·복기 | A + C (문구는 F1-G6) |
| ③ | `finance/03-ai-followup` | AI 대화(후속 질문) | **D 전체(F1-G5)** |
| ④ | `carelog/04-customers` | 고객 목록·상세 | A·B + C + D(F1-G4·G9) |
| ⑤ | `carelog/05-schedule` | 일정 조회·작성 | A·B + C + D(F1-G4·G9) |
| ⑥ | `carelog/06-record` | 업무 기록(일정 상세) | A·B + C + D(F1-G9) |

- `_shared/f1.css`: 시안용 Adaptive 계층(인라인된 것과 같은 내용). FE 코드가 아니다.
- `_shared/PretendardVariable.subset.woff2`: Pretendard Variable을 시안에 쓰인 글자만 남겨 줄인 파일(94KB). SIL OFL 1.1, 라이선스는 `_shared/Pretendard-OFL-1.1.txt`. 원본은 design-system `preview/PretendardVariable.woff2`.
- `_shared/render-metrics.json`, `boundary-metrics.json`: Playwright 측정값(넘침, 조작 영역, 글자 크기, 대비, 내비 위치, pane 수).
- `.anno`("검수 메모") 요소와 `data-f1` 속성은 시안 주석이다. 제품 UI로 구현하지 않는다.
- 렌더 조건: Chromium, deviceScaleFactor 1, 언어 ko-KR. Dark는 `data-mode="dark"`.
