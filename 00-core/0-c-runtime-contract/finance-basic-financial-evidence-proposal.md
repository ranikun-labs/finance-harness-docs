# Finance — 기본 금융자료 조회 계약 v0.1

상태: **PROPOSED / NOT_EFFECTIVE** · FIN-04 첫 자료원 · 2026-11-27. 사용자 자유 대화의 기본 근거 제공만 대상. 실시간 가격·뉴스·컨센서스·포트폴리오·자동매매·대규모 수집/RAG 제외. 기존 전체 Agent MVP Memory 범위 유지, 운영 Memory OFF.

## 1. 출시 필수 여부 → Owner 결정 → 구현 인수 조건 → 합성 테스트

| 출시 필수 | Owner 결정 후보 | 구현 인수 조건 | 합성 테스트 예시 |
|---|---|---|---|
| 국내 기업 기본 자료 | 첫 자료원 OpenDART; KOSPI/KOSDAQ 단일 기업, 선택 정기보고서의 매출·영업이익·당기순이익(제공·확인 가능한 경우), 공시 링크 | 승인된 Product-owned read-only adapter. corp_code 및 종목/시장 확인, 보고서/기간/통화/단위/연결·별도 구분과 공시 접수번호 결속 | 가상 A사 사업보고서/반기보고서 값과 원본 링크 일치. 동명이인 기업은 선택 요청 |
| 최신성/정정 | 요청한 기간과 최신 조회 상태를 명시, 최신 확인 실패 시 단정 금지 | 접수일·보고 기간·조회시각 분리, 정정/철회 확인, 금액 비교는 같은 기간/회계기준/통화만, 불일치·누락은 미제공 | 정정 접수 후 옛 금액을 최신으로 답하지 않음; 분기 단독과 누적을 섞지 않음 |
| 이용권한/장애·비용 | API 1종, 제한형 on-demand 조회. 승인된 수치만 모델에 전달 | 서버 인증키 보관/마스킹, 승인 이용/표시·재가공·재제공 및 외부 LLM 전송 조건, 실제 quota·deadline·호출 상한 설정, 오류/부재/제한 구분. raw 데이터와 사용자 대화/식별자 전달 금지 | no-data·quota·timeout·키 오류의 서로 다른 안전 응답; 가짜 접수번호·금액·통화 불일치 출력 차단 |

## 2. 공식 자료원 확인

