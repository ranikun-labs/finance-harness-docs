# 0-C Runtime Contract

본 폴더는 Finance Harness의 런타임 실행 계약과 검증 기준을 담는다.
구현 코드와 backend regression은 별도 작업이며, 이 문서들은 계약과 기준선만 정의한다.

---

## 포함 문서

| 파일 | 역할 |
|---|---|
| `finance-routing-contract.md` | 질문 입력부터 안전 응답 노출까지의 전체 실행 파이프라인 계약. intent → action → lens → template → validator 순서와 분기 규칙을 고정한다. |
| `finance-output-examples.md` | 각 intent/action 경로의 정상 응답 표준형(좋은 예)과 위반 응답 실패형(나쁜 예)을 고정한다. 내부 전용 — 사용자 대면 재사용 금지. |
| `finance-red-team-cases.md` | 정책 우회·오분류·위험 생성을 유도하는 공격 입력에 대한 Red Team 검증 케이스. 내부 테스트 전용 — 사용자 대면 재사용 금지. |
| `finance-policyguard-alignment-v3.md` | Safety Core + Lens Infra + 1차 렌즈 10종 간 contract 정합성 검사 보고서. 내부 검토 전용. |
