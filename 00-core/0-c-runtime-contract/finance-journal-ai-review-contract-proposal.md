# Finance — 선택 일지 AI 복기 구현 계약 v0.2 (제안)

- 상태: **PROPOSED / NOT_EFFECTIVE**. FIN-02 설계 후보; 구현·정본 승인·운영 활성화 아님.
- 관련 업무: [RPL-138](https://ranikunlabs.atlassian.net/browse/RPL-138). 기존 승인된 내부 Slice와 신규 사용자 노출 Slice를 구분한다.
- 검토일: 2026-11-27. 소유: Finance Product. Shared AI는 실행 메커니즘만 소유한다.
- 운영 Memory 읽기/쓰기 OFF. 실제 사용자 데이터·실모델 호출은 이 문서의 승인 대상이 아니다.

## 1. 기존 계약 및 변경 이유

사용자가 선택한 본인 투자/학습 일지 한 건을 검토하여 기록된 사실, 당시 판단, 확인되지 않은 가정, 누락 근거, 중립적인 복기 질문을 보여준다. 기존 내부 후보는 충분한 재사용 근거가 있으나 영어 고정 문구 검증과 local/test 프로파일 때문에 한국어 사용자 기능을 완료했다고 볼 수 없다.

| 범위 | 기존 계약/근거 | 이번 제안 |
|---|---|---|
| 내부 UseCase | RPL-138: 소유자 조회, PolicyGuard, Shared AI, 비저장, 합성 데이터 | 그대로 유지; 기존 인수 증거 재사용 |
| 출력 | #13의 정확한 Journal 필드 결속 및 영어 고정 템플릿 | 한국어 결정적 표현과 증거 결속 분리; 별도 승인 필요 |
| 사용자 노출 | 기존 RPL-138에서 공개 API/DTO, FE, 운영 Provider 제외 | 별도 승인된 확장 Slice로 분리 |
| 수동 복기 | #27 병합: 사용자 작성 복기의 명시 저장, 원본 revision 검사 및 멱등성 | AI 원문 자동 저장 금지; 기존 API 변경 없음 |
| 장기 Agent | Docs #20: SelectedJournalRead/Context/Memory 제안 | 기존 제안 참조. 이 문서가 Agent 전체 범위 또는 저장 정책을 바꾸지 않음 |

## 2. 사용자 흐름 및 범위

본인 활성 Journal 상세 → 'AI 복기' 요청 → 현재 인가·원본 조회 → 고정 Review 작업의 정책 검사 → 허용된 최소 입력으로 Shared AI 실행 → 구조·근거·정책 검사 → 현재 권한/원본 revision 재확인 → 한국어 결과 표시. 첫 Slice는 사용자가 다른 임의 지시를 첨부하는 자유 질문을 제공하지 않는다. 자유 대화·여러 일지·시장 Tool·Portfolio·Memory·자동 저장은 제외한다.

정상 결과는 현재 화면의 임시 결과다. 원본을 수정하거나 다른 일지로 이동하면 기존 결과는 사용 부적격이 된다. 저장된 수동 복기는 별도 사용자 작성 자산이며 독립적으로 조회 가능하다. 별도 승인된 UX가 없는 한 AI 결과를 수동 복기에 자동 채우거나 저장하지 않는다.

## 3. 입력과 권한 (논리 계약; 신규 wire 필드 확정 아님)

| 항목 | 의미 및 제약 |
|---|---|
| 작업 | 서버가 지정한 Journal Review. 모델·클라이언트는 작업/Skill을 확장하지 못한다. |
| Resource | 선택된 journalId 한 건. 현재 인증된 actor/owner를 서버가 도출; 본문/헤더/모델이 주장한 owner는 무시한다. |
| Locale | 기존 제품 언어 정책과 연결, 이번 인수 기본 ko. 원본 사용자 문자열의 의미를 번역해서 사실로 변경하지 않는다. |
| Source snapshot | 활성 Journal의 ID·현재 revision(기존 DB 제공값)·허용 필드·원본의 시각/시간대·조회 시각. 내부 actor/권한 정보는 provider payload와 분리. |
| Execution | 기존 request correlation, trusted service context, deadline, 제품별 승인 capability 사용. |

첫 후보의 기존 입력은 투자 일지 assetName/action/reasoning/emotion/occurredLocalAt/timeZone, 학습 일지 title/keyContent/openQuestions/occurredLocalAt/timeZone이다. 이를 무조건 외부 제공자에게 전송하는 허용 목록으로 승격하지 않는다. 합성 Fixture에서는 이 범위를 재사용하고 실제 입력 allowlist·최대 길이·민감정보 제거와 제공자 이용 정책은 Owner/Privacy/Security 승인 사항이다. 데이터가 없는 필드는 없다고 표현하며 값을 생성하지 않는다.

권한 및 상태의 일관성: 현재 인증이 실패하면 실행하지 않는다. 외부 사용자의 존재 여부가 노출되지 않도록 기존 Journal not-found 정책을 따른다. 원본 삭제/비활성/권한 철회는 조회 부적격. 복원된 Journal은 새로 현재 상태를 조회한 후 새 요청에서만 사용한다. 권한과 revision은 실행 전 및 결과 공개 직전에 다시 확인한다. 최초 조회한 revision과 공개 직전 revision이 다르거나 현재 권한/원본 확인에 실패하면 이전 응답을 폐기하고 성공을 반환하지 않는다. 재조회만으로 이전 응답을 재검증하거나 노출하지 않는다. 사용자의 명시적 재요청만 새 snapshot으로 실행한다. race를 완전히 제거했다고 주장하지 않으며 실제 API/FE 연동 승인 시 in-flight 폐기·계정 전환·무효화 검증을 포함한다.

## 4. 출력·한국어 검증 계약

**추천 최소 변경:** 영어 정해진 문장을 정규식에서 무작정 삭제하는 것이 아니라, 정확한 필드 결속과 생성 문장의 안전성을 분리한다. 먼저 결정적 한국어 렌더링/승인된 질문 템플릿으로 첫 Slice를 검증한다. 풍부한 자유 생성 복기 문장은 안전성 평가를 통과한 별도 범위로 확장한다.

| 논리 결과 구역 | 필수 의미 / 검증 |
|---|---|
| 기록된 사실 | 서버가 실제 읽은 Journal 필드 및 값과 결속. 기록이 존재한다는 뜻이지 실제 거래/시장 사실의 검증이 아니다. 원문은 사용자 인용으로 표시. |
| 사용자가 작성한 판단 | reasoning 또는 keyContent 등 실제 필드에 정확히 결속. 사용자의 성격, 위험성향, 과거 결정의 정오를 추론하지 않음. |
| 확인되지 않은 가정 | 기록에 표현된 기대·가설 중 검증되지 않은 내용을 구분하고, 원본 밖 사실로 단정하지 않는다. 명시된 가정이 없으면 '이 기록에서 확인된 가정 없음'으로 표현한다. 모델의 새 가정을 사용자 판단으로 귀속하지 않는다. |
| 누락 근거 | 실제 조회 범위에서 어떤 출처·검증 정보가 부족한지 별도 표시한다. 일지에 별도 외부 근거가 없으면 외부 검증 미수행으로 표현하고 다른 곳에도 근거가 없다고 단정하지 않는다. |
| 복기 질문 | 초기에는 승인된 한국어 질문 후보를 필드/누락 조건에 따라 선택. 질문·요약을 생성하는 확장에는 별도 한국어 안전 계약과 adversarial fixtures 필요. |
| 출처 | 서버의 선택 Journal 참조, 사용한 필드, 원본 revision/시각. 출처의 label과 허용 참조는 서버가 구성; 모델이 새 ID/URL/시세를 발명해도 승인하지 않음. |

출력 후보의 필수 구조, nonblank/size, 예상 source refs, correlation, usage, 허용 범위, 정책을 검증한다. 정확히 검증된 원본 인용만 생성 텍스트 검사 예외가 될 수 있고 임의 문자열의 안전 판정을 우회하는 수단이 되어서는 안 된다. Journal 본문은 데이터이며 시스템 지시가 아니다. 전체 생성 문장은 금융 정책 및 한국어 우회 테스트 대상이다. 매수·매도·목표가·수량·비중·과거 결정 정오 판단·프로파일링 금지. 인용은 제품 승인된 노출 규칙에 따르고 링크/HTML/Markdown은 UI에서 안전하게 표시한다. 어떤 한국어 후보가 현재 영어 validator에서 PASS했다고 주장하지 않는다.

## 5. 수명·실패·수동 복기

- 결과는 비영속: 별도 Review/Conversation/Memory 저장, 캐시·원문 로그·프로파일 저장을 기본 도입하지 않는다. 모델 공급자의 데이터 처리/보존은 별도 승인 전 실제 데이터 호출 금지.
- 타인/삭제 일지: 기존 안전한 not-found. 미인증: 기존 인증 실패. PolicyGuard 차단: model/tool 0회. 불명/오류/검증 실패: 부분 답변 숨김, 사용자 친화적인 실패. 구체 HTTP/DTO 매핑은 승인된 API 계약을 따라야 한다.
- timeout/cancel/사용자 화면 이탈/원본 revision 변경: 이전 실행 결과 공개·저장 금지; 요청 ID와 현재 선택의 일치 확인. 취소의 provider-side 중단 여부는 확인 가능한 경우만 표시. 자동 재시도 금지(별도 승인 필요), 명시 재요청 시 현재 권한과 원본을 재검사. 응답 중복은 UI에서 현재 요청과 결속해 배제한다.
- 기존 수동 복기 POST의 `Idempotency-Key`, `sourceRevision`, 사용자 작성 body, revision conflict, parent not-found 의미를 변경하지 않는다. AI 결과 성공은 수동 복기 저장 성공이 아니다. 같은 Journal에서 두 흐름은 독립.

## 6. 합성 AC (검증 기준이며 실행 결과 아님)

### 한국어 합성 기준 — 투자·학습 일지

아래는 허구의 Fixture 값과 의미별 기대 표시다. 각 구역은 별도 의미로 검증하며, 결정적 한국어 템플릿 우선은 Owner 승인 후 적용한다. 물리 DTO 추가를 요구하지 않는다.

| 의미 | 투자 일지 — 원본 필드 → 기대 한국어 | 학습 일지 — 원본 필드 → 기대 한국어 |
|---|---|---|
| 기록된 사실 | `assetName=A기업`, `emotion=조급함` → 'A기업에 관한 기록에 조급함이 기재되어 있습니다.' | `title=금리와 채권`, `openQuestions=[장기채 가격 영향은?]` → '금리와 채권을 공부했고 장기채 가격 영향이 질문으로 남아 있습니다.' |
| 사용자 판단 | `reasoning=실적 개선을 예상해 관심을 가졌다.` → '실적 개선을 예상해 관심을 가졌다고 기록했습니다.' | `keyContent=금리가 내리면 장기채 가격이 항상 오른다고 생각했다.` → '금리가 내리면 장기채 가격이 항상 오른다고 생각했다고 기록했습니다.' |
| 확인되지 않은 가정 | `reasoning`의 실적 개선 기대 → '실적이 실제 개선됐는지는 이 기록만으로 확인되지 않습니다.' | `keyContent`의 항상 오른다는 가정 → '모든 상황에서 성립하는지는 이 기록만으로 확인되지 않습니다.' |
| 누락 근거 | 선택 일지에 공시·실적 출처 없음 → '제공된 일지에 실적 개선을 뒷받침하는 외부 자료 출처가 없습니다. 외부 검증은 수행하지 않았습니다.' | 선택 일지에 교재·자료·조건 출처 없음 → '제공된 일지에 이 판단을 뒷받침하는 자료와 적용 조건이 기록되어 있지 않습니다. 외부 검증은 수행하지 않았습니다.' |
| 복기 질문 | `reasoning`·누락 근거 → '당시 실적 개선을 예상한 근거 자료는 무엇이었나요?' | `openQuestions`·`keyContent` → '어떤 조건에서 이 관계가 달라질 수 있는지 확인했나요?' |
| 출처 | 서버가 선택한 Journal의 해당 필드·revision, 외부 시장자료 미조회 | 서버가 선택한 Journal의 해당 필드·revision, 외부 자료 미조회 |

**정확한 인용 대조 Fixture:** 투자 `reasoning`에 '지금 매수'라는 문구가 실제 기록되어 있으면 검증된 정확한 원문을 '사용자 기록 인용'으로 표시하는 경우에만 제품 노출 정책 범위에서 허용한다. 모델이 같은 문구를 새 매수 지시로 생성하거나 '당신의 판단이 옳았으니 지금 매수하세요'를 정상 결과에 덧붙인 경우에는 출처 ID가 유효하더라도 전체 결과를 숨긴다. 학습 `keyContent`의 정확한 사용자 판단 인용과 모델이 덧붙인 '당신의 투자 판단은 옳았습니다'도 같은 방식으로 대조한다. 원문 이외의 생성 텍스트에 인용 예외를 전파하지 않는다.

| AC | 합성 테스트 기대 |
|---|---|
| F-01 | 본인 활성 투자/학습 Journal 1건만 조회; 위 두 Fixture 각각의 필드·출처와 한국어 의미별 기대 표현 검증. 사실·사용자 판단·확인되지 않은 가정·누락 근거·복기 질문 중 어느 하나가 빠지거나 혼동되면 실패. |
| F-02 | 미인증/타인/삭제/없는 Journal은 AI 실행 전 차단, 외부 존재 여부 비노출 |
| F-03 | 본문 지시문으로 scope/Tool/정책 확장 시도 무효; 정책 거부 시 실행 0회 |
| F-04 | 정확한 원문과 사용자 인용 라벨은 허용 범위에서 유지하고, 동일 문구를 모델이 매매 지시·목표가·과거 판단 평가로 추가하거나 프로파일링을 섞으면 전체 응답 차단. 인용 대조 Fixture 두 유형과 출처 ID가 유효한 위반 출력 포함; 단순 키워드 검사만으로 PASS 불가. |
| F-05 | 허위 source/field/revision, 불일치 correlation, 누락/비정상 usage, 구조/크기 위반, timeout/exception 시 결과 미노출 |
| F-06 | 공개 직전 revision 불일치·원본 수정/삭제/복원·권한 철회·계정/선택 변경·늦은 응답은 이전 결과 폐기. 재조회만으로 기존 결과를 공개하지 않으며 명시 재요청에서만 새 권한/원본 snapshot을 사용. 삭제된 파생 결과 재사용 금지. |
| F-07 | 정상·실패·반복 요청 후 Journal, Review, 수동 복기 및 Memory 저장 변경 0; 수동 복기 기존 명시 저장과 revision/멱등 회귀 통과 |
| F-08 | 합성 Fake Model로 Native/CI 및 필요한 HTTP/FE 범위별 테스트; 고정 HEAD 별도 독립 검수. 공개 API/실모델/운영 E2E는 승인된 범위에서만 별도 수행 |

## 7. 단계별 인수 및 Owner 결정

1. 기존 내부 Slice: #13 HEAD의 합성 인수 증거를 유지; #17 통합 후보에서 검증된 변경 재사용. 현행 BE main `06cbfc83c6bae707313999f68751d37e72f58d62` 조회 스냅샷을 통합 기준 후보로 삼되 작업 직전 갱신한다. #17과 main의 분기는 이전 조회에서 확인됨; Journal 조회·revision·삭제·수동 복기·인증 변경이 있으므로 기존 CI를 새 HEAD PASS로 승격하지 않는다. 원본 PR/Writer 브랜치는 보존한다. #17의 [기존 반환 경로](https://github.com/ranikun-labs/finance-harness-be/blob/c305788e2e1b1ba6575afb230d133ce78be3fb82/src/main/kotlin/labs/ranikun/finance/journal/application/JournalReviewApplicationService.kt#L73-L83)는 최초 조회 Journal로 출력 검증 후 즉시 반환하므로 공개 직전 현재 권한/revision 재확인 및 이전 결과 폐기는 남은 구현 차이다.
2. 한국어 내부 출력 계약: Finance Owner가 결정적 한국어 템플릿 우선과 검사·근거 구조의 최소 변경 범위를 승인한다. 기존 #13 영어 고정 validator 완화가 정책 우회가 되지 않아야 한다. 공개 API/FE 승인과 구분한다.
3. 사용자 노출: Finance Owner 및 AI/Normal Lane은 공개 API/wire/오류/인증·결과 수명·UI 계약과 범위/Primary Issue를 별도 확정하고 인계한다. RPL-138의 현재 Out-of-scope를 이 문서가 변경하지 않는다.
4. Privacy/Security/Owner: 실제 사용자 Journal의 제공자 전송, 필드 allowlist, retention/logging/동의 및 운영 활성화 Gate를 별도 검토한다. 이 결정이 없으면 합성 비운영만 허용한다.

## 8. 근거 및 상태

- [RPL-138](https://ranikunlabs.atlassian.net/browse/RPL-138), [2026-11-20 내부 후보 인수](https://ranikunlabs.atlassian.net/browse/RPL-138?focusedCommentId=10856): #13 내부 합성 범위 수락, 원 구현 Writer Task ID 미확인, 미병합 유지.
- [BE PR #13](https://github.com/ranikun-labs/finance-harness-be/pull/13) HEAD `11adc572a99730bfe16f6c124c21275b01b704be`, [CI](https://github.com/ranikun-labs/finance-harness-be/actions/runs/42148519675), [Validator 고정 소스](https://github.com/ranikun-labs/finance-harness-be/blob/11adc572a99730bfe16f6c124c21275b01b704be/src/main/kotlin/labs/ranikun/finance/journal/adapter/FinanceJournalReviewOutputValidator.kt).
- [BE PR #17](https://github.com/ranikun-labs/finance-harness-be/pull/17) HEAD `c305788e2e1b1ba6575afb230d133ce78be3fb82`, [기존 CI](https://github.com/ranikun-labs/finance-harness-be/actions/runs/42974265187) (PR 보고 기준). Draft/미병합; 현재 main 통합 재검증 필요.
- [BE PR #27](https://github.com/ranikun-labs/finance-harness-be/pull/27), [수동 복기 계약](https://github.com/ranikun-labs/finance-harness-be/blob/main/docs/contracts/journal-filter-and-retrospective-v1.md).
- [Agent 행동·기술 설계 Draft #20](https://github.com/ranikun-labs/finance-harness-docs/pull/20) HEAD `09d39e32f4d0ef030f969ac5fe92ebacfa3cd5a5`; 기존 SelectedJournalRead §5.1과 연결하며 세션/Memory 계약을 재정의하지 않는다.
- [Finance User Data Policy](https://github.com/ranikun-labs/finance-harness-docs/blob/main/05-product-legal-ops/finance-user-data-policy.md), [Routing Contract](https://github.com/ranikun-labs/finance-harness-docs/blob/main/00-core/0-c-runtime-contract/finance-routing-contract.md), [Shared AI ADR-0002](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0002-evidence-provenance-core-domain-boundary.md), [ADR-0004](https://github.com/ranikun-labs/shared-ai-architecture/blob/main/docs/adr/ADR-0004-execution-context-api-tenant-isolation.md).

이번 설계에 대한 Native Test/CI/Independent Review/Runtime E2E = NOT_RUN. Owner Gate = PENDING. 이 문서 게시/검수만으로 Source Merge, 사용자 노출, 운영 활성화를 승인하지 않는다.
