# Finance UX Delta — Auth, Journal Creation and Review Handoff

## 0. Metadata

```text
policy_id: finance-ux-delta-auth-journal-handoff
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
  - ./finance-onboarding-and-identity-policy.md
  - ../05-product-legal-ops/finance-user-data-policy.md
  - ../05-product-legal-ops/finance-disclaimer-and-copy-guide.md
```

이 문서는 최신 Independent Product UX Review의
`UX_DELTA_APPROVED_FOR_FE_CANONICALIZATION` 결과를 제품·UX 계약으로 고정한다.
이 문서는 FE 구현, Backend/API/Auth protocol, persistence schema, runtime 지원 또는
출시 승인을 의미하지 않는다.

우선순위는 다음과 같다.

```text
Finance Safety / Product Policy
> this UX contract
> FE-local implementation guidance
> historical prototype or review evidence
```

Review transcript와 prototype은 입력 근거이며 canonical source가 아니다. Product
Policy, Identity Policy, Backend Architecture 또는 Safety Contract와 충돌하면 해당
owner가 우선한다.

## 1. Product IA and task

Primary IA는 **검토 / 저널**을 유지한다.

```text
검토
→ 판단 기록 또는 학습 노트
→ 저널 상세
→ 복기
↺ 다음 검토 개선
```

핵심 사용자 task는 다음과 같다.

> 무엇을 판단했고 왜 그렇게 판단했는지 남긴다.

Review 결과는 사용자가 명시적으로 선택하기 전까지 transient/operational result다.
Review 전체를 자동으로 Journal로 저장하지 않는다.

## 2. User-facing naming and discriminator

| Internal discriminator | User-facing name | Meaning                                           |
| ---------------------- | ---------------- | ------------------------------------------------- |
| `investment`           | 판단 기록        | 무엇을 판단했고 왜 그렇게 판단했는지 남기는 기록  |
| `study`                | 학습 노트        | 검토에서 배운 내용과 더 확인할 질문을 남기는 기록 |

`investment | study` discriminator는 기존 내부 계약으로 유지한다. 사용자에게
`투자 기록` 또는 `공부 노트`를 기본 이름으로 노출하지 않는다.

Investment Action과 Emotion taxonomy는 기존 계약을 유지한다.

- Action: 관심 / 관망 / 매수 / 매도
- Emotion: FOMO / 불안 / 확신 / 관망 / 혼란

새 enum을 만들지 않는다. Reasoning 안의 사용자 작성 표현은 taxonomy가 아니다.

## 3. Entry and focused editor

### 3.1 Direct Entry

Direct Entry는 다음 순서다.

```text
저널
→ 새 기록
→ 판단 기록 / 학습 노트 한 번 선택
→ 선택된 Focused Editor
```

Entry Choice는 독립 surface이며 Link 또는 Button으로 제공한다. Editor 안에서
`[판단 기록 | 학습 노트]`를 persistent tab처럼 항상 노출하지 않는다.

필요한 경우 `기록 유형 변경`을 secondary action으로 제공할 수 있다. 사용자가 이미
입력한 dirty state에서는 기존 warning을 유지한다. 이 action은 type 선택의 UX이며
새 route architecture나 transport contract를 만들지 않는다.

### 3.2 Review Entry

Review Result 하단에는 `기록으로 이어가기` surface를 둔다.

- **판단 기록 남기기** — 검토한 내용을 바탕으로 내 판단과 이유를 기록한다.
- **학습 노트로 남기기** — 검토에서 배운 내용과 더 확인할 질문을 정리한다.

각 CTA는 해당 Focused Editor로 직접 이동한다. Guest가 이 CTA를 선택하는 경우 Auth
Entry를 거친 뒤 원래 선택과 intent를 resume할 수 있어야 한다.

판단 기록 CTA에서 허용되는 draft 후보는 명시적으로 확인된 대상, Original Question
context, Decision Context candidate다. Action, Reasoning, Emotion은 user-owned
입력으로 두며 Review 결과가 자동으로 prefill하지 않는다.

## 4. Focused Editor contracts

### 4.1 판단 기록

표시 hierarchy는 다음 순서를 따른다.

1. 대상
2. Action
3. Reasoning
4. Emotion (optional)
5. 기록 시점
6. Review-derived인 경우 Decision Context

Reasoning은 핵심 user-owned body다. Action, Reasoning, Emotion은 Review 결과가
자동으로 결정하거나 prefill하지 않는다.

`occurredAt`은 현재 local date-time을 기본값으로 제공할 수 있지만 Form 상단의
dominant field로 크게 노출하지 않는다. `기록 시점`, `방금 전 · 변경`과 같은
secondary presentation을 사용한다. 내부 `occurredAt` 의미와 기존 RPL-48 mapping은
변경하지 않는다.

