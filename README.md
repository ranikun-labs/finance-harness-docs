# Finance Harness Documents

Finance Harness의 정책, Lens, Runtime Contract,
Knowledge Architecture 및 관련 내부 문서를 관리하는
private Git repository.

## Canonical repository scope

- 00-core/0-a-safety-core
- 00-core/0-b-lens-infra
- 00-core/0-c-runtime-contract
- 00-core/0-d-knowledge-architecture
- 01-core-analysis-lenses
- 02-asset-class-long-term
- 03-domain-deep-dive
- 06-professional-standards

위 canonical 경로만 현행 repository Source of Truth로 취급한다.

`02-asset-class-long-term`와 `03-domain-deep-dive`의 신규 Product-MVP
Lens 문서는 canonical repository placement 상태이지만, Lens Catalog 등록,
fixture regression, Runtime activation을 완료했다는 의미가 아니다.
해당 문서는 현재 draft / non-routable / non-runtime-loadable 상태를 유지한다.

`06-professional-standards`는 human/professional review 품질 기준의
canonical layer다. 이 계층은 PolicyGuard, Output Validator, Routing,
Runtime, fixture, red-team, regression 계약을 변경하거나 대체하지 않는다.

## Review-pending legacy scope

- legacy-review-pending/05-product-legal-ops
- legacy-review-pending/06-professional-standards
- legacy-review-pending/90-meta-handoff

legacy-review-pending 문서는 보존 자료이며,
canonical repository 문서나 Runtime loading 대상으로 간주하지 않는다.

## Source of Truth

files, files2, canonical, 다운로드 사본,
Fable 임시 결과와 같은 중복·임시 파일은
Source of Truth로 사용하지 않는다.
