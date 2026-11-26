# F3-00 요약 — Finance·Carelog 제품 수준 통합 UX 검수

## 결론

| 제품 | UX 출시 준비 판정 | BLOCKER | MAJOR | MINOR | 한 줄 |
|---|---|---|---|---|---|
| Carelog (`9c3a028`) | **CONDITIONAL** | 0 | 6 | 8 | 화면은 제품 수준이다. 업무 데이터가 보이는 곳과 되돌릴 수 없는 행동을 고쳐야 한다 |
| Finance (`2ae32e3`) | **CONDITIONAL** | 0 | 6 | 5 | 검토에서 결과까지는 제품 수준이다. 기록을 쓰는 순간(진입 위치, 입력 순서, 입력 보호)을 고쳐야 한다 |

- 이 판정은 **mock·fixture 기준의 UX 출시 준비 판정**이다. 제품 출시 가능 판정이 아니다. 실제 인증, 백엔드 저장·재조회, 운영 안정성은 범위 밖이고 NOT_RUN이다.
- 두 제품 모두 핵심 여정을 fixture에서 끝까지 조작했다. 못 본 구간은 Finance의 저장 직후 재조회, 학습 노트로 이어가기와 Carelog의 Assistant 답변이다(F3-01).
- 두 제품에서 똑같이 재현된 결함이 여섯 가지다. Foundation v1.0.0의 토큰과 표현 규칙은 잘 작동했고, 빠진 것은 **동작 계약**(포커스 표시, 이탈 보호, 화면 이동 시 스크롤, 목록·상세 포커스, 저장 알림, 파괴적 확인)이다. Design System v1.1 후보로 올린다(F3-04).

## 필수 수정 우선순위

| 순서 | 묶음 | Finding | 왜 먼저인가 |
|---|---|---|---|
| 1 | 공통: 이탈 보호 + 확인 Dialog | C-05, F-03 | 쓴 글이 사라진다. 두 제품 공통 |
| 2 | Finance: 화면 이동 시 스크롤·포커스 | F-01 | 작성 화면이 맨 아래로 열린다 |
| 3 | Finance: 작성 화면 순서 | F-02 | 첫 입력란이 약 1,150px 아래. UX 계약 순서와 다르다 |
| 4 | Carelog: 고객 상세의 다음 일정 | C-01 | 정리 필요 일정이 "다음 일정 없음"으로 보인다 |
| 5 | Carelog: 일정 화면 고정 영역과 새 일정 기본값 | C-03, C-04 | 추가 버튼과 정리 필요 안내가 화면 밖. 기본값만으로 정리 필요가 된다 |
| 6 | Carelog: 취소 처리 확인 | C-02 | 한 번 누르면 되돌릴 수 없다 |
| 7 | 공통: 포커스 표시 | C-06, F-06 | 진한 버튼의 포커스가 안 보인다(1:1). 공통 구현 한 곳 |
| 8 | Finance: 플래그를 열기 전 | F-04, F-05 | 저장되지 않는 맥락을 남긴다고 말한다. 수정·삭제 UI가 무스타일 |

세부 AC와 재검증은 F3-05에 있다. 시안 5개는 `mockups/`에 있다(C-01, C-02, C-03, F-02, F-05).

## Owner 결정이 필요한 것

1. 검토 맥락(Decision Context) 저장 지원 시점과 그 전의 화면 문구(F-04).
2. Carelog 취소 복구 기능 여부(C-02). 확인 패널은 결정 없이 넣을 수 있다.
3. Carelog 앱 표면을 Foundation대로 바꿀지(C-08).
4. v1.1에 동작 계약을 넣을지(F3-04 X-01~X-08).

## 알려진 항목 9개 판정

| # | 항목 | 판정 |
|---|---|---|
| 1 | Finance `ring-ring/50` 포커스 링 | 재현. Light 2.17:1, Dark 2.4:1 → F-06 (MAJOR) |
| 2 | Finance Light 하단 탭 글자 대비 | 재현. 4.28:1 → F-07 (MINOR) |
| 3 | Finance 폰 안쪽 화면의 화면 모드 접근 | 재현 → F-08 (MINOR) |
| 4 | 오전/오후 vs 24시간제 | 정책 차이. 혼란 근거 없음. 결함 아님 |
| 5 | Carelog Dark on-strong `#0b1220` | 7.63:1, 화면 문제 없음. 채택 권고 |
| 6 | Finance 회전·리사이즈 때 내비 포커스 손실 | 재현 안 됨. 4개 전환에서 포커스 유지. PASS |
| 7 | Finance 1280 이상 결과 보조 열 | PASS(1280×480 포함) |
| 8 | 공개·로그인 화면 Dark | 두 제품 결함 없음. PASS |
| 9 | Carelog 공개 화면 "기능 보기" 2개 | UX 중복 아님. 아래 링크 높이 24만 C-14 |

## 검증 범위

