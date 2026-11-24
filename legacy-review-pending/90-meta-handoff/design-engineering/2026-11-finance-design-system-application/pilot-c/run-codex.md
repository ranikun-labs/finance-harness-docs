# Pilot C 실행 절차: Codex

> **상태: 미실행 (인계 절차)**. 이 패키지를 작성한 세션에는 Codex 실행 도구가 없다. 아래 명령과 플래그는 Codex CLI 버전에 따라 다를 수 있으므로, 실행 전에 `codex --help`와 `codex exec --help`로 **실제 옵션을 확인**하고 확인된 값만 쓴다. 확인하지 않은 플래그는 결과에 적지 않는다.

과제: `task.md` (pilotC-j3-meta). 판정: `rubric.md`. 결과표: `results-template.md`.

S = `scratchpad(비보존)`
P = `$S/pilotC`

---

## 0. 원칙

- Claude 실행과 **같은 base SHA `7dd9590`, 같은 입력 문서 11개, 같은 과제 문구(`task.md` 7절)** 를 쓴다.
- Codex 세션은 새로 연다. Claude 실행 결과나 이 설계 맥락을 미리 보여주지 않는다.
- 입력 지침 파일은 레포의 **AGENTS.md**(Codex adapter, `instructions/harness.md`에서 생성된 것)를 쓴다. 파일이 없으면 실행을 멈추고 기록한다 (Codex에 입력을 임의로 바꿔 주지 않는다).
- push·PR 금지.

## 1. 사전 확인

Claude 쪽 `run-claude.md` 1절과 동일하게 수행한다 (레포 확인, `7dd9590` 확인, 입력 문서 11개 존재 확인). 추가로:

```bash
# AGENTS.md 존재 확인 (Codex 지침 입력)
test -f finance-harness-fe:AGENTS.md && echo OK || echo "MISSING AGENTS.md"
# 하네스 이벤트 도구 확인 (스킬 사용 로그용, S3)
command -v "$HOME/.local/bin/harness-event" || echo "harness-event 없음 (S3 NOT_RUN 처리)"
```

- `AGENTS.md`가 `7dd9590`에 있는지도 확인한다: `git -C "$P/repo" cat-file -e 7dd9590:AGENTS.md`.
- 없으면 실행 전에 Owner 판단을 받는다. 임의로 CLAUDE.md를 넣지 않는다.

## 2. 작업 공간 분리 (worktree)

```bash
git -C "$P/repo" worktree add -b pilotC/codex-j3-meta "$P/wt/codex" 7dd9590
cd "$P/wt/codex" && pnpm install --frozen-lockfile
```

- 브랜치: `pilotC/codex-j3-meta`. Claude는 `pilotC/claude-j3-meta`.
- 두 worktree는 서로 다른 디렉터리·브랜치여야 하며, 한쪽의 `node_modules`를 다른 쪽에서 재사용하지 않는다.

## 3. 프롬프트 준비

```bash
mkdir -p "$P/results/codex"
# task.md 7절 인용문을 prompt-codex.txt로 저장 (문구 수정 금지, Claude와 바이트 단위로 같아야 함)
cmp "$P/prompt-claude.txt" "$P/prompt-codex.txt" && echo SAME || echo "DIFFERENT (기록 필요)"
```

- Claude와 Codex 프롬프트 파일을 `cmp`로 비교해 **동일**함을 확인한다. 다르면 같은 파일을 다시 쓴다.
- 입력 문서 목록(task.md 3절)은 프롬프트에 포함하거나, 같은 순서의 목록 파일을 주고 "이 순서로 읽어라"라고 지시한다.

## 4. 실행

### 방식 A: 인터랙티브 Codex 세션
1. 새 Codex 세션을 열고 작업 디렉터리를 `$P/wt/codex`로 지정한다.
2. 첫 메시지로 `prompt-codex.txt` 내용을 붙여넣는다.
3. 세션 전사를 `results/codex/transcript.md`로 저장한다.

