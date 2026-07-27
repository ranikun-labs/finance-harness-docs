# Finance Privacy and Marketing Gate

## 0. Metadata

```text
policy_id: finance-privacy-and-marketing-gate
document_type: product-service-policy
product_policy_status: draft
legal_review_status: required
architecture_status: not_started
implementation_status: not_started
runtime_support: not_supported
release_status: not_released
launch_values_status: not_applicable
owner: Product Owner (prototype stage: Project Owner)
canonical_refs:
  - ./finance-product-policy.md
  - ./finance-launch-experiment-values.md
  - ../05-product-legal-ops/finance-user-data-policy.md
  - ../05-product-legal-ops/finance-disclaimer-and-copy-guide.md
```

이 문서는 Product Policy 초안이다. 구체적인 동의 문구, Checkbox 카피,
UI, 개인정보처리방침 또는 약관을 확정하지 않으며 법률 결론을 대신하지
않는다. 출시 전 외부 법무 검토가 필요하다.

## 1. 문서 목적과 상위 정책

이 문서는 [`finance-product-policy.md`](./finance-product-policy.md)의
개인정보·마케팅 원칙을 가입, Starter 이용과 추가 혜택 신청 흐름에 적용하기 위한
하위 정책이다.

개인정보 최소수집·목적 제한·보존·삭제의 canonical baseline은
[`finance-user-data-policy.md`](../05-product-legal-ops/finance-user-data-policy.md)가
소유한다. 사용자 대면 고지와 마케팅 카피의 제한은
[`finance-disclaimer-and-copy-guide.md`](../05-product-legal-ops/finance-disclaimer-and-copy-guide.md)가
소유한다. 이 문서는 두 기준을 약화하거나 법률 최종 문구로 대체하지 않는다.

## 2. 핵심 Gate

```text
개인정보 수집·이용 동의
≠
광고성 정보 수신 동의
```

두 동의는 목적, 선택 여부, 기록 및 철회 효과를 구분한다. 개인정보 처리에
필요한 동의를 받았다는 이유로 광고성 정보를 발송할 수 없고, 광고성 정보
수신에 동의했다는 이유로 고지되지 않은 개인정보 처리를 확장할 수 없다.

## 3. 기본 가입의 개인정보 처리

기본 가입은 Platform Account와 Finance Product의 기본 이용에 필요한 최소
정보만 처리한다.

```text
- 전화번호는 기본 가입의 필수 항목이 아니다.
- 전화번호를 제공하지 않아도 Starter 이용을 막지 않는다.
- 향후 혜택 또는 마케팅 가능성을 이유로 Contact 정보를 선수집하지 않는다.
- Provider가 제공한 Contact는 제공 출처와 목적을 구분해 처리한다.
- Finance-specific Consent는 기본 가입 완료와 별도로 관리한다.
```

수집 항목, 목적, 보유 상태, 삭제 및 외부 처리 기준은
[`finance-user-data-policy.md`](../05-product-legal-ops/finance-user-data-policy.md)를
따른다. 미확정 보존기간이나 법적 의무를 이 문서에서 숫자로 고정하지 않는다.

## 4. 전화번호 선택 수집

전화번호는 추가 무료 혜택 신청과 중복 방지에 실제로 필요한 경우에만
목적을 분명히 하고 별도 단계에서 처리한다.

```text
- 전화번호 미제공은 기본 가입이나 Starter 기능의 제한 사유가 아니다.
- 혜택 신청 전에 처리 목적과 필요한 항목을 구분해 고지해야 한다.
- 혜택 신청 목적의 처리를 일반 마케팅 목적 처리로 확대하지 않는다.
- 변경 가능한 재검증 주기, 신청 기간 또는 중복 방지 기간은 이 문서가
  고정하지 않으며 finance-launch-experiment-values.md가 소유한다.
```

정확한 고지 방식, 거부권 표현, 보유기간 및 파기 예외는 외부 법무 검토가
필요하다.

## 5. Provider Contact와 자체 SMS 검증

Provider가 전화번호를 제공하거나 검증 상태를 표시한 것과 Finance가 자체
SMS 절차로 검증한 것은 동일한 증거가 아니다.

| 입력 또는 상태 | Product Policy 의미 |
|---|---|
| Provider 제공 전화번호 | Provider 응답에서 받은 Contact 값 |
| Provider 검증 표시 | Provider 기준의 검증 provenance |
| Finance 자체 SMS 검증 | Finance 목적의 별도 검증 결과 |

Finance 자체 검증이 필요한 Product 목적이라면 Provider 값의 존재만으로
이를 생략하지 않는다. 반대로 자체 SMS 검증 결과를 External Identity나
Platform Account의 대체 식별자로 사용하지 않는다.

Contact 검증 상태의 Shared Identity 소유 여부는
[`finance-onboarding-and-identity-policy.md`](./finance-onboarding-and-identity-policy.md)의
Open Product Decision이다. 이 문서는 물리 저장 구조나 서비스 소유권을
확정하지 않는다.

