# Finance Harness Documents

Finance Harness의 정책, Lens, Runtime Contract,
Knowledge Architecture, Backend Architecture 및 관련 내부 문서를 관리하는
private Git repository.

## Canonical repository scope

- 00-core/0-a-safety-core
- 00-core/0-b-lens-infra
- 00-core/0-c-runtime-contract
- 00-core/0-d-knowledge-architecture
- 01-core-analysis-lenses
- 02-asset-class-long-term
- 03-domain-deep-dive
- 04-backend-architecture
- 05-product-legal-ops
- 06-professional-standards
- service-policy

위 canonical 경로만 현행 repository Source of Truth로 취급한다.

`finance-harness-docs`는 Finance AI Knowledge·Safety 문서뿐 아니라 Finance
Product Service Policy의 canonical 문서도 소유한다. 미래 Finance Backend
Repository는 이 정책을 참조하여 Architecture, Implementation, Runtime
Evidence만 소유하며, Product Service Policy 본문의 Source of Truth를
복제하지 않는다.

`service-policy`는 금융 하네스 웹·앱의 가입, 상품, Promotion, Trial, Usage,
개인정보·마케팅 적용 정책을 다루는 canonical 계층이다. 기존 숫자 기반
문서 구조(00~06)와는 별도의 최상위 책임 계층이며, 세부 경계는
[`service-policy/README.md`](./service-policy/README.md)를 따른다.

`02-asset-class-long-term`와 `03-domain-deep-dive`의 신규 Product-MVP
Lens 문서는 canonical repository placement 상태이지만, Lens Catalog 등록,
fixture regression, Runtime activation을 완료했다는 의미가 아니다.
해당 문서는 현재 draft / non-routable / non-runtime-loadable 상태를 유지한다.

`04-backend-architecture/finance-backend-architecture.md`는 Finance Backend의
Domain 소유권, 외부 Identity/Commerce 논리 경계, CurrentUserPort,
EntitlementPort, Finance DB 소유권, AI Runtime, Shared Platform Audit,
V1 local adapter 방향에 대한 canonical Source of Truth다.

장기 공통 목표 Deployment Unit은 Carelog CRM Server, Finance Harness
Server, Dev Harness Cloud Server, AI Runtime Server, Shared Platform
Server(Identity, Commerce, Audit)다. Finance Harness Server는 독립 제품
서비스이며 Finance Domain 데이터와 제품별 AI 결과·근거의 Source of
Truth를 계속 소유한다.

이 목표 구조는 Finance V1의 실제 Shared Platform·AI Runtime 의존이나
서버·Database·Migration·API Client 구현을 승인하지 않는다. V1은 local
Identity/Entitlement adapter 결정을 유지하며 Billing, Payment,
Subscription, Quota를 구현하지 않는다.

`05-product-legal-ops`는 internal operational baseline으로 canonicalized된
Product / Legal / Operations 계층이다. 법률 자문, 법무 승인, 외부 검토
의견, 공개 출시 승인, 유료 출시 승인이 아니며, public launch와 paid
launch gate는 unresolved 상태를 유지한다. 이 계층은 00-core safety and
Runtime contracts보다 우선하지 않고, service purpose, PolicyGuard,
Output Validator, market-data grounding, Lens routing, Runtime activation,
fixture, red-team, regression 계약을 변경하거나 대체하지 않는다.

`06-professional-standards`는 human/professional review 품질 기준의
canonical layer다. 이 계층은 PolicyGuard, Output Validator, Routing,
Runtime, fixture, red-team, regression 계약을 변경하거나 대체하지 않는다.

## Review-pending legacy scope

- legacy-review-pending/05-product-legal-ops
- legacy-review-pending/06-professional-standards
- legacy-review-pending/90-meta-handoff

legacy-review-pending 문서는 보존 자료이며,
canonical repository 문서나 Runtime loading 대상으로 간주하지 않는다.
`legacy-review-pending/90-meta-handoff`의 Backend 구현 제안도 역사적
참고 자료일 뿐이며, canonical Backend Architecture로 사용하지 않는다.

## Source of Truth

files, files2, canonical, 다운로드 사본,
Fable 임시 결과와 같은 중복·임시 파일은
Source of Truth로 사용하지 않는다.
