# Pilot C 실행 절차: Claude Code

과제: `task.md` (pilotC-j3-meta). 판정: `rubric.md`. 결과표: `results-template.md`.
이 문서는 **절차**다. 이 문서를 작성한 세션에서는 Claude 실행을 하지 않았다.

S = `scratchpad(비보존)`
P = `$S/pilotC`

---

## 0. 원칙

- 실행은 **새 세션**에서 한다. 설계 문서를 읽은 이 세션을 재사용하지 않는다 (앞선 맥락이 결과를 오염시킨다).
- 입력은 `task.md` 3절 순서 그대로. 프롬프트는 `task.md` 7절 문구를 그대로 쓴다.
- Codex와 **같은 날, 같은 base SHA, 같은 입력 문서 버전**으로 실행한다.
- push·PR 생성 금지. 결과는 로컬 브랜치와 `$P/results/claude/`에 모은다.

## 1. 사전 확인 (실행 전, 하나라도 실패하면 중단하고 결과 템플릿에 기록)

```bash
# 1) 레포 존재·git 여부 확인
git -C finance-harness-fe rev-parse --is-inside-work-tree
```
- 결과가 `true`면 그 저장소를 쓴다.
- 결과가 실패(`not a git repository`)면 같은 원격을 scratchpad에 새로 받는다. 기존 dirty 작업과 섞이지 않게 한다.
```bash
gh repo clone ranikun-labs/finance-harness-fe "$P/repo"
git -C "$P/repo" fetch origin
```

```bash
# 2) base SHA 확인 (7dd9590 = Pilot A와 같은 base)
git -C "$P/repo" cat-file -t 7dd9590        # 결과: commit
git -C "$P/repo" show 7dd9590:src/pages/JournalDetailPage.tsx | grep -c RecordTagBadge
# 결과 ≥ 1 이어야 한다. 0이면 base가 과제와 맞지 않으므로 중단 (base 불일치 기록)

# 3) 입력 문서 존재 확인 (task.md 3절 11개)
for f in \
  finance-harness-fe:CLAUDE.md \
  finance-harness-fe:docs/product-policy.md \
  finance-harness-fe:docs/nav-map.md \
  finance-harness-fe:docs/frontend-roadmap.md \
  finance-harness-docs:service-policy/finance-ux-delta-auth-journal-handoff.md \
  $S/finance-design/03-screen-specs.md \
  $S/finance-design/05-handoff-plan.md \
  $S/pilot-a/review-1st.md ; do test -f "$f" && echo "OK $f" || echo "MISSING $f"; done
```

참고: 현재 `finance-harness-fe` 체크아웃의 `JournalDetailPage.tsx`는 base와 다를 수 있다(다른 세션 작업 포함). 과제 기준 파일은 **반드시 `7dd9590`의 내용**이다. 작업은 worktree에서만 한다.

## 2. 작업 공간 분리 (worktree)

```bash
mkdir -p "$P/wt"
git -C "$P/repo" worktree add -b pilotC/claude-j3-meta "$P/wt/claude" 7dd9590
cd "$P/wt/claude" && pnpm install --frozen-lockfile
```

- 브랜치 이름: `pilotC/claude-j3-meta` (Codex는 `pilotC/codex-j3-meta`).
- `master`에서 분기하지 않는다. 기준은 `7dd9590`이다.
- 설치가 실패하면 로그를 `results/claude/install.log`에 저장하고 1회만 재시도한다.

## 3. 프롬프트 준비

`task.md` 7절 블록을 파일로 저장한다. 입력 문서 목록(3절)은 프롬프트 본문에 그대로 포함한다.

```bash
mkdir -p "$P/results/claude"
# 7절 인용문을 prompt-claude.txt로 저장 (task.md 7절 그대로, 문구 수정 금지)
```

## 4. 실행

두 가지 방식 중 하나를 쓰고, 어떤 방식인지 `session-notes.md`에 적는다.

### 방식 A: 인터랙티브 세션 (권장, 승인 과정이 기록됨)
1. 새 Claude Code 세션을 열고 작업 디렉터리를 `$P/wt/claude`로 지정한다.
2. 첫 메시지로 `prompt-claude.txt` 내용을 붙여넣는다.
3. 세션 export 또는 전사를 `results/claude/transcript.md`로 저장한다.

