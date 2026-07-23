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

위 canonical 경로만 현행 repository Source of Truth로 취급한다.

`02-asset-class-long-term`와 `03-domain-deep-dive`의 신규 Product-MVP
Lens 문서는 canonical repository placement 상태이지만, Lens Catalog 등록,
fixture regression, Runtime activation을 완료했다는 의미가 아니다.
해당 문서는 현재 draft / non-routable / non-runtime-loadable 상태를 유지한다.

`04-backend-architecture/finance-backend-architecture.md`는 Finance Backend의
Domain 소유권, 외부 Identity/Commerce 논리 경계, CurrentUserPort,
EntitlementPort, V1 local adapter 방향에 대한 canonical Source of Truth다.
이 문서는 실제 Identity/Commerce 서버, Billing/Payment/Quota 또는 물리
MSA 구현을 승인하지 않는다.

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
