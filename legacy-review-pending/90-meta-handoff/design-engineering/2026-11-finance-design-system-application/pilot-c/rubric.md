# Pilot C 판정 루브릭 (AI 간 재사용성)

과제: `task.md` (pilotC-j3-meta). 이 루브릭은 **실행 전에 고정**한다. 실행 후 기준을 바꾸지 않는다.
base: `7dd9590`. 측정 대상은 두 런타임이 같은 입력으로 낸 결과물과 과정 기록이다.

측정 목적은 두 가지다.
1. **결과 품질**: 같은 설계 입력에서 계약을 얼마나 지키는가.
2. **재사용성**: 공유 입력(하네스 instruction, 설계 문서, 과제 문구)이 런타임 바뀌어도 같은 결과를 만드는가. 런타임별 보정 작업(입력 재설명, 수정 요청)이 얼마나 드는가.

---

## 1. 계약 준수 체크리스트 (런타임당 1장)

각 항목은 `PASS` / `FAIL` / `PARTIAL` / `NOT_RUN` 중 하나. `PARTIAL`은 0.5점, `NOT_RUN`은 집계에서 제외하고 사유를 적는다.

| ID | 항목 | 기준 출처 | 확인 방법 | 결과 | 증거 (명령·라인) |
|---|---|---|---|---|---|
| C1 | 배지 제거: 메타 영역 `RecordTagBadge` 0 | 03 J3 B/C, 05 §3 PR-3 | `grep -n RecordTagBadge` 함수 범위 | | |
| C2 | 행동·감정·유형 값이 텍스트로 렌더, 라벨 i18n 키 재사용 | 03 J3 "값은 텍스트" | 코드 리뷰 + 테스트 | | |
| C3 | 메타 카드 그림자 0 | 05 §4 #2 | `grep shadow` 함수 범위 | | |
| C4 | 메타 글자 역할 `type-*` 사용, `text-sm`·`text-xs`·`text-[` 0 | 05 §4 #5 | grep | | |
| C5 | 감정에 danger/warning 색 0 (X6) | 03 J3 Light/Dark | grep `danger`, `warning` | | |
| C6 | 임의값·hex 신규 0 | 05 §4 #2·#3 | diff grep (task A4) | | |
| C7 | 변경 파일 2개 이하, 허용 파일 밖 0 | task 4.1 | `git diff --name-only` | | |
| C8 | 공용 컴포넌트·신규 파일 0 | task 5.3 | `git status` | | |
| C9 | i18n 사전 무변경 | task A6 | `git diff --stat src/i18n` | | |
| C10 | 보호 계약 무변경 (`JournalCreate*`, POST, `EMOTION_TAGS`) | 05 §4 #10 | diff 파일 목록 + grep | | |
| C11 | route 문자열 하드코딩 0 | FE CLAUDE.md | grep `'/app` 등 신규 라인 | | |
| C12 | 테스트 삭제·스냅샷 일괄 갱신 0 | task 5.10 | diff 검토 | | |
| C13 | 신규 테스트 ≥ 1, role/name/텍스트 기반 셀렉터 | task 4.2-5 | diff 검토 | | |
| C14 | 수정·삭제 UI 변화 0 | task 5.4 | diff 검토 | | |
| C15 | 커밋 메시지가 목적 중심 (표현 원칙) | 공통 CLAUDE.md 표현 원칙 | `git log -1 --format=%B` | | |
| C16 | 검증 근거가 실행 로그로 있음 (추측 단언 0) | 공통 CLAUDE.md 증거 원칙 | `verify.log` 존재 + 결과 인용 | | |
| C17 | 실행하지 않은 항목을 NOT_RUN으로 명시 | 05 §4 #12 | 보고서 본문 | | |

