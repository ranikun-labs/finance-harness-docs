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

### `pilot-a/`
Pilot A(finance-harness-fe PR #51, 홈 화면 적용) 검수 근거. 이미지는 포함하지 않는다.
| 파일 | 내용 |
| --- | --- |
| `review-1st.md` | 1차 독립 리뷰 (HEAD e32b33b, 판정 PASS) |
| `review-delta.md` | Delta 독립 리뷰 (e32b33b -> 7b4ed63, 판정 PASS) |
| `final.md` | Writer 최종 결과 (HEAD 7b4ed63) |

- 대상: finance-harness-fe PR #51, 최종 HEAD 7b4ed63. 독립 리뷰 PASS 2회(각 리뷰가 명시한 HEAD 기준).
- NOT_RUN: editor-harness, http-smoke, backend-integration, 실기기. e2e 실패 3종은 기준선에서도 재현된 기존 flake로 Writer가 판단했다(독립 리뷰 Delta가 근거 부합 확인).
- 용도: Pilot C 비교 시 Pilot A(기준 구현)의 검수 근거로 사용한다. 승인·병합 근거가 아니다.

## 문서 배치 판단

- 정식 위치 없음으로 판단해 `legacy-review-pending/90-meta-handoff/` 하위에 유지한다. 저장소 README의 canonical scope(00~06, service-policy)에 디자인 적용·Pilot 인계 계층이 없고, `service-policy/`는 제품 정책 계층이라 맞지 않는다.
- README 근거: "canonical 경로만 현행 repository Source of Truth로 취급한다", "legacy-review-pending 문서는 보존 자료이며 canonical repository 문서나 Runtime loading 대상으로 간주하지 않는다". 이 폴더는 분석·인계 보존 자료이므로 이 성격과 일치한다.
- 정식 계층 신설이나 승격은 Owner 결정 사항이다.

## 상태

- Finance 분석(`finance-design/`): 작성 완료. 렌더 미검증.
- Pilot C(`pilot-c/`): NOT_RUN.
- Pilot A(`pilot-a/`): 리뷰 PASS 2회 기록 보존. 이미지 제외.
- Claude·Codex 실행: 모두 미실행. 결과표는 빈 템플릿 상태다.

## 관련

- Jira: RPL-146, RPL-148
- PR: #49, #51

## 정제 메모

- 작업 중 사용한 절대 경로는 저장소 상대 표기로 바꿨다. 예: `finance-harness-fe:src/...`, `scratchpad(비보존)`(세션 임시 경로라 보존되지 않음).
- 이메일·토큰·비밀번호·전화번호 패턴을 검색했고 자격 증명·개인정보 항목은 없다. `token` 단어 2건은 디자인 토큰 용어다.
- `pilot-c/task.md`가 참조하는 `pilotA/review.md`는 `pilot-a/review-1st.md`로 보존했다(파일명 변경).
