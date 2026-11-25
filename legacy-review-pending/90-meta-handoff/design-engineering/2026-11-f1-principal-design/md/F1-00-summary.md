# F1-00 요약 (Fable F1, 2026-11-24)
1. **결론**: 폭 기준과 내비 위치는 두 제품 모두 그대로 두고(A), 화면 안의 위계를 Foundation v1.0.0 규칙으로 맞추는 설계를 시안 6묶음과 함께 냈다. 공통으로 올릴 것은 값이 아니라 역할 9개(C)다.
2. **가장 먼저 고칠 것**: Finance 1280×1366에서 `layout.ts`는 rail인데 화면은 상단 내비다(`globals.css:300`, 최신 dev에도 남음). F1-01 F-1, F1-05 PR-F0.
3. **필수 수정**: Finance 7건(F1-02 §2), Carelog 6건(F1-03 §2). 검수 뒤 병합된 Finance #53·#54, Carelog #38이 일부를 해소했을 수 있어 화면 PR은 최신 dev 재캡처 뒤 남은 차이만 다룬다(F1-05 §1).
4. **Owner Gate 9건(F1-01 §7.2, 이 목록이 유일한 정본)**: F1-G1 Compact 줄높이 확인 / G2 Dark 노출 / G3 결과 화면 체크 변경 가능 여부 / G4 Carelog 하단 내비 한 줄 / G5 Finance 후속 질문 기능(시안 ③) / G6 문구 변경 3건 / G7 Finance 결과 화면 1280 보조 열 / G8 결과 화면에서 맥락·사실 고르기 / G9 Carelog 24시간제 표기.
5. **Gate가 아닌 분류 확정(F1-01 §7.1)**: Carelog 상태 레일과 1100 2단은 A로 유지. Dark Teal 값과 Dark on-strong은 v1.1 후보(C)이고, LOCK을 후보로 올린 것이 아니다.
6. **시각 확인 범위(직접 열어 봄)**: 시안 6화면 12뷰 95장(390·768×1024·1024×768·1440 Light, 대표 Dark, 첫 화면 320·375·393, 상태 8종)과 실제 앱 342장(두 제품 9경로 × 17폭, fixture)을 모음 시트로 확인.
7. **자동 측정(시안 95장 + 경계 40회)**: 가로 넘침·44 미만 대상·12px 미만·대비 미달·글자 잘림·Strong 2개 이상·카드 안 카드 0건(정적 렌더, 자체 검증).
8. **NOT_VERIFIED**: react-product-foundation(연결 거부), Finance 작성 화면·복기 입력 폼·인증 복귀, Carelog 인증·Assistant·일정 추가 폼·정리 필요 상태 상세, #53·#54·#38 병합 뒤 렌더와 #39 내용.
9. **NOT_RUN**: 키보드 순서, 스크린리더, 200% 확대, 실기기, 회전 동작, `verification/run-gate.sh`(시안은 정본 밖 산출물).
10. **정본·입력**: design-system `021e40e`(MANIFEST 실패 0건), finance-harness-fe `fda50a6`, finance-harness-docs `7dc249b`, carelog-fe `963f173`, carelog-architecture `aba36e3`.
11. **모델**: `claude-fable-5-2`(시스템 프롬프트 기준, 메인이 세 필드 일치 확인). 세션 `session_01QY2ztb2yQX7eQkHjheQmHm`. 프로세스 재시작 1회, 사용량 제한 중단 1회. Effort·순 작업 시간·비용 UNKNOWN(15:02~17:30 UTC 무렵).
12. **독립 검수**: 설계 미참여 Opus Reviewer 2회. 1차(커밋 전) CHANGES_REQUIRED(MAJOR 2, MINOR 7) → 9건 반영. 2차(head `5ce84f1`) CHANGES_REQUIRED(MAJOR 2, MINOR 2) → 4건과 권고 4건 반영. **반영 뒤 Delta 재검수는 메인이 실행한다(이 문서 작성 시점 NOT_RUN).**
13. **변경하지 않은 것**: 제품 코드, Foundation 정본, Jira, Confluence. Git은 이 폴더를 추가하는 Draft PR 1건(finance-harness-docs #22)뿐이다.
