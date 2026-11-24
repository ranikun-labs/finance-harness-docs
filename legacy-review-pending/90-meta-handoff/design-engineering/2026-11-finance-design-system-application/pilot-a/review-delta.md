# PR #51 Delta 리뷰 (e32b33b -> 7b4ed63) - 판정: PASS
필수 Finding 없음.
1. TextField: describedby = hint+error(순서 hint 먼저), 04 명세 "aria-describedby=hint+error"와 일치. 기존 단언(aria-describedby='q-error')은 명세에 맞게 교체, 대신 accessibleDescription·alert id·error-only 케이스 추가 -> 약화 아닌 강화. HomePage 테스트도 동일.
2. Notice 이동: 문구·key 불변, role=note 유지, 토큰/색 변경 없음(주석 1줄 + JSX 위치만). 단 H1 명세 순서(질문->예시->고지->최근)에서 고지가 질문 카드 안(예시 앞)으로 이동 = 명세 편차. Light에서 inset=page 동색이라 보이지 않던 문제를 해소하는 의도적 선택이고, 고지가 제출 직전에 놓여 의미상 오히려 자연스러움. 비차단: PR 본문에 편차·이유 명시, 03 명세 후속 갱신 권장.
3. e2e: 실패(retrospective Mobile-375, routes last-record-card)는 Home과 무관한 화면이고 pw-o*/pw-out-head에 기준선 재현 흔적 있음, 단독 재실행 간헐 -> flake 결론은 근거에 부합. 세 번째 실패 식별은 보고서에 이름이 없음(2종만 명시)이라 PR 본문에 3건 이름 기재 권장(비차단). e2e 변경 없음.
4. 제품 동작·카피·라우팅: 변경 없음(diff 5파일, i18n/라우트/제출 로직 불변).
5. 이미지 6장: 390/768/1280 Light·Dark 모두 위계(질문 Card -> 진한 버튼 1개 -> inset 고지) 명확, Light에서 고지 면이 흰 카드 위 회색으로 가시, Dark 대비 양호, 가로스크롤 없음. 최근 기록은 텍스트 메타 행. 이상 없음.
