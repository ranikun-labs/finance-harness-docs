# F1-00 요약 (Fable F1, 2026-11-24)

1. **결론**: 폭 기준과 내비 위치는 두 제품 모두 그대로 두고(A), 화면 안의 위계를 Foundation v1.0.0 규칙으로 맞추는 설계를 시안 6묶음과 함께 냈다. 공통으로 올릴 것은 값이 아니라 역할 9개(C)다.
2. **가장 먼저 고칠 것**: Finance 1280×1366에서 `layout.ts`는 rail인데 화면은 상단 내비다(`globals.css:300`). F1-01 F-1, F1-05 PR-F0.
3. **필수 수정**: Finance 7건(F1-02 §2), Carelog 6건(F1-03 §2). 중심은 "행동 강도가 반대"(Finance 결과·상세)와 "시각이 가장 약함"(Carelog 일정).
4. **진행 중 PR**: Finance #53, Carelog #38은 방향이 같아 그대로 진행을 추천한다(F1-05 §1).
5. **Owner Gate (F1-01 §7)**: D-1 Compact 줄높이 확인, D-5 Dark 노출, D-6 결과 화면 체크 변경 가능 여부, D-7 Carelog 하단 내비 한 줄, D-8 Finance 후속 질문 기능, D-9 문구 변경 3건, D-10 Finance 결과 화면 보조 열. D-3·D-4(Dark Teal 값)는 v1.1 후보.
6. **시각 확인 범위(직접 열어 봄)**: 시안 6화면 12뷰를 390·768×1024·1024×768·1440 Light, 대표 Dark, 첫 화면 320·375·393, 상태 8종으로 94장 렌더하고 모음 시트로 확인. 경계 40회 측정. 실제 앱은 두 제품 9경로씩 17폭으로 342장 캡처하고 모음 시트 19장을 확인.
7. **자동 측정(시안 94장 + 경계 40회)**: 가로 넘침·44 미만 대상·12px 미만·대비 미달·글자 잘림·Strong 2개 이상·카드 안 카드 모두 0건(정적 렌더, 자체 검증).
8. **NOT_VERIFIED**: react-product-foundation(연결 거부), Finance 작성 화면·복기 입력 폼·인증 복귀, Carelog 인증·Assistant·일정 추가 폼·정리 필요 상태 상세, #53·#38의 렌더 결과.
9. **NOT_RUN**: 키보드 순서, 스크린리더, 200% 확대, 실기기, 회전 동작, `verification/run-gate.sh`(시안은 정본 밖 산출물).
10. **정본·입력**: design-system `021e40e`(MANIFEST 실패 0건), finance-harness-fe `fda50a6`, finance-harness-docs `7dc249b`, carelog-fe `963f173`, carelog-architecture `aba36e3`.
11. **모델**: `claude-fable-5-2` (시스템 프롬프트 기준. 세션 `session_01QY2ztb2yQX7eQkHjheQmHm`. 세 필드 교차 확인은 메인 담당). 실행 중 작업 프로세스가 한 번 재시작됐고, 모델 변경 알림은 받지 못했다.
12. **Effort**: UNKNOWN. **소요**: 15:02~17:10 UTC 무렵(중간에 사용량 제한으로 멈춘 시간 포함, 순 작업 시간 UNKNOWN). 비용 UNKNOWN.
13. **독립 검수**: 설계에 참여하지 않은 Opus Reviewer가 문서·캡처·원본을 직접 열어 검수했다. 1차 판정 CHANGES_REQUIRED(BLOCKER 0, MAJOR 2, MINOR 7). 9건을 모두 반영했다(보조 열 D-10 재분류, 주간 띠 44 보정과 재측정 등). **반영 뒤 재검수는 NOT_RUN**이다. Reviewer가 맞다고 확인한 것: MANIFEST, Drift 수치, F-1 결함, D-1, Carelog 1179/1180, 경계 40회, CTA 문구, 금지어 0건.
14. **변경하지 않은 것**: 제품 코드, Foundation 정본, Jira, Confluence. Git은 이 폴더를 추가하는 Draft PR 1건뿐이다.