### 방식 B: 비대화형 (토큰·비용 JSON이 바로 남음)
```bash
cd "$P/wt/claude"
claude -p "$(cat "$P/prompt-claude.txt")" --output-format json \
  > "$P/results/claude/run.json" 2> "$P/results/claude/run.stderr.log"
```
- 출력 JSON의 필드명(usage, cost 등)은 CLI 버전마다 다르다. 실행 전 `claude --help`로 옵션을 확인하고, 실제 출력 필드를 `session-notes.md`에 그대로 옮긴다.
- 비대화형 실행은 승인 프롬프트를 띄우지 않으므로 허용 도구 범위를 실행 전에 같은 값으로 맞춘다 (sandbox·permission 모드를 `session-notes.md`에 기록).

실행 중 금지: 사람이 중간에 힌트를 주는 것. 힌트가 필요하면 **그 사실을 기록**하고(Owner 수정 요청으로 집계, rubric 5절), 힌트 전후를 구분해서 적는다.

## 5. 검증 (실행 종료 후, 같은 순서)

```bash
cd "$P/wt/claude"
git status --porcelain                              # 신규·수정 파일 목록 (C7, C8)
git diff --name-only 7dd9590..HEAD                 # 허용 파일 2개 이내인지
pnpm vitest run src/pages/JournalDetailPage.test.tsx > "$P/results/claude/vitest-file.log" 2>&1; echo "exit=$?"
pnpm typecheck > "$P/results/claude/typecheck.log" 2>&1; echo "exit=$?"
pnpm build > "$P/results/claude/build.log" 2>&1; echo "exit=$?"
pnpm verify > "$P/results/claude/verify.log" 2>&1; echo "exit=$?"
```

- 에이전트가 이미 돌린 명령이라도 **Owner 또는 리뷰어가 직접 다시 돌린다** (루브릭 V 항목의 재현성).
- `verify` 실패 시 로그 마지막 80줄을 `verify.log` 상단에 복사해 둔다.
- 스크린샷은 선택 (task 4.4). 시드가 없으면 NOT_RUN.

## 6. 결과 수집

```bash
cd "$P/wt/claude"
git diff 7dd9590..HEAD > "$P/results/claude/diff.patch"
git log -1 --format=%B > "$P/results/claude/commit-message.txt"
git diff -U0 7dd9590..HEAD -- src/pages/JournalDetailPage.tsx | grep '^+' | grep -nE '\-\[|#[0-9a-fA-F]{3,8}\b' > "$P/results/claude/added-arbitrary.txt" || true
```

- `session-notes.md` 템플릿은 `task.md` 6절을 따른다 (`$P/results/claude/session-notes.md`).
- 루브릭 1~7절 표를 채운 뒤 `results-template.md`의 Claude 열에 옮긴다.
- 커밋은 로컬에만 남긴다. `git push` 금지, `gh pr create` 금지.

## 7. 정리

- worktree는 결과 수집 후 **삭제하지 않는다** (리뷰어 재확인용). 디스크를 아끼려면 `node_modules`만 지운다.
- 정리 명령 예: `rm -rf "$P/wt/claude/node_modules"` (브랜치·커밋은 유지).
- 브랜치는 Codex 결과와 비교가 끝날 때까지 보존한다.

## 8. 실패 처리

| 상황 | 처리 |
|---|---|
| base SHA 불일치 | 실행하지 않고 `results-template.md` 판정을 "판정 보류 (base 불일치)"로 둔다 |
| 입력 문서 누락 | 누락 파일을 기록하고 실행 여부를 Owner가 결정한다 (임의로 대체 문서 사용 금지) |
| `pnpm install` 실패 | 로그 저장 후 1회 재시도, 재실패 시 "검증 NOT_RUN"으로 기록 |
| 범위 이탈 (허용 파일 밖 수정) | 그 변경을 되돌리지 않고 `git diff --name-only`로 증거를 남긴 뒤 C7 FAIL로 기록 |
| 세션 중단 | 같은 worktree에서 재개하지 말고 새 worktree(`claude-j3-meta-2`)로 다시 시작, 중단 기록을 남긴다 |