| 항목 | 값 |
|---|---|
| 직접 조작 | 두 제품 × 폰 390×844, 태블릿 768×1024, 가로 1024×768(Finance)·1180×800(Carelog), 데스크톱 1440×900. Light·Dark |
| 자동 측정 | Carelog 532회(19경로 × 14폭 × Light/Dark), Finance 557회(19경로 × 15폭 × Light/Dark). 넘침, 글자 대비, 12px 미만, 대상 크기, `h1` 수, 내비 위치·2단 여부 |
| 상태 | 로딩, 빈 화면, 오류, 다시 시도, 저장 중, 저장 실패, 검증, 없음. Carelog는 api-mock 빌드로 |
| 키보드 | 두 제품 주요 여정, 포커스 표시 대비, Dialog의 Escape와 포커스 복귀 |
| 회전·리사이즈 | 390×844 ↔ 844×390 입력 보존. Finance 내비 전환 4쌍 |
| 확대 | **흉내 실험만**: 640×400(1280 창 200% 상당), 320×256(400% 상당), 글자 200%. 실제 브라우저 zoom은 NOT_RUN |
| 캡처 | `captures/<product>/<set>/`와 각 `meta.json`(HEAD, 경로, 실측 폭·높이, 테마, 상태). 화면에는 fixture 데이터만 있다 |

## NOT_RUN · UNKNOWN

- NOT_RUN: 실제 백엔드 저장·인증·재조회, 실기기, 스크린리더, Safari·Firefox, OS 확대 설정, 실제 브라우저 zoom, Finance 저장 직후 재조회(mock 한계), Finance 영어 화면, Carelog 회원가입 성공, `datetime-local`의 기기별 표기.
- 헤드리스 브라우저의 `screen.orientation.type`은 viewport를 따라가지 않는다. 방향은 `innerWidth`와 `innerHeight`로 판단했다.
- UNKNOWN: 단계별 순 작업 시간과 정확한 비용(아래 메타 참고).
- "기존 v3 Finding 8건의 Delta Closure": 원문이 이 세션에 없어 대조하지 못했다(NOT_VERIFIABLE).

## 메타

| 항목 | 값 |
|---|---|
| 검수자 | Fable F3 (독립 Principal Product Designer 겸 UX 감사자). F1 설계와 제품 구현에 참여하지 않음. Design System rc1·rc2 검수 이력 있음 |
| 세션 | `session_01QY2zte6ByZwLJ2y76SH1y6` |
| 모델(자기 보고, 참고) | 시스템 프롬프트 기준 `claude-fable-5-2`, 추론 수준 high |
| 모델(메인 Runtime 교차 확인) | 단계 0 시작, 단계 1 경계, 사용량 제한 직전: configured_model, session_context.model, last_served_model 모두 `claude-fable-5-2` |
| 하위 에이전트 | Sonnet: fixture 실행 방법 조사 2개, 인벤토리 문서, 시안 제작. Haiku: 체크포인트 push. 판정과 시각 판단은 직접 했다 |
| 승인 | Owner가 이 세션에서 직접 승인(2026-11-25) |
| 비용 | 기준값 $22.43(F3 이전). 05:54 UTC 조회 $31.39 → 그 시점까지 약 $9. 이후 값은 UNKNOWN(메인 확인) |
| 중단 | 컨테이너 재시작 1회(05:54 UTC 무렵), 5시간 사용량 한도 1회(09:10 UTC 초기화). 산출물 유실 없음 |

## 고정 입력

| 저장소 | HEAD | 확인 |
|---|---|---|
| finance-harness-fe (dev) | `2ae32e3d12358b04caa6dba73488ddf6694cefff` | 일치 |
| carelog-fe (dev) | `9c3a028312f6def9631a91e58dc180ce7530e40e` | 일치 |
| design-system | `021e40e61ab19a487133d903fae7412eb2a135cb` | 일치 (원격 main은 `933edaf`로 이동, F3-01 Drift) |
| finance-harness-docs (main) | `1fe4f72fbdcfb883214776ccb556711c7b87eec4` | 일치 |
| carelog-architecture (main) | `aba36e32b1805c0d38cc7d129fcf4219c9e50047` | 일치 |

## Prompt Preflight (실행 전 1회 점검)

| 관점 | 점검 결과 |
|---|---|
| 목표 | "UX를 사용자에게 내놓을 수준인가, 아니면 무엇을 어떤 순서로 고치나" 하나다 |
| 사실 | 고정 HEAD 5개 직접 확인. 두 제품 빌드 exit 0 |
| 재사용 | F1의 실제 앱 캡처·측정은 이전 HEAD라 근거로 쓰지 않았다. F1 문서는 맥락 파악에만 썼다 |
| 위험 | 원격 쓰기는 한 브랜치·한 경로. push 전 검사 3종을 스크립트로 고정 |
| 과설계 | 전수 캡처 대신 자동 측정. 시안은 MAJOR의 시각·구조 문제 5건만 |
| 완료 조건 | v3 9절 8개 |
| AI 효율 | 실행 방법 조사·문서 조립·시안 제작은 하위 에이전트, 조작·시각 판단·판정은 직접 |

## 문서 목록

`F3-01-inventory.md`(인벤토리와 Drift), `F3-02-finance.md`, `F3-03-carelog.md`, `F3-04-system.md`(v1.1 후보, 공통 React UI, F1 패턴 판정), `F3-05-handoff.md`(PR 묶음), `F3-progress.md`(진행 기록), `captures/`, `mockups/`.