- 금융감독원 OpenDART [소개](https://opendart.fss.or.kr/intro/main.do)에서 공시/재무정보 공개 활용을 안내한다. [이용약관](https://opendart.fss.or.kr/intro/terms.do) 제6·7·10·16·19·23조 확인: 가입/인증키, 사용 용도, 호출량 제한, 저작권 및 제출인 책임. 공식 안내의 공개 활용 가능성과 우리 유료 서비스 재제공·캐싱·외부 LLM 처리에 필요한 권리 판단은 구별한다. 이용권한은 **확인/승인 대기**; API key 발급·실행 안 함. 제3자 권리, 공시 원문 재전송, 결과물 표시/재배포 범위, 제공자 계약을 Owner/Legal이 확인하기 전 실연결 금지. API 인증키는 서버 Secret, FE/모델/로그에 노출 금지.
- [고유번호 API](https://opendart.fss.or.kr/guide/detail.do?apiGrpCd=DS001&apiId=2019018): corp_code, corp_name, stock_code 매핑. [공시검색](https://opendart.fss.or.kr/guide/detail.do?apiGrpCd=DS001&apiId=2019001): 정기공시, 접수번호/접수일, 최종 보고서 필터, 정정·철회 정보. [단일회사 주요계정](https://opendart.fss.or.kr/guide/detail.do?apiGrpCd=DS003&apiId=2019016): fnlttSinglAcnt.json, 사업연도/보고서 코드, fs_div(CFS/OFS), 계정명, 기간, 당기금액·누적금액, currency, rcept_no. API가 제공하지 않는 정보는 추론으로 채우지 않는다. 일부 기업/계정·자료가 제공되지 않을 수 있다. 재무정보 제공 범위와 정확성 한계는 [정기보고서 재무정보 안내](https://opendart.fss.or.kr/guide/main.do?apiGrpCd=DS003) 참조.

## 3. 최소 흐름/정규화 계약

- Finance PolicyGuard → 승인된 기업·기간 해석 → 기존 제품 소유 Tool allowlist → 기업 식별→공시 목록/정정 확인→주요 계정 조회→정규화 및 원본 참조 검증→Shared AI 실행→Finance 최종 검증. Shared AI는 공통 Evidence/correlation만 제공하고 기업 회계/표시 의미나 자료원 DB를 소유하지 않는다. 금융자료 Tool 호출 인자만 외부로 보낸다. 원문 질문·Journal·identity/tenant·계좌는 자료원에 전송 금지.
- 첫 범위는 사용자 지정 1기업, 1보고 기간(필요 시 전기 비교는 동일 회계 기준과 기간 확인된 경우만); 재무 숫자는 결정적 decimal 처리. `corp_code`, `stock_code` 문자열의 선행 0 보존. 동명이인/불명확 회사명은 추가 선택 요청. 기간·연결/별도·통화·금액 스케일이 확정되지 않으면 계산·비교 금지. CFS 우선, 없으면 OFS로 명시적으로 표시하고 자동 혼합 금지. 원본에 계정이 없거나 금액이 비어 있으면 0이 아님. 단일계정의 의미는 공시에서 확인하고 금융업 등 다른 계정 체계를 무리하게 동일 지표로 매핑하지 않는다.
- 근거는 기업/기간/보고서 코드, 회계구분, 계정, 통화·단위, 수치, 접수번호·접수일·조회시각을 결속한다. 원문 링크 https://dart.fss.or.kr/dsaf001/main.do?rcpNo={검증된 접수번호} 형태는 공식 가이드에서 제공; 모델 생성 URL/번호 불허. 정정/철회 여부와 주요계정 응답의 접수번호를 교차 확인. 최신 보고서와 숫자가 맞는지 확인 불능이면 최신 값으로 제공하지 않는다. 새 자료원이 연결되었다고 최신 시세를 보장하지 않는다.
- API status 013(데이터 없음), 020(요청 제한), 인증/접근 오류, 점검, timeout, 구조 불일치 구분. 실제 계정별 제한 확인 및 서버 호출 budget 구성은 출시 Gate. 가이드의 통상 제한 예시는 계약상 보장량이 아님. 초기 자동 재시도 금지, 사용자 명시 재시도만 현재 정책/예산 확인 후 허용. 필요한 경우 검증된 과거 자료는 날짜와 최신 미확인 상태를 표시할 수 있으나 이를 최신 답변으로 포장하지 않는다. 초기 영속 캐시/대량 수집/Vector DB는 불필요; 캐시 도입 시 별도 이용권한·정정 무효화/TTL 검증. 외부 모델은 승인된 정규화 수치·근거만 사용, API 키/개인정보/원문 보고서 통째로 제공 금지.

## 4. 남은 승인 및 검증

자료원 및 표시 범위, 실제 사용 용도·제3자 권리·재제공/LLM 처리, quota/Secret/비용 제한, 최신성 확인 deadline·실측은 Product/Legal/Security/BE Owner 승인. 합성 테스트 예: A사 100/20/10 (백만원이라는 확인된 Fixture 단위), 별도/연결·누적 혼동·정정·철회·가짜 ID/수치 차단, 013/020/timeout에서 허구의 값 없이 실패 안내. 일반 안전한 질문은 자료 장애가 있어도 가능한 범위에서 교육적 설명, 자료 기반 사실은 확인 불가. 모든 합성/실 API 호출·실모델·E2E는 NOT_RUN. 기존 [Agent Docs #20](https://github.com/ranikun-labs/finance-harness-docs/pull/20)과 [Shared AI Evidence ADR](https://github.com/ranikun-labs/shared-ai-architecture/blob/f60551b46a99c96b05e85737807d137a8c306791/docs/adr/ADR-0002-evidence-provenance-core-domain-boundary.md) 재사용. RPL-127/138의 기존 구현 범위는 확대하지 않는다. FIN-04 전용 기존 Jira 재확인 및 필요 시 Create Gate.