### 방식 B: 비대화형 `codex exec` (버전 확인 후 사용)
```bash
codex exec --help          # 작업 디렉터리·샌드박스·JSON 출력 옵션 확인 후 아래를 맞춘다
cd "$P/wt/codex"
codex exec "$(cat "$P/prompt-codex.txt")" > "$P/results/codex/run.log" 2>&1
```
- 작업 디렉터리 지정, 샌드박스(쓰기 허용 범위), 승인 모드 옵션은 `--help` 결과의 **실제 이름**으로 쓴다. 확인되지 않은 옵션은 사용하지 않는다.
- 토큰·비용 값이 출력에 없으면 "미수집"으로 두고 추정하지 않는다 (루브릭 6절 E9 규칙).

실행 중 금지: 사람이 중간에 힌트를 주는 것. 힌트가 필요하면 기록하고 Owner 수정 요청으로 집계한다 (rubric 5절).

## 5. 스킬 사용 로그 (S3)

Codex는 명시 `Skill` 도구가 없으므로, 공유 지침에 따라 **작업 시작 시 한 번** 다음 이벤트를 발생시키도록 프롬프트가 요구하는지 확인한다 (task 프롬프트에는 없음 → 지침 파일이 요구하는지만 본다).

```bash
"$HOME/.local/bin/harness-event" emit skill-start --skill <skill-name> --runtime codex
```

- 기록이 없으면 S3 = FAIL이 아니라 "지침 준수 여부 관찰값"으로 기록한다 (지침이 요구하는 단계이므로 준수 여부는 측정 대상이다).
- 로그 기록 실패(권한·sandbox)는 telemetry 실패이므로 작업 실패로 세지 않는다. repo-local fallback 경로를 확인한다.

## 6. 검증 (종료 후, Claude와 같은 명령)

```bash
cd "$P/wt/codex"
git status --porcelain
git diff --name-only 7dd9590..HEAD
pnpm vitest run src/pages/JournalDetailPage.test.tsx > "$P/results/codex/vitest-file.log" 2>&1; echo "exit=$?"
pnpm typecheck > "$P/results/codex/typecheck.log" 2>&1; echo "exit=$?"
pnpm build > "$P/results/codex/build.log" 2>&1; echo "exit=$?"
pnpm verify > "$P/results/codex/verify.log" 2>&1; echo "exit=$?"
```

- Codex가 낸 "통과" 보고는 **근거로 인정하지 않는다.** 리뷰어가 같은 명령을 직접 다시 돌려 확인한다.

## 7. 결과 수집

```bash
cd "$P/wt/codex"
git diff 7dd9590..HEAD > "$P/results/codex/diff.patch"
git log -1 --format=%B > "$P/results/codex/commit-message.txt"
git diff -U0 7dd9590..HEAD -- src/pages/JournalDetailPage.tsx | grep '^+' | grep -nE '\-\[|#[0-9a-fA-F]{3,8}\b' > "$P/results/codex/added-arbitrary.txt" || true
```

- `session-notes.md`를 `task.md` 6절 템플릿으로 채운다. 모델명·reasoning 설정·샌드박스 값은 실행 시점에 확인한 값만 쓴다.
- 루브릭 1~7절 표를 채우고 `results-template.md`의 Codex 열에 옮긴다.

## 8. 정리

- worktree 보존, `node_modules`만 정리 가능 (Claude와 동일).
- 브랜치 `pilotC/codex-j3-meta`는 비교 완료 전까지 유지.

## 9. 실패 처리

| 상황 | 처리 |
|---|---|
| `AGENTS.md` 부재 | 실행 보류, Owner 결정 기록. 임의 대체 금지 |
| 옵션 이름 확인 불가 | 그 옵션 없이 인터랙티브 방식 A로 전환하고 전환 사유 기록 |
| base SHA 불일치 | 실행하지 않고 "판정 보류 (base 불일치)" |
| 토큰·비용 미노출 | "미수집" 기록, 추정 금지 |
| 범위 이탈 | 되돌리지 않고 `git diff --name-only` 증거 기록 후 C7 FAIL |
| 세션 중단 | 새 worktree(`codex-j3-meta-2`)로 재시작, 중단 기록 |