집계: `계약 준수율 = (PASS + 0.5×PARTIAL) / (17 − NOT_RUN)`.
**필수 항목(반드시 PASS)**: C1, C3, C6, C7, C8, C10, C12, C16. 필수 항목 하나라도 FAIL이면 해당 런타임 결과는 **FAIL**로 판정한다(준수율 높아도 무관).

---

## 2. 컴포넌트 재사용 수

diff 기준으로 측정한다. 측정 명령:

```bash
BASE=7dd9590
# 1) 대상 파일 내 import 목록 (before / after 각각)
git show $BASE:src/pages/JournalDetailPage.tsx | grep -E "^import"  > before-imports.txt
git show HEAD:src/pages/JournalDetailPage.tsx | grep -E "^import"  > after-imports.txt
diff before-imports.txt after-imports.txt

# 2) 메타 영역에서 쓰는 UI 컴포넌트 수 (JSX 태그 종류 수)
#    JournalMetadata 함수 범위만 뽑아 대문자 시작 태그 고유 개수
awk '/^function JournalMetadata/,/^}/' <file> | grep -oE "<[A-Z][A-Za-z]+" | sort -u | wc -l
```

| 지표 | 정의 | Claude | Codex | 비고 |
|---|---|---|---|---|
| R1 기존 컴포넌트 사용 수 | 메타 영역에서 `src/components/ui`·`common`의 기존 컴포넌트 고유 개수 | | | |
| R2 신규 컴포넌트 수 | 새로 만든 공용 컴포넌트 수 (목표 0) | | | 0이 아니면 감점 |
| R3 재사용 비율 | R1 / (R1 + R2 + 로컬 헬퍼 수) | | | |
| R4 중복 생성 | 이미 있는 `Card`·`dl` 패턴을 새로 구현한 횟수 | | | |
| R5 import 증감 | after − before 줄 수 | | | |

판정 해석: R2 = 0 이고 R3 가 높은 쪽이 "기존 시스템 재사용"에 강하다. 단, 재사용 비율이 높아도 C1~C17 계약이 깨지면 의미 없다.

---

## 3. 임의 토큰 grep

변경 전후 같은 명령을 실행하고 **증감**을 기록한다. 기준선(before)은 `7dd9590` 파일이다.

```bash
PAT='-\[|#[0-9a-fA-F]{3,8}\b|text-xs|text-sm|text-\[|shadow|border-l|ring-|outline-none|!important|bg-background|rounded-\[|p-\[|m-\['
# before
git show 7dd9590:src/pages/JournalDetailPage.tsx | grep -cE "$PAT"
# after (런타임 워크트리에서)
grep -cE "$PAT" src/pages/JournalDetailPage.tsx
# 변경 라인만 (신규 추가 여부 판정 — 이게 핵심)
git diff -U0 7dd9590..HEAD -- src/pages/JournalDetailPage.tsx | grep '^+' | grep -nE "$PAT"
```

| 지표 | Claude | Codex | 목표 |
|---|---|---|---|
| T1 전체 파일 매치 before | (기준선) | (기준선) | — |
| T2 전체 파일 매치 after | | | T2 ≤ T1 |
| T3 **추가된 라인** 매치 | | | **0** |
| T4 제거된 매치 (`-` 라인) | | | 메타 영역 `text-sm`·`shadow` 제거분 ≥ 기대치 |

주의: `[overflow-wrap:anywhere]` 같은 기존 임의 속성은 이번 과제 범위 밖이므로 T2에서 그대로 남아도 감점하지 않는다. 추가만 막는다.

---

## 4. 검증 명령 (런타임 worktree마다 동일 순서)

```bash
pnpm install --frozen-lockfile          # 실패 시 실패 로그를 기록하고 1회만 재시도
pnpm vitest run src/pages/JournalDetailPage.test.tsx
pnpm typecheck                          # = tsc -b --noEmit
pnpm build                              # tsc -b && vite build && ssr build && prerender && verify-prerender
pnpm verify                             # 최종: prettier --check . && eslint . && tsc && vitest run && build
```

