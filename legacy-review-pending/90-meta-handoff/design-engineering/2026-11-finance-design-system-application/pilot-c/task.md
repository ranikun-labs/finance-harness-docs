# Pilot C 과제 (J3-meta): 저널 상세 메타 영역 정합화

- 과제 ID: `pilotC-j3-meta`
- 대상 런타임: Claude Code, Codex (**동일 과제 문구·동일 입력 문서·동일 금지사항**을 준다)
- 기준 base: `7dd9590` (Pilot A와 동일 base. 확인 절차는 `run-claude.md` / `run-codex.md` 참조)
- 상태: 인계 패키지 작성 완료. 두 런타임 모두 **미실행**(이 문서 작성 시점 기준).

---

## 1. 배경 (왜 이 과제인가)

Pilot A(PR #51, H1 검토 시작)는 Claude Code가 03 명세대로 화면을 정합화했고 독립 리뷰 PASS였다.
Pilot C는 **같은 설계 입력을 두 런타임에 주고 결과 차이를 재현 가능하게 비교**하는 것이 목적이다. 그래서 과제는
- 작고(파일 2개), 측정 가능하고(grep·테스트·계약 체크로 판정), 설계 문서에 명시된 규칙이 실제로 적용되는지 보는 과제여야 한다.

J3 저널 상세(`/app/journal/:id`)의 **메타 영역**이 적합하다. 03 명세 J3 "B/C" 행은 "메타 = InfoList, 값은 텍스트, 배지 없음"을 요구하는데,
현재 `JournalMetadata`는 `RecordTagBadge`로 유형·행동·감정을 배지로 그리고, 카드에 `shadow-sm`을 준다. 명세와 현 구현의 차이가 명확하다.

## 2. 과제 한 줄

> `src/pages/JournalDetailPage.tsx`의 `JournalMetadata` 컴포넌트를 03 명세 J3 규칙에 맞춘다: 유형·행동·감정 배지를 텍스트 값으로 바꾸고, 메타 카드의 그림자를 제거하고, 메타 글자 역할을 `type-*` 유틸로 맞춘다. 같은 파일 안에서만 작업하고 공용 컴포넌트는 건드리지 않는다.

## 3. 입력 문서 (두 런타임 동일, 이 순서로 읽게 한다)

절대 경로 기준. 파일이 없으면 실행을 멈추고 결과에 "입력 누락"으로 기록한다.

| 순서 | 경로 | 용도 |
|---|---|---|
| 1 | `finance-harness-fe:CLAUDE.md` 또는 `AGENTS.md` (런타임별 adapter, 생성물) | 레포 규칙·라우팅 |
| 2 | `finance-harness-fe:docs/product-policy.md` | FE 구현 guard (route/접근성/보호 계약) |
| 3 | `finance-harness-fe:docs/nav-map.md` | 라우트 기준 |
| 4 | `finance-harness-fe:docs/frontend-roadmap.md` | 현재 STEP 범위 확인 |
| 5 | `finance-harness-docs:service-policy/finance-ux-delta-auth-journal-handoff.md` | UX-Δ §4·§5 (저널 계약) |
| 6 | `scratchpad(비보존)/finance-design/03-screen-specs.md` | **J3 절 + §0.1 값·§0.3 상태 어휘 + 부록** (주 입력) |
| 7 | `scratchpad(비보존)/finance-design/05-handoff-plan.md` | **PR-3 범위·AC, §4 공통 AC 1~12** |
| 8 | `pilot-a/review-1st.md` (이 폴더) | Pilot A 리뷰 체크 방식(참고만) |
| 9 | `finance-harness-fe:src/pages/JournalDetailPage.tsx` | **대상 파일** |
| 10 | `finance-harness-fe:src/pages/JournalDetailPage.test.tsx` | **대상 테스트** |
| 11 | `finance-harness-fe:src/components/common/RecordTagBadge.tsx` | 읽기 전용 참고 (수정 금지, 라벨 키 출처 확인용) |

참고: Pilot A 결과 PR은 `ranikun-labs/finance-harness-fe#51` (Draft). 읽어도 되지만 복사하지 않는다.

## 4. 작업 범위

### 4.1 허용 파일 (이 두 개만 수정)
- `src/pages/JournalDetailPage.tsx` — `JournalMetadata` 함수 내부
- `src/pages/JournalDetailPage.test.tsx` — 기존 테스트 셀렉터 보정 + 신규 테스트 1개

### 4.2 해야 할 일
1. `JournalMetadata`의 `RecordTagBadge` 호출 3곳(`entryType`, `action`, `emotion`)을 **텍스트 값**으로 바꾼다. 라벨 문자열은 `RecordTagBadge.tsx`가 쓰는 **기존 i18n 키**를 재사용한다.
2. 메타 `Card`의 `shadow-sm`을 제거한다(명세: 카드 그림자 0, 그림자는 Dialog 오버레이만).
3. 메타 `dl`의 글자 크기 `text-sm`을 명세의 **meta 역할(13/18)** 에 해당하는 `type-*` 유틸로 바꾼다. 해당 유틸이 repo에 없으면 바꾸지 말고 결과 보고에 "유틸 부재"를 적는다. (확인된 예: `type-supporting`, `type-body`는 repo에 존재.)
4. 감정 값에 danger/warning 등 상태 색을 쓰지 않는다(명세 J3 Light/Dark: "감정 FOMO·혼란을 danger 색으로 칠하지 않는다").
5. 테스트: 기존 테스트 중 배지 role/클래스에 의존하는 셀렉터가 깨지면 **role/name/텍스트 기반**으로 고친다. 신규 테스트 1개 추가: 행동 4종·감정 5종 중 하나 이상에서 값이 텍스트로 렌더되고 배지 전용 마크업이 없음을 확인한다. (감정 5종 전체를 확인하면 더 좋다.)

### 4.3 완료 조건 (AC, 전부 측정 가능)
| ID | 조건 | 확인 방법 |
|---|---|---|
| A1 | `JournalMetadata` 안에 `RecordTagBadge` 사용 0건 | `grep -n "RecordTagBadge" src/pages/JournalDetailPage.tsx` 결과 중 `JournalMetadata` 범위 0건 (import 제거 포함 여부는 lint로 확인) |
| A2 | `JournalMetadata`의 `Card`에 `shadow` 클래스 0건 | 해당 함수 범위 grep `shadow` 0 |
| A3 | 메타 dl에 `text-sm`·`text-xs`·`text-[` 0건, `type-*` 유틸 사용 | 해당 함수 범위 grep |
| A4 | 변경 라인에 새 임의값(`-[`)·hex(`#[0-9a-fA-F]{3,8}`) 추가 0건 | `git diff -U0 <base>..HEAD -- src/pages/JournalDetailPage.tsx \| grep '^+' \| grep -nE '\-\[\|#[0-9a-fA-F]{3,8}'` 결과 0 |
| A5 | 새 공용 컴포넌트·파일 생성 0건 (`RecordTagText` 포함, 이는 PR-3 전체 범위의 다른 작업) | `git status --porcelain`에 신규 파일 0 |
| A6 | i18n 사전 변경 0 (새 키 추가 없음, ko/en parity 유지) | `git diff --stat`에 `src/i18n/` 0 |
| A7 | 테스트 전부 통과, 신규 테스트 ≥ 1 | `pnpm vitest run src/pages/JournalDetailPage.test.tsx` exit 0, 신규 테스트 이름 기록 |
| A8 | 전체 검증 통과 | `pnpm verify` exit 0 (prettier · eslint · tsc · vitest · build) |
| A9 | 변경 파일 2개 이하 | `git diff --name-only <base>..HEAD` 줄 수 ≤ 2 |
| A10 | 커밋 1개, 메시지가 목적 중심 (표현 원칙: 무엇을 만들었나보다 어떤 문제를 해결했나) | `git log -1 --format=%B` |

### 4.4 선택 (있으면 좋음, 없으면 NOT_RUN으로 기록)
- 390·1280 × Light·Dark 스크린샷 (Pilot A `shot.mjs` 방식). 상세 페이지는 데이터 시드가 필요해 **시드를 찾지 못하면 NOT_RUN**으로 둔다. 억지로 목 데이터를 레포에 추가하지 않는다.
- Playwright e2e: 실행하지 않으면 NOT_RUN.

## 5. 금지사항 (두 런타임 동일)

1. 허용 파일 2개 밖 수정 금지. 필요해 보여도 범위를 넓히지 말고 "범위 밖 필요 사항"으로 보고한다.
2. `RecordTagBadge.tsx`, `JournalRecordCard.tsx`, `JournalSummaryCard.tsx`, `JournalReviewPage.tsx`, `JournalEditor`, `LifecycleControls` 수정 금지. (RecordTagBadge는 다른 3개 화면이 쓰는 공용 컴포넌트다.)
3. 새 공용 컴포넌트(`RecordTagText` 등) 생성 금지. 필요하면 같은 파일 안의 내부 함수로만 둔다.
4. 수정·삭제·복원 UI 추가·변경 금지 (03 명세 Q5 미결, 계약 근거 없음).
5. 보호 계약 변경 금지: `JournalCreateCommand`, `JournalCreateRequest`, `JournalCreatePort`, `HttpJournalCreatePort`, `POST /finance/journals`, `create_failed`/`invalid_result`, `investment|study` 값, `EMOTION_TAGS` 5종.
6. route 문자열 하드코딩 금지. 경로는 `src/constants/routes.ts`만 사용.
7. 새 문구·새 필드·새 route 추가 금지. 계약에 없는 기능 금지.
8. 임의값(`-[...]`)·hex 신규 추가 금지, `!important` 금지, `outline-none`/`ring` 재정의 금지.
9. 의존성 추가 금지 (`package.json` 수정 금지).
10. 테스트를 지워서 통과시키기 금지. 스냅샷 일괄 갱신(`-u`) 금지.
11. `master` 직접 커밋·푸시 금지. 작업은 목적별 브랜치, **push·PR 생성은 이 과제 범위 밖**이다 (결과는 로컬 브랜치 + 로그로 수집).
12. 다른 세션의 dirty/untracked/stash 작업을 결과에 섞지 않는다. 반드시 별도 worktree에서 작업한다.
13. 검증을 실행하지 않고 통과라고 쓰기 금지. 실행 못 한 항목은 `NOT_RUN` + 사유.
14. 런타임 실행 여부를 허위로 기록 금지 (Codex 미실행 상태에서 실행했다고 쓰면 안 된다).

## 6. 산출물 (결과 수집용, 모두 scratchpad 아래)

- `results/<runtime>/diff.patch` — `git diff <base>..HEAD` 전체
- `results/<runtime>/verify.log` — `pnpm verify` 출력 전체 (tail 200줄 이상)
- `results/<runtime>/vitest-file.log` — 대상 테스트 파일 실행 로그
- `results/<runtime>/session-notes.md` — 아래 템플릿 (기록칸 채움)
- 대화/도구 로그 원본 export가 가능하면 같은 폴더에 저장

`session-notes.md` 템플릿:
```
# <runtime> 실행 기록 (pilotC-j3-meta)
- 시작/종료 시각 (UTC):
- 모델·버전·reasoning/effort:
- sandbox·approval 모드:
- base SHA 확인 결과:
- 입력 문서 읽음 여부 (순서 1~11 체크):
- 토큰 (input/output/cache, 가능하면 런타임 usage 원문):
- 비용 (USD, 런타임 대시보드 또는 추정 방식 명시):
- 수정 요청(Owner/리뷰어) 횟수 및 목록:
- 범위 이탈 발생 여부:
- 막힌 지점과 해결 방식:
```

## 7. 과제 문구 (그대로 전달)

> 저장소 `finance-harness-fe`의 base `7dd9590`에서 작업 브랜치를 만들고, `src/pages/JournalDetailPage.tsx`의 `JournalMetadata`를 아래 입력 문서의 J3 규칙에 맞춰 정합화하세요. 입력 문서 11개를 순서대로 읽고 시작하세요. (목록은 task.md 3절)
> 수정 범위는 `JournalDetailPage.tsx`와 `JournalDetailPage.test.tsx` 두 파일로 제한합니다. 유형·행동·감정 배지를 텍스트 값으로 바꾸고, 메타 카드 그림자를 제거하고, 메타 글자 크기를 `type-*` 유틸로 맞추세요. 테스트를 보정하고 신규 테스트를 1개 이상 추가하세요.
> 금지: 두 파일 밖 수정, 공용 컴포넌트 생성·수정, i18n 키 추가, 보호 계약 변경, 임의값·hex 추가, 테스트 삭제, push, PR 생성.
> 완료 기준: task.md 4.3 A1~A10. 마지막에 `pnpm verify` 결과와 AC 표(PASS / FAIL / NOT_RUN + 증거)를 보고하세요. 실행하지 않은 것은 NOT_RUN으로 쓰세요.