### 4.2 학습 노트

Direct Entry의 최소 구조는 다음과 같다.

- 무엇을 배웠나요?
- 핵심 정리
- 더 확인할 것 (optional)
- 기록 시점 (secondary)

Review-derived Learning Note는 빈 Form으로 시작하지 않을 수 있다. 단, 사용자가
Review 화면에서 실제로 본 stable content만 Editable Draft 후보가 될 수 있다.

```text
title        ← Original Question
key content  ← visible Analysis Guidance / learning 핵심
open questions ← Unknown / Verification Targets
```

이 draft는 `Review/AI Result → Editable Draft → User Edit → User-owned Durable Note`
순서다.

- 모든 prefill은 사용자가 수정·삭제할 수 있다.
- 전체 Review 결과를 복제하지 않는다.
- 숨겨진 AI output을 prefill하지 않는다.
- 자동 저장하지 않는다.
- Stable Review Field Contract가 없으면 Backend DTO를 추측하지 않는다.

표현 의미는 다음과 같다.

> 검토 결과에서 가져온 초안입니다. 저장하기 전에 자유롭게 수정하거나 지울 수
> 있습니다.

## 5. Decision Context

Decision Context는 Live Review Link가 아니라 immutable historical snapshot이다.

- Review → 판단 기록 진입에서만 노출한다.
- Direct Journal Entry에서는 숨긴다.
- Review-derived 판단 기록에서는 기본 ON 후보를 사용하되 사용자가 OFF할 수 있다.
- ON의 최소 context는 Original Question, checklist wording/version, confirmation
  state, capturedAt이다.
- 이 네 가지 minimum field는 하나의 historical minimum으로 함께 남기며 개별적으로
  deselect할 수 없다. Optional evidence는 별도 선택 영역이다.
- 당시 상태를 그대로 보여주며 이후 live Review와 동기화하지 않는다.
- AI Review 전체 결과나 전체 session transcript를 저장하지 않는다.

권장 presentation은 다음 의미를 전달한다.

```text
검토 맥락 함께 남기기 [ON]
이 판단을 내릴 때 참고한 검토 맥락도 함께 남깁니다.
질문, 체크리스트 버전, 확인 상태와 기록 시점을 당시 상태 그대로 남깁니다.
AI 검토 결과 전체를 저장하지는 않습니다.
```

Decision Context persistence DTO, schema, API 또는 저장 방식은 이 문서에서
확정하지 않는다.

## 6. Auth Entry and resume UX

Guest Review는 제한적으로 허용한다.

Finance Auth Entry의 Product UX 방향은 **OAuth-first**다. Login과 Signup을 별도의
primary user journey로 나누지 않고 하나의 provider-neutral Auth Entry에서 기존
사용자의 authentication 또는 첫 이용자의 registration 흐름으로 이어지게 한다.
UI는 특정 provider를 전제로 하지 않으며, Kakao/Google/Apple 등 exact provider는
현재 확정하지 않는다.

- Review Start: guest 가능
- Review Result: guest 가능
- Journal List / Detail: Auth required
- Direct Journal Entry: Editor 진입 전 Auth required

Review → Journal handoff에서 Guest가 CTA를 선택하면 다음 semantic flow를 사용한다.

```text
Guest Review
→ Auth Entry
→ Login / Signup through one provider-neutral entry
→ selected Editor + original intent/context resume
```

Auth Entry의 user-facing concept는 다음과 같다.

> 계속하려면 로그인해 주세요. 처음 이용하는 경우 계정이 함께 만들어집니다.

로그인 취소는 기존 Review Result로 돌아간다. 인증 실패도 Review Result를 파괴하지
않는다. 성공 후 무조건 `/app`으로 보내지 않는다.

Resume candidate는 original intended route, selected record type, review context,
review-derived draft intent, Decision Context choice다. 실제 state storage와 resume
protocol은 Shared Identity와 FE integration contract가 소유한다.

이 문서는 cookie/bearer, issuer, audience, JWKS, OAuth callback, provider, Capacitor
callback 또는 session handoff를 결정하지 않는다.

## 7. Responsive and accessibility contract

### 7.1 Responsive

| Viewport            | Contract                                                                                                                              |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Phone 393           | Entry Choice independent surface; Editor single column; no persistent type switch; Decision Context collapsible; occurredAt secondary |
| Tablet Portrait 834 | No 480px phone cap; wider readable Editor; single column                                                                              |
| Portrait 1024×1366  | Wider Editor; no forced rail; no excessive empty margin                                                                               |
| Landscape 1194      | Main Editor + Context/Record Info, max two major surfaces                                                                             |
| Desktop 1360+       | Main Editor + Context Rail, max two major surfaces; no 3-pane                                                                         |