| 지표 | 정의 | Claude | Codex | Pilot A 기준(H1, 참고) |
|---|---|---|---|---|
| V1 대상 테스트 | 통과/전체 | | | — |
| V2 typecheck | exit code | | | 0 |
| V3 build | exit code | | | 성공 |
| V4 verify | exit code | | | — |
| V5 전체 vitest 통과 수 | 숫자 | | | 788 passed |
| V6 전체 vitest 실패 수 | 숫자 | | | 0 |
| V7 lint 경고 수 | eslint 출력 | | | 0 |
| V8 e2e (playwright) | 실행/NOT_RUN | | | NOT_RUN |
| V9 스크린샷 6장 | 실행/NOT_RUN | | | 실행 (390/768/1280 × L/D) |

규칙:
- 명령이 실패하면 **그 명령의 로그 마지막 80줄**을 `verify.log`에 남긴다.
- 실패를 고친 뒤 재실행한 경우 "최초 결과"와 "재실행 결과"를 둘 다 기록한다 (재시도 횟수 = 지표).

---

## 5. Owner 수정 요청 횟수 (사람 개입 비용)

리뷰어 역할은 **Owner 1명**이 맡고, 같은 기준으로 두 런타임 결과를 본다. 리뷰어가 "수정 요청"을 보낼 때마다 아래 표에 1행 추가한다.

| # | 요청 시각 | 대상 런타임 | 분류 (계약 / 회귀 / 스타일 / 테스트 / 범위 이탈 / 설명 부족) | 요청 내용 요약 | 수정 소요 (분) | 해결 (Y/N) |
|---|---|---|---|---|---|---|
| 1 | | | | | | |
| 2 | | | | | | |

집계 지표:
- O1 수정 요청 라운드 수 (요청 묶음 기준)
- O2 수정 요청 건수 (행 수)
- O3 계약 위반 건수 (분류 = 계약 또는 범위 이탈)
- O4 사람 개입 시간 합 (분)

주의: 리뷰어가 같은 사람이므로 **요청 기준을 사전에 고정**한다. 기준은 task.md 4.3 AC와 루브릭 1절 C1~C17이다. 이 범위 밖 요청은 "스타일 취향"으로 분류하고 집계에서 따로 표시한다.

---

## 6. 소요 시간·토큰·비용 (기록칸)

| 지표 | 정의 | Claude | Codex |
|---|---|---|---|
| E1 시작 시각 (UTC) | 첫 프롬프트 전송 | | |
| E2 로컬 커밋 완료 시각 (UTC) | `git log -1` 작성 시각 | | |
| E3 검증 완료 시각 (UTC) | `verify.log` 마지막 줄 | | |
| E4 wall-clock (분) | E3 − E1 | | |
| E5 사람 대기 시간 (분) | 승인 대기·질문 답변 대기 합 | | |
| E6 입력 토큰 | 런타임 usage 원문 | | |
| E7 출력 토큰 | 〃 | | |
| E8 캐시 토큰 (read/write) | 〃 (없으면 NOT_RUN) | | |
| E9 비용 (USD) | 대시보드 값 또는 "단가×토큰" 추정. 추정은 **추정**이라고 표시 | | |
| E10 도구 호출 수 | 로그 집계 | | |

**비교 조건 고정 (안 맞으면 비교 결과에 "조건 불일치"를 붙인다)**:
- 모델: Claude `claude-haiku-5-5`(본 세션 기준) — Codex 쪽 모델과 같은 급인지 기록. 같은 급이 아니면 "모델 등급 차이"를 명시하고 재사용성 결론은 보류한다.
- reasoning/effort 설정, sandbox·approval 모드, 입력 문서 순서를 둘 다 기록한다.
- 시간 측정은 같은 날·비슷한 시간대에 실행한다 (서버 부하 차이 배제는 불가하므로 한계로 적는다).

---