## 6. 추가 무료 혜택 신청 Gate

Welcome Promotion은 기본 가입과 분리된 선택 혜택이며, 이 문서는 개인정보
처리 관점만 정의한다.

```text
전화번호 검증
→ 추가 무료 혜택 신청에 필요한 개인정보 처리
→ 광고성 정보 수신 동의와 별도 처리
```

사용자는 마케팅 수신에 동의하지 않아도 Welcome Promotion을 신청할 수
있어야 한다. 개인정보 처리 동의 화면이나 흐름에 마케팅 동의를 필수 조건처럼
결합하지 않는다.

Eligibility, 중복 지급 판정, 지급 횟수·수량, Ledger, 취소·회수·재지급,
Trial 조합 및 만료 처리는 후속
`finance-welcome-promotion-policy.md`가 소유한다. 해당 문서가 생성되기
전까지 이 파일명은 계획 참조이며, 이 문서는 지급 정책을 선행 확정하지 않는다.

## 7. 광고성 정보 수신 동의

광고성 정보 수신 동의는 채널별로 구분한다.

```text
- 이메일
- SMS
- 앱 Push
- 카카오 메시지
```

한 채널의 동의를 다른 채널의 동의로 간주하지 않는다. 새로운 채널을
추가할 때 기존 포괄 동의로 자동 포함하지 않으며, 목적과 채널에 맞는 별도
검토를 거친다.

구체적인 채널별 동의 문구, 동의 취득 방식 및 증빙 구조는 이 문서에서
확정하지 않는다. 외부 법무 검토 완료 전에는 법률 요건을 충족했다고
표현하지 않는다.

## 8. 철회 원칙

사용자는 마케팅 수신 동의를 철회할 수 있어야 한다.

```text
- 철회는 해당 광고 채널의 향후 발송을 중단하는 방향으로 적용한다.
- 한 채널의 철회를 다른 채널의 동의 유지 근거로 확대하지 않는다.
- 마케팅 동의 철회를 기본 서비스 이용 제한 사유로 사용하지 않는다.
- 개인정보 삭제, 계정 삭제 및 마케팅 동의 철회는 서로 다른 요청으로 구분한다.
```

철회 방법, 처리 시점, 사용자 고지 및 예외의 정확한 기준은 외부 법무
검토가 필요하다. 이 문서는 구체적인 UI 또는 법적 처리기한을 고정하지 않는다.

## 9. 운영 메시지와 광고 메시지

계정·보안·거래·서비스 운영에 필수적인 메시지와 광고성 메시지를 구분한다.

| 분류 | 목적 | 마케팅 동의와의 관계 |
|---|---|---|
| 운영 메시지 | 계정, 인증, 보안, 요청 처리, 서비스 운영에 필요한 사실 통지 | 광고성 내용으로 확장하지 않는다 |
| 광고 메시지 | Promotion, 이용 유도 또는 상업적 홍보 | 해당 채널의 수신 동의와 철회 상태를 별도로 적용한다 |

운영 메시지라는 이름으로 Promotion이나 이용 유도 내용을 결합하지 않는다.
혼합 목적 메시지의 세부 분류와 허용 범위는 외부 법무 검토가 필요하다.
사용자 대면 표현은
[`finance-disclaimer-and-copy-guide.md`](../05-product-legal-ops/finance-disclaimer-and-copy-guide.md)의
공개 카피 Gate를 따른다.

## 10. Launch Value와 후속 Batch 경계

다음 변경 가능한 값은
[`finance-launch-experiment-values.md`](./finance-launch-experiment-values.md)가
소유하며 이 문서에 고정하지 않는다.

```text
- 전화번호 재검증 주기
- 중복 방지 기간
- Promotion 신청 가능 기간과 지급 관련 수치
- 동의 또는 Contact 처리에 관련된 운영 임계값
```

Trial, AI Usage, Quota, Paid Entitlement, Plan 및 Subscription은 Batch B의
`finance-ai-usage-and-quota-policy.md`와
`finance-trial-and-entitlement-policy.md`가 소유한다.

Welcome Promotion Eligibility와 지급·회수 로직은 Batch C의
`finance-welcome-promotion-policy.md`가 소유한다.

## 11. 법률 검토 Gate

이 문서는 `legal_review_status: required`인 Product Policy 초안이다.
다음 항목은 출시 전 외부 법무 검토가 필요하다.

```text
- 전화번호 수집·이용 목적의 정확한 고지 방식
- 개인정보 수집·이용 동의 구조
- 광고성 정보 수신 동의 구조와 채널별 문구
- 마케팅 동의 철회 방식과 처리 기준
- 운영 메시지와 광고 메시지의 세부 분류
```

구체적인 동의 문구, Checkbox 카피, UI 또는 법률 해석은 이 문서에서
확정하지 않는다. `legal_review_status`는 실제 검토가 완료되고 그 결과가
canonical 문서에 반영되기 전까지 `required`를 유지한다.