Review Result와 Editor를 무조건 동시에 2-pane으로 열지 않는다. Trading Terminal
density를 만들지 않는다.

### 7.2 Accessibility and i18n

기존 FE accessibility contract를 시각 polish보다 우선한다.

- native label, fieldset/legend, `aria-invalid`, `aria-describedby`
- error summary와 first-error focus
- radio keyboard focus, visible focus, 44px 이상 target
- keyboard submit, text-based errors, dirty warning
- ko/en semantic parity
- Entry Choice Link/Button semantics
- Context toggle native semantics
- prefill 후 accessible status
- Auth Resume 후 heading focus
- DOM order와 visual rail order 일치

## 8. Deferred Contract Matrix

| Deferred item                           | Owner                                   | Why deferred                                                   | Required input                                                         |
| --------------------------------------- | --------------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Auth protocol and provider integration  | Shared Identity / Backend Architecture  | UX concept만 승인됐고 protocol은 제품 UX owner가 소유하지 않음 | Shared Identity contract, provider decision, security review           |
| Auth Resume persistence/transport       | Shared Identity + FE integration        | Resume candidate만 정의됐고 저장·session handoff는 미확정      | Auth state contract, lifecycle, failure/cancel semantics               |
| Decision Context persistence DTO/schema | Finance Backend Architecture            | immutable presentation과 persistence는 별도 경계               | approved Journal/Context API, data retention and privacy decision      |
| Review API DTO and lifecycle            | Review Engine workstream                | FE는 production API seam을 선행하지 않음                       | Backend Phase 1 Review contract, section/status/error mapping          |
| Stable Review Prefill Field Contract    | Review Engine + Product/UX              | visible field 후보만 있으며 stable DTO는 미확정                | Review section ownership, provenance, user-edit rules                  |
| Production Journal Create activation    | Finance Backend + RPL-48 contract owner | existing transport is protected and Production Create is OFF   | backend readiness, integration approval, RPL-48 compatibility evidence |
| Journal List/Detail API                 | Finance Backend Architecture            | current FE presentation/fixture is not production read model   | owner-scoped read model, auth and not-found contract                   |
| Guest Review exact limits               | Product + Entitlement/Usage owners      | “limited” is semantic only; no numbers are approved            | entitlement policy, abuse/cost evidence, legal/privacy review          |

Deferred item을 완료된 것으로 표시하거나 이 문서에서 DTO·endpoint·schema를
발명하지 않는다.

## 9. Protected boundaries

다음 기존 계약은 이 UX Delta로 변경하지 않는다.

- `JournalCreateCommand`
- `JournalCreateRequest`
- `JournalCreatePort`
- `HttpJournalCreatePort`
- `POST /finance/journals`
- `create_failed` / `invalid_result`
- internal discriminator `investment | study`
- Production Create: OFF
- RPL-68 Primary IA: 검토 / 저널

Transport가 UX 구현과 충돌하면 직접 변경하지 않고 별도 Future Contract Delta로
보고한다.

## 10. Implementation slice proposal

이 제안은 구현 승인이 아니라 FE 작업 경계를 정리한 것이다.

| Slice | Scope                                                                                                         | Explicit non-goals                                          |
| ----- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| UX-1  | Direct Entry Choice, naming, focused route entry, persistent switch removal                                   | transport, auth protocol, backend                           |
| UX-2  | 판단 기록/학습 노트 focused editor hierarchy, occurredAt secondary, validation and dirty warning preservation | new taxonomy, API/persistence                               |
| UX-3  | Review Handoff CTAs and editable Learning Draft fixture/view-model semantics                                  | hidden prefill, full-result copy, autosave, Review DTO      |
| UX-4  | Review-derived Decision Context presentation plus Auth Entry/resume seam                                      | persistence DTO, OAuth/session protocol, backend activation |
| UX-5  | Responsive, A11Y, ko/en regression and protected-contract verification                                        | architecture redesign, Production Create activation         |

각 slice는 실제 구현 전에 필요한 owner contract가 준비됐는지 별도로 확인한다.

## 11. Related canonical documents

- [Finance Product Policy](./finance-product-policy.md)
- [Finance Onboarding and Identity Policy](./finance-onboarding-and-identity-policy.md)
- Future Review Engine Contract — Deferred (owner: Finance Review Engine workstream)
- [Finance Core Loop and Domain Contract](../04-backend-architecture/finance-core-loop-and-domain-contract.md)
- [Finance User Data Policy](../05-product-legal-ops/finance-user-data-policy.md)