## 7. 재사용성 보조 지표 (공유 하네스가 실제로 작동했는지)

| ID | 질문 | 확인 방법 | Claude | Codex |
|---|---|---|---|---|
| S1 | 레포 지침 파일을 읽었나 (CLAUDE.md / AGENTS.md) | 세션 로그 Read 기록 | | |
| S2 | 입력 문서 11개를 순서대로 읽었나 | 세션 로그 Read 목록 | | |
| S3 | 스킬 사용을 하네스 로그에 남겼나 (Claude: PostToolUse 훅 자동, Codex: `harness-event emit skill-start` 수동) | `${XDG_STATE_HOME:-$HOME/.local/state}/oh-my-ai/harness-usage.log` 또는 `.oh-my-ai/state/harness-usage.log` | | |
| S4 | 같은 과제 문구로 같은 AC 해석을 했나 (AC 해석 차이 건수) | 결과 대조 | | |
| S5 | 금지사항 위반 건수 | task 5절 대조 | | |
| S6 | 실패 복구 (execution-recovery) 개입 횟수 | 로그 | | |

S3의 "로그 기록 실패"는 telemetry 실패이므로 작업 실패로 집계하지 않는다 (공통 지침).

---

## 8. 판정 기준

### 8.1 런타임별 합격 (Go / No-Go)
- **합격(PASS)**: 필수 항목(C1, C3, C6, C7, C8, C10, C12, C16) 전부 PASS, 계약 준수율 ≥ 0.9, V2·V3·V4 exit 0, O3 = 0.
- **조건부(CONDITIONAL)**: 필수 항목 PASS, 준수율 0.75 이상 0.9 미만, 또는 O3 = 1 (범위 이탈 1건을 수정 후 해소한 경우).
- **불합격(FAIL)**: 필수 항목 1개 이상 FAIL, 또는 V4 실패 후 미해결, 또는 금지사항 위반(5절 기준 계약 위반) 1건 이상 미해결.

### 8.2 두 런타임 비교 결론 (재사용성 관점)

| 결론 | 조건 |
|---|---|
| **재사용 가능 (입력 공유로 결과 동등)** | 두 런타임 모두 합격, 준수율 차이 ≤ 0.1, O2 차이 ≤ 1, C-필수 항목 불일치 0 |
| **부분 재사용 (보정 필요)** | 둘 다 합격 또는 조건부이나 O2 차이 ≥ 2, 또는 S4 AC 해석 차이 ≥ 2 |
| **재사용 불가 (런타임별 입력 분화 필요)** | 한쪽 FAIL, 또는 S5 위반이 한쪽만 발생 |
| **판정 보류** | 한쪽 미실행(NOT_RUN 처리 아님, 실제 미실행) 또는 조건 불일치 (6절 "비교 조건 고정") |

결론에는 반드시 "비교 조건 불일치 항목"과 "NOT_RUN 목록"을 함께 적는다.

### 8.3 Pilot A 대비 해석 (참고)
Pilot A(Claude 단독, H1)는 리뷰 PASS, 검증 788 passed / tsc 0 / build 성공, e2e NOT_RUN이었다.
Pilot C는 **작은 과제 1개를 두 런타임에 준 비교**이므로 Pilot A 수치를 Pass/Fail 기준으로 쓰지 않는다. 참고 열로만 둔다.

---

## 9. 한계 (결론 전에 항상 명시)
- 단일 과제, 단일 리뷰어(Owner)라서 통계적 유의성은 없다. 재현 가능성 확인 수준이다.
- 런타임별 모델·설정 차이가 결과 차이의 원인인지 하네스 차이인지 분리되지 않는다. 6절 조건을 기록해 분리 가능성을 남긴다.
- 시각 판정(스크린샷)은 Owner 육안 판단이라 주관이 섞인다. 판정 근거는 명령 증거를 우선한다.
- 실기기·VoiceOver·Capacitor 검증은 이 과제 범위 밖이다.
