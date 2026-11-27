# Finance — 최소 출시 MVP Conversation 계약 v0.1

상태: **PROPOSED / NOT_EFFECTIVE** · FIN-03 · 2026-11-27. 제품 목표(영속 자유 대화)는 기존 로드맵에서 선택됐으나 저장 정책·물리 계약·구현 위임은 미승인이다. 운영 Memory OFF. 신규 공개 API·DB migration은 승인 대상이지 이 문서로 발효되지 않는다.

## 1. 기존 정본과 범위

- [기존 Agent Docs #20 고정 HEAD](https://github.com/ranikun-labs/finance-harness-docs/tree/09d39e32f4d0ef030f969ac5fe92ebacfa3cd5a5/00-core/0-c-runtime-contract): 같은 유효 세션의 Context와 별도 명시적 Memory 제안. 장기 대화 원문 저장 제외라는 이전 제안과 FIN-03의 영속 대화 목표 차이를 명시한다. #20을 무단 수정하거나 Memory 계약을 폐기하지 않는다.
- [Routing Contract](https://github.com/ranikun-labs/finance-harness-docs/blob/fb058948572c9e943176530cf0fa003d7e901a66/00-core/0-c-runtime-contract/finance-routing-contract.md): Safe 응답 포함 5단 구조·Journal CTA, PolicyGuard 우선, 최종 Output Validator. 새 자연어 답변 형식은 승인된 Routing delta가 필요하다.
- [User Data Policy §10·OQ-U-02](https://github.com/ranikun-labs/finance-harness-docs/blob/fb058948572c9e943176530cf0fa003d7e901a66/05-product-legal-ops/finance-user-data-policy.md): D-07 SESSION-ONLY 기본 제안, D-08 보존 미정, Restricted 파생분은 원천 등급 상속·연동 파기. 기존 정책이 우선하며 이 후보로 저장을 활성화하지 않는다.
- [RPL-127](https://ranikunlabs.atlassian.net/browse/RPL-127) 기존 체크리스트 확인·일반 분석 범위, [RPL-138](https://ranikunlabs.atlassian.net/browse/RPL-138) 선택 일지 비영속 내부 Review 범위 보존. 어느 이슈에도 영속 Conversation을 임의 추가하지 않는다. FIN-03 전용 이슈는 조회 결과에서 확인되지 않았으므로 구현 전 중복 검색·Create Gate 필요 시 승인받는다.

## 2. 출시 필수 여부 → Owner 결정 → 구현 인수 조건 → 합성 테스트

| 출시 필수 | Owner 결정 후보 | 구현 인수 조건 | 합성 테스트 예시 |
|---|---|---|---|
| 자유 대화 | 일반 설명·학습 질문은 자연어, 기존 체크리스트/복기 모드는 유지 | 기존 Intent 정책 우선; 서버가 모드를 선택하고 허용된 Lens/Tool만 사용. 자연어 모드용 구조/근거 Validator 승인; 위험 질문은 기존 거절·redirect, 미검증 문장 미노출 | 금리와 채권 관계 설명은 자연어, 매수 시점 추천은 차단. 기존 confirmed checklist 흐름 회귀 없음 |
| 저장·목록·재접속 | 사용자 선택형 저장 대화. 명시 선택 전 세션 전용 | Finance 소유 Conversation/Message(논리 개념); 로그인 actor·tenant·resource 기준 조회, 목록 최신순과 안정 tie-break/pagination; 선택 저장의 사용자 발화와 검증 완료 답변만 보존. 세션 전용은 영속 목록·원문 캐시 없음 | 새 저장 대화→재로그인→동일 메시지 순서 조회; 다른 계정/tenant의 ID 추측 차단; 세션 전용은 재로그인 목록에 없음 |
| 이어가기·실패 | 같은 대화의 관련 최소 Context, 대화당 생성 1건, buffered final 응답 | 요청 식별·서버 순서·현재 권한·중복/동시성·삭제 세대 확인. 실패·재시도·늦은 결과에 대한 상태 계약과 커밋 후 성공 응답 | 동시 2요청 중 1건만 생성; 동일 요청 재전송·응답 유실로 메시지 2개가 되지 않음; 실패 후 사용자 명시 재시도 |
| 삭제·개인정보 | 사용자 통제 저장, 즉시 접근 및 재사용 차단; 보존/백업/파기 처리 별도 승인 | 대화 및 계정 삭제의 연동 파기, stale callback 차단, 복구 후 삭제 재적용, 최소 감사 이벤트, raw 로그 금지 | 삭제 중 응답 완료→메시지 되살아나지 않음; 삭제 이전 백업 복구 후에도 조회·검색 불가 |
| 전체 Agent MVP 유지 | 명시적 Memory 저장·조회·정정·삭제 목표 유지; 운영 OFF | Conversation과 별도 승인된 장기 Memory Command/Query. 자동 추출·교차 대화 재사용 금지 | 일반 발언은 Memory write 0건; 운영 OFF에서 기억 요청은 실제 저장 성공으로 표시하지 않음 |

## 3. 최소 기술 계약 (논리 모델, 물리 DTO/이름 미확정)

사용자 → Finance auth → 현재 Conversation 인가 → 저장 방식/현재 상태 검사 → PolicyGuard → 기존 승인 Lens/Tool → Shared AI → Finance Evidence/Output Validator → 현재 권한/삭제·선택 세대 재확인 → 최종 저장 및 표시.

- Conversation은 Finance의 본인 소유 객체, 메시지는 Conversation에 귀속된 서버 순번과 역할(사용자/검증된 assistant), 실행 요청/결과 상태에 결속한다. 클라이언트 principal/tenant/순번/assistant text는 권위 아님. 서버 발급 참조는 소유권 증거 아님. 제목은 사용자가 입력하거나 민감 내용을 유출하지 않는 결정적 기본 제목; 제목·목록에도 동일 권한 적용. 수정/분기/공유/전체 검색/자동 요약은 이번 제외.
- 저장형 대화의 새 메시지 요청은 authenticated owner, conversation, 요청 키, 정규화한 입력 및 기대 conversation 상태에 결속. 같은 키·같은 입력은 기존 상태/결과 반환(현재 인가 먼저); 같은 키·다른 입력은 충돌. 동시 새 요청은 기존 생성 중이라고 안내하고 queue를 신설하지 않는다. 요청의 승인·사용자 메시지의 1회 기록·생성중 소유권 획득은 원자적으로 처리. 정책 차단 질문의 원문은 기본 영속화하지 않고 안전한 차단 표시/최소 메타만 허용(Privacy 승인 필요). 실패 시 승인된 사용자 메시지와 실패 상태를 유지하되 검증되지 않은 assistant 본문은 저장하지 않는다. 자동 모델 재호출 금지. 명시 재시도는 기존 사용자 메시지에 결속된 새 실행 시도로 하며 이전 실행이 다시 성공하거나 새 사용자 메시지를 중복 삽입하지 못한다. 비정상 종료의 생성 상태 정리는 deadline과 실행 소유권 검사로 수행; 외부 모델 호출 exactly-once 보장은 주장하지 않는다.
- 모델 호출 중 DB 트랜잭션/행 잠금을 장시간 유지하지 않는다. 완료 시 현재 owner/tenant·conversation active·삭제 세대·실행 소유권·관련 원본 유효성을 조건부로 검사하고 검증 완료 결과만 한 번 확정한다. stale 실행은 폐기. 성공 표시는 영속 저장형의 commit 확인 이후만 가능. 응답 유실 후 조회/동일 키 재전송으로 결과 확인; 결제/사용량의 완전한 exactly-once와 혼동 금지.
- 로그인 세션과 저장된 Conversation 분리: 로그아웃/계정 전환 시 화면·요청·임시 Context/cache를 폐기하고 새 인증으로만 저장 대화를 다시 읽는다. 로그아웃 자체가 승인된 영속 기록을 삭제하지는 않는다. 현재 대화의 승인된 관련 턴만 전달. 타 대화/타 사용자/Memory/전체 Journal 자동 주입 금지. Context 크기·입력 길이·turn budget·요청/모델 deadline·비용 한도는 BE/AI Owner가 고정한 유한값으로 테스트해야 하며 이번 후보가 운영 수치를 확정하지 않는다. 무한 Tool loop/자동 재시도 금지.
- 기존 FIN-02 Journal Review는 비영속 독립 기능을 유지. 초기 저장형 대화에 Restricted Journal 파생 내용을 자동 삽입하거나 복제하지 않는다. 추후 명시 선택 Journal을 저장형 대화에서 사용할 때는 D-08 등급 상속 및 원본별 provenance/삭제 전파를 승인·검증하기 전 차단. 일반 사용자 발화에도 민감정보가 들어갈 수 있으므로 데이터 분류·최소화·고지·외부 전송 제어는 필수. 원본 삭제/수정/권한 철회로 사용할 수 없게 된 근거/파생 결과는 표시·검색·Context에서 제외하고 새 요청에서 재조회한다.
- 기존 안전 규칙은 변경하지 않는다. 기존 체크리스트 승인 경로는 모드 전환으로 우회할 수 없다. 자유 대화에서도 금융 권유·목표가·수량·비중·과거 판단 평가·프로파일링 금지. 자료가 없으면 현재 시장 사실을 만들어내지 않는다. 불완전/정책 위반/근거 위반 출력은 저장·노출 금지. 승인된 안전한 fallback만 제공. 첫 출시에는 검증 전 스트리밍 생략.

## 4. 데이터 정책 결정과 출시 Gate

Product + Privacy Owner가 OQ-U-02 (b) 사용자 선택 저장 및 D-07/D-08의 등급·허용 항목을 승인할 것을 추천. 사용자가 저장형 대화를 선택하기 전에 저장 목적·기간·삭제·외부 처리 고지. 세션 전용 선택 가능. 저장형은 사용자 삭제까지 보존하는 방향(USER-CONTROLLED)과 계정 삭제 연동을 제안하되 무기한 보존을 확정하지 않는다. 구체 최대 보존기간, 백업 만료 상한, 실제 파기 기한/복구 재삭제 절차, 법적 예외, 관리자 접근·감사, provider 보존/사용/지역·동의 등은 Privacy/Security 승인·구현 검증 전 실데이터/closed beta 저장 OFF. 원문 로그/품질 학습/자동 사용자 프로파일링 금지. 삭제 시 즉시 논리적 접근 차단, 후속 물리 파기·백업 자연 소멸은 각각 완료 증거 확보. 새 DB 테이블/API/migration·권한·Writer 위임은 기존 BE 계약 대조 후 승인한다.

## 5. 공통 출시 증거

합성 Fake Model/Fixture AC는 아직 NOT_RUN. 실제 Provider 및 운영 인증·FE→BE→Shared AI E2E·정책/삭제/복구·동시성·사용량/오류 검증이 출시 선행. 기존 #12와 #40 합성 PASS는 이 영속 계약의 PASS 아님. 전체 Agent MVP의 명시적 Memory 및 Quant 후속 목표 보존. 본 문서는 구현 위임이나 Jira 상태 변경을 승인하지 않는다.
