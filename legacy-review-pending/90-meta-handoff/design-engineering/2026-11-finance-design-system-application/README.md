# Finance 디자인 시스템 적용 분석과 Pilot C 인계 자료

Finance Harness FE에 디자인 시스템(Foundation v1.0.0)을 적용하기 위한 화면·계약 분석, 구현 인계 계획, 그리고 Pilot C(AI 간 재사용성 검증) 과제 정의를 세션 작업 공간에서 유실되지 않도록 보존한 폴더다.

- 목적: 작업 세션 scratchpad에만 있던 분석 산출물을 repository에 남겨, 다음 세션과 Owner 검토가 같은 근거로 이어받게 한다.
- 성격: 분석·인계 자료다. 승인된 제품 정책이나 Foundation 원문이 아니며, 구현 완료나 검증 PASS를 의미하지 않는다.

## 파일

### `finance-design/`
| 파일 | 내용 |
| --- | --- |
| `01-inventory.md` | Finance FE 화면·흐름 인벤토리와 분석 범위 |
| `02-contract-gap.md` | 계약·Foundation 대비 차이 분석 |
| `03-screen-specs.md` | 화면별 디자인 명세 (Foundation v1.0.0 기준) |
| `04-missing-components.md` | 누락 컴포넌트·패턴 명세와 우선순위 |
| `05-handoff-plan.md` | 구현 인계 계획 (작은 PR 단위) 및 Pilot A 추천 |

### `pilot-c/`
| 파일 | 내용 |
| --- | --- |
| `task.md` | Pilot C 과제 정의 (J3-meta: 저널 상세 메타 영역 정합화) |
| `rubric.md` | 판정 루브릭 (AI 간 재사용성) |
| `run-claude.md` | Claude Code 실행 절차 |
| `run-codex.md` | Codex 실행 절차 |
| `results-template.md` | 결과표 템플릿 (실행 후 기록용) |

## 상태

- Finance 분석(`finance-design/`): 작성 완료. 렌더 미검증.
- Pilot C(`pilot-c/`): NOT_RUN.
- Claude·Codex 실행: 모두 미실행. 결과표는 빈 템플릿 상태다.

## 관련

- Jira: RPL-146, RPL-148
- PR: #49, #51

## 정제 메모

- 작업 중 사용한 절대 경로는 저장소 상대 표기로 바꿨다. 예: `finance-harness-fe:src/...`, `scratchpad(비보존)`(세션 임시 경로라 보존되지 않음).
- 이메일·토큰·비밀번호·전화번호 패턴을 검색했고 자격 증명·개인정보 항목은 없다. `token` 단어 2건은 디자인 토큰 용어다.
- `pilot-c/task.md`가 참조하는 `pilotA/review.md`는 이 폴더에 포함하지 않았다(Pilot A 세션 산출물, 별도 보존 필요).
