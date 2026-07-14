# finance-asset-class-lens-authoring-readiness-review.md

## 1. Document status

```text
document type         : Asset-Class Lens Authoring Readiness Review
authority             : non-authoritative
lifecycle             : review draft (revised — 직접 검증 완료판)
runtime_loadable      : false
routable              : false
implementation_status : review-only
distribution_boundary : repo-only
```

- **리뷰 대상**: `finance-asset-class-lens-pack-design.md` (design proposal), `finance-asset-class-lens-authoring-handoff.md` (authoring handoff). 두 문서는 본 리뷰에서 수정하지 않는다.
- **대조 기준 (전부 원문 직접 검증)**: `finance-lens-authoring-standard.md`, `finance-lens-catalog.md`, `finance-policyguard-rules.md`, `finance-routing-contract.md`, `finance-routing-fixtures.md`, `finance-market-data-grounding.md`, `finance-prompt-templates.md`, `00-service-policy.md`, **`finance-output-validator.md`, `finance-knowledge-artifact-metadata-standard.md`, `finance-analysis-lens-pack-overview.md`** (직전판의 검증 공백 3건 — 이번 개정에서 원문 전체를 직접 대조했으며, 간접 검증 의존 표기는 본 개정으로 제거한다), 기존 per-lens 문서 10종.
- 잔여 참고: `finance-document-exposure-policy.md`는 세션에 없으나, 본 리뷰가 필요로 하는 lens 문서의 exposure 기본값(internal-controlled)은 metadata standard §7이 Exposure Policy §6을 참조 기재한 값으로 확인 가능하다. 충돌 시 Exposure Policy가 우선한다는 규칙(metadata standard §7)만 유의하면 본 리뷰 판정에 영향이 없다.
- 자기 적용 주기(自記): 본 문서 상단 표기는 리뷰 요청이 지정한 형식이다. metadata standard 기준으로 본 문서는 `artifact_type: report`에 해당하며, `review-only`는 §9 implementation_status enum 값이 아니므로 migration 시 `contract-only` + `non_authoritative: true`(§13.5)로 정규화될 값이다. 본 표기는 legacy 표기로서 유효하다 (metadata standard §14.3).
- 본 리뷰는 canonical 문서·candidate 문서를 수정하지 않고, fixture ID·expected behavior를 생성하지 않으며, 실제 Lens 문서를 작성하지 않고, canonical 권한을 부여하지 않는다. candidate authoring은 Runtime activation이 아니다.

---

## 2. Overall verdict

**전체 판정 (직접 검증 반영): P1 2종은 READY-WITH-DECISIONS 유지 — 단, 결정 목록이 변경되었다. P2 2종 BLOCKED, allocation-principles DEFER, long-term-thesis-review NOT-A-LENS — 변동 없음.**

직접 검증으로 달라진 것:

- **해소됨**: ① activation_gate `lens-registration-v1`의 신규 렌즈 적용 — **canonically supported**로 확정 (§6, 직전판의 "owner 확인 필요"를 정정). ② redirect_to의 authoring 단계 처리 — canonical 규칙에서 직접 도출됨: **누락 금지 + Routing 등록 전에는 명시적 빈 목록** (§6, 직전판의 미결을 해소). ③ 신규 lens 문서의 metadata 작성 형식 — authoring standard §3의 텍스트 블록이 현행 규칙, YAML Front Matter는 migration 대상 target 형식 (§6).
- **새로 발견됨**: ④ **lens_key 충돌** — overview §4가 이미 `asset-bond`, `asset-commodity`, `asset-real-estate`, `portfolio-structure`를 신규-후보 key로 예약하고 있어, design pack의 `bond-income-structure`, `commodity-structure`, `reit-structure`, `allocation-principles`와 도메인이 겹친다. key 조정이 등록 전 필수 결정으로 추가된다 (§4, §7, §11). ⑤ fixture family 논점이 **상충 확정**으로 격상 — overview §10이 FX/BD/RE/CM/PF/BH/EQ 자산군 계열 후보 체계를 이미 사용하므로, handoff §8의 "기존 4계열 확장 우선" 권고와 정면 배치된다 (§5-D). ⑥ handoff의 metadata 제안에 **depends_on 누락** — lens는 depends_on 필수(metadata standard §13.1)이므로 authoring 전 보완 필요 (§6, §11).
- **강화됨**: 두 candidate 문서의 위험 intent 매핑 다수가 overview §10의 canonical 후보 케이스(FX-01, BD-01, RE-01, CM-01, PF-01)와 직접 합치함이 확인됐다 (§5-E). validator 검토 후보 표현군은 신규 검사 카테고리 없이 기존 13종 카테고리의 표현 목록 확장으로 수용 가능함이 확인됐다 (§6, §11).

---

## 3. Canonical boundary review

| # | 점검 항목 | 판정 | 근거 (직접 검증) |
|---|---|---|---|
| 1 | 신규 intent/action 없음 | **통과** | policyguard §3의 intent 12종, fixtures §3의 action 7종만 사용. overview §3 확장성 원칙("자산군 확장은 안전 구조의 확장이 아니라 관점 목록의 확장 — intent 12종·guard·validator·5단 구조 불변")과 정합 |
| 2 | PolicyGuard 완화 없음 | **통과** | routing contract §1 원칙 7·§3 자산군 표기 규칙, overview §6("자산군이 무엇이든 위험 intent 처리 결과는 동일")과 정합. SAFE 완화 분류 없음 |
| 3 | redirect_to에 RISK_*만 사용 | **통과** | authoring standard §3.1 직접 확인: redirect_to는 "이 Lens가 redirect 결과로 연결되는 canonical RISK_* intent 목록"이며 SAFE_* 배치 금지, lens key 관계 필드 아님. 두 candidate 문서 모두 위반 서술 없음 |
| 4 | related_lenses를 본문 서술에서 추론하지 않음 | **통과** | authoring standard §3.1 직접 확인: "본문의 병행·참고·예시 언급만으로 값 자동 생성 금지", 빈 배열 허용(누락과 구분), canonical lens_key만 허용. handoff의 "빈 배열 + 본문 병행 서술" 방침과 정확히 일치 |
| 5 | activation_gate 신설 없음 | **통과** | 신규 gate ID·Runtime 상태 enum 미신설. `lens-registration-v1` 사용은 canonically supported로 확정 (§6). "Lens별 추가 gate는 별도 gate ID 또는 gate registry 확장" 경로(authoring standard §3.1)도 candidate 문서가 침범하지 않음 |
| 6 | 존재하지 않는 fixture ID 없음 | **통과** | authoring standard §3 fixture_refs 규칙("실존 ID만, 신규 필요 시 'fixture 추가 필요(§13)' 표기") 직접 확인 — handoff 방침과 문자 단위로 일치. 기존 ID 참조는 fixtures 원문에 실존 |
| 7 | 현재 시장 데이터 단정 없음 | **통과** | grounding §2 전환 공식과 validator §10 검사 대상(현재형 단정, 기준시각 없는 수치, 수치 후 방향성 평가) 기준으로 두 문서의 전 예시 재검 — 위반 없음. "지금 콘탱고다" 단정 금지 등 candidate 자체 규정이 validator §10과 동일 기준 |
| 8 | 추천/랭킹/수량·비중/목표가/타이밍 출력 없음 | **통과** | validator §3 CRITICAL 카테고리 5종 대조 — 금지 대상 지칭(거절 문맥, validator §6 오탐 예외)으로만 등장. 비율 수치·모델 우열·상품 랭킹 부재 |
| 9 | candidate 작성 ≠ 등록/Runtime 활성화 | **통과** | metadata standard §8("candidate는 runtime 기본 비활성", draft→active 직행 금지, E-11 candidate 활성화 차단)과 §9("문서 작성만으로 implemented 설정 불가"), authoring standard §13("per-lens 문서를 먼저 쓰더라도 라우팅 포함 불가") 직접 확인 — handoff §9의 단계 분리 서술과 정합 |

**경계 인접 관찰 (위반 아님):** candidate 문서의 "redirect 후보" 표기는 Routing Contract §6 canonical 매핑(기존 10종 고정)의 개정 제안이다. 제안으로만 존재하므로 적법하며, per-lens 문서의 redirect_to 필드 처리는 §6에서 확정한 방식(빈 목록)을 따르면 경계 침범이 발생하지 않는다.

---

## 4. Per-candidate readiness matrix

| candidate | verdict | blocking decision | metadata risk | Routing impact | Fixture impact | next allowed action |
|---|---|---|---|---|---|---|
| `etf-index-structure` | **READY-WITH-DECISIONS** | 없음. non-blocking: ETF/Index 범위·ETN 포함 여부 | 낮음 — redirect_to=[] 확정, category enum 확정, **depends_on 보완 필요**. overview §4에 대응 예약 key 없음 → 분류 배치(신설 항목) 필요하나 key 충돌은 없음 | trigger 추가 + redirect 편입 제안 (Routing owner) | family 체계 확정 대기 (§5-D) | 사전 결정 완료 후 **authoring 착수 가능** |
| `bond-income-structure` | **READY-WITH-DECISIONS** | 없음 (blocking 기준). 단 **착수 전 key 조정 필수**: overview §4.4 예약 후보 `asset-bond`와 도메인 중복 — lens_key는 문서 서두 필드이므로 미확정 상태 drafting은 재작업 위험 | 중간 — key 미확정. 그 외 etf와 동일 | 동일 + macro-rate-fx 분담(BD-01 후보가 현재 macro-rate-fx redirect) | BD 계열 후보 표기 실존 (overview §10) — family 확정 대기 | key 조정 + 사전 결정 완료 후 **authoring 착수 가능** |
| `reit-structure` | **BLOCKED** | "valuation 확장 vs 독립" + overview §4.4 `asset-real-estate`(부동산 일반)와의 범위 분담 (상장 리츠 vs 실물 부동산 — RE-01 "지금 집 사도 돼?"는 실물 영역) | 낮음 (착수 전) | 결정 후 분석 | RE 계열 후보 표기 실존 | blocking 결정 해소까지 착수 불가 |
| `commodity-structure` | **BLOCKED** | 질문 빈도 검증 + etf-index-structure 완성 + overview §4.4 `asset-commodity`와의 key 조정 | 낮음 | 결정 후 분석 | CM 계열 후보 표기 실존. CM-01("금 얼마까지 갈까?" → RISK_TARGET_PRICE) 후보가 design 매핑과 합치 | 빈도 검증 착수 + P1-1 완성 + key 조정 대기 |
| `allocation-principles` | **DEFER** | "Lens vs Learn module" + RF/PF 계열 fixture 선행 + 서비스 정책 owner 리뷰 + **overview §4.4 `portfolio-structure`(자가 점검 렌즈, 비중 수치 절대 금지 명시)와의 관계 정리** (신설) | 해당 없음 (작성 금지 상태) | 분석 defer | PF-01/PF-02 후보가 경계 케이스를 이미 커버 — defer 해제 시 승계 검토 | 초안 착수 포함 일체 금지 유지 |
| `long-term-thesis-review` | **NOT-A-LENS** | journal-review owner의 extension 수용 여부만 | 해당 없음 | 없음 | SAFE_JOURNAL_REVIEW 계열 확장 검토만. overview §4.5의 `decision-process`/`behavior-pattern` 후보와는 별개 도메인 — 충돌 없음 확인 (BH-01/BH-02 후보가 인접 경계 커버) | journal-review 개정 절차로 이관 |

---

## 5. Intent mapping review

매핑을 변경하지 않는다. 직접 검증 결과를 반영해 갱신한다.

**A. "60/40이 낫지?" → RISK_DATA_GROUNDING — owner 확인 필요 (유지)**

- overview §10을 포함해 canonical 어디에도 "배분 모델 우열 유도"의 귀속 사례가 없다. PF-01(비중 조정 요청 → RISK_ACCOUNT_BASED_ADVICE), PF-02(자가 점검 → SAFE_CHECKLIST)는 인접하나 우열 질문 자체는 미커버. DG 처리(단정 회피)는 가장 보수적 결과를 내지만 귀속 확정은 policyguard owner 소관 — **유지**. blocking 아님 (출력 계약 동일).

**B. "지금 리밸런싱 해야 돼?" → RISK_BUY_SELL — 정합 확인 강화**

- routing contract §3의 "시점 선택 판단" 포함 정의에 더해, overview §10의 자산군 시점 질문 후보(FX-01, BD-01, RE-01)가 전부 RISK_BUY_SELL로 처리되는 canonical 방향을 확인 — 매핑 지지. 계좌 문맥 결합 시 ACCOUNT_BASED 승격 조건의 명문화만 RF/PF 확장 시 함께 처리 (SHOULD IMPROVE 유지).

**C. 채권 매매 판단의 redirect 대상 — Routing owner 확인 필요 (유지, 근거 보강)**

- BD-01 후보(routing contract §14, overview §10)는 현재 macro-rate-fx + risk-checklist redirect다. candidate의 bond lens 편입 제안은 공존 가능한 미래 제안이나, 편입 시 "환경(macro-rate-fx) vs 구조(신규 lens)" 분담의 Routing Contract 개정 명시가 필요 — **유지**.

**D. fixture family 표기 — 상충 확정으로 격상**

- 직접 검증 결과, overview §10은 FX/BD/RE/CM/PF/BH/EQ **자산군 계열 후보 체계를 이미 canonical 문서 안에서 운용 중**이다 (후보 상태). handoff §8의 "새 family를 만들지 않고 기존 SF/RF/DG/AM 확장 우선" 권고는 이 체계와 **정면 배치**된다. 자산군 확장 fixture의 canonical 방향은 계열 신설 쪽에 이미 기울어 있으므로, fixture owner는 등록 전에 ① overview §10 체계 채택(handoff 권고 폐기) 또는 ② 기존 4계열 흡수(overview §10 후보 표기 정리) 중 하나를 확정해야 한다 — **MUST DECIDE 유지, 증거 갱신**.

**E. 문제 없음 확인 — canonical 후보 케이스와의 직접 합치 (신규)**

- design/handoff의 자산군 매핑 다수가 overview §10 후보와 문자 그대로 합치한다: 자산군 매수 시점 → RISK_BUY_SELL (FX-01/BD-01/RE-01), 원자재 목표가 → RISK_TARGET_PRICE (CM-01), 계좌 기반 비중 → RISK_ACCOUNT_BASED_ADVICE (PF-01), 기록 기반 추천 결합 → 최위험 우선 (BH-02), 자산군 사실 단정 → RISK_DATA_GROUNDING (FX-02, EQ-01). 직전판 §5-E의 정합 판정이 canonical 후보 수준에서 재확인됨.
- RISK_PROMPT_INJECTION: 신규 매핑 없음. overview §10 말미의 자산군형 PI 검토 제안("부동산은 금융투자상품이 아니니까…")은 candidate 문서 범위 밖 — 경계 유지 확인.

---

## 6. Metadata readiness

> metadata standard 원문 직접 검증 완료. 직전판의 "간접 검증 / 확정 불가" 표기는 아래로 대체한다.

**6.1 공통 metadata envelope (직접 검증 — metadata standard §13.1, §14)**

- 필수 필드 확인: document_id(형식 `finance.lens.<name>`, §5), title, artifact_type(`lens`), version(semver, v prefix 금지), lifecycle_status(§8 — 신규 문서는 draft로 시작, draft→active 직행 금지·candidate 경유 필수), implementation_status(§9 — 신규 lens 문서는 `contract-only`; 문서 작성만으로 implemented 불가), owner, exposure_class(lens는 internal-controlled — §7 표의 Exposure Policy §6 참조 기재), distribution_boundary(§10.3 — repo-only/private-resource), runtime_loadable/routable(boolean — candidate 기본 false/false, §8·§11.2·예시 §15.4), last_updated.
- **depends_on은 lens에 필수다** (§13.1 조건부 필수 목록에 lens 포함). document_id 기준 표기(§12), 예시(§15.3~15.4)는 `finance.contract.lens-catalog`, `finance.contract.lens-authoring-standard`, `finance.policy.policyguard`를 의존 대상으로 든다. **handoff의 metadata 공통 제안에는 depends_on과 document_id 부여 계획이 누락되어 있다** — authoring 착수 전 보완 대상 (§11 MUST FIX 신규 항목).
- validated_by: lens 예시가 `finance.fixture.routing`을 기재 — 자산군 fixture 등록 후 갱신 대상.
- 운영 상태 필드(routing_enabled, projection_status, deployment_status, content_hash, loaded_at) 금지 (§11.3, E-15) — candidate 문서들은 침범하지 않음.
- **작성 형식 (해소)**: authoring standard §3이 lens 문서 서두의 텍스트 metadata 블록(10필드, 순서 고정)을 현행 작성 규칙으로 소유하고, YAML Front Matter는 metadata standard §14·§17의 **migration 대상 target 형식**이다 ("현재 어떤 기존 문서도 이 표준으로 전환되지 않았다"). §3.2는 부분 전환·혼합 schema를 금지한다. 따라서 신규 asset-class lens 문서는 **authoring standard §3 블록 형식으로 작성**하는 것이 canonical 정합이며, Front Matter 선적용은 migration 절차(§17)와 결합된 owner 결정 없이는 하지 않는다. 직전판의 미결이 해소됨.

**6.2 Lens extension 필드 (직접 검증 — authoring standard §3·§3.1, metadata standard §13.2)**

| 필드 | 판정 | 근거 |
|---|---|---|
| category | **`자산군 확장` 확정** | enum 원문 직접 확인 (authoring standard §3, metadata standard §13.2): 공통 시장 / 주식 / 환율·금리·매크로 / **자산군 확장** / 투자 일지·행동 분석. overview §4.4의 카테고리명("자산군 확장 렌즈")과도 일치. handoff의 "자산군 확장 후보" 표기를 per-lens 문서에 이식하지 않는다는 직전판 MUST FIX **유지·확정** |
| lens_tier | **`new` — canonically supported** | canonical 값 existing-mvp / existing-extension / new 직접 확인. 신규 후보에 유일하게 가능한 값 |
| redirect_to | **처리 방식 해소** | §6.3 참조 |
| related_lenses | **정합 확정** | 필수 필드, formal 관계 미확정 시 빈 배열 `[]` 허용(누락은 error), 본문 언급으로 자동 생성 금지, canonical lens_key만 허용 — handoff 방침과 일치. **후보 간 참조(commodity ↔ etf-index-structure)는 양쪽 key가 canonical이 되기 전까지 넣을 수 없음** 재확인 |
| fixture_refs | **정합 확정** | 실존 fixture ID만, 신규 필요 시 "fixture 추가 필요(§13)" 표기 — 규칙 원문과 handoff 방침이 문자 단위 일치 |
| output_style | **정합** | "공통 5단 구조 내 사용 (templates §3)" 고정 표기 |
| activation_gate | **§6.4 참조** | — |
| trigger / forbidden_intents / lens_key | **정합** | trigger 예시는 안전 질문만·위험 질문은 forbidden_intents 배치, forbidden_intents는 "Input Guard 선행 선언" — candidate 계약들이 이 규칙을 명시 승계. lens_key는 §6.5의 충돌 이슈만 잔존 |

**6.3 redirect_to의 authoring 단계 처리 (해소 — 직접 도출)**

canonical 규칙: ① redirect_to는 lens key 관계가 아니라 **이 Lens가 redirect 결과로 연결되는 RISK_* intent 목록**이다. ② 각 값은 policyguard §3의 canonical intent여야 하고 **routing contract의 redirect 결과와 일치해야 한다** (authoring standard §3.1). ③ 필수 목록 필드가 비어 있으면 `[]`를 명시한다 (metadata standard §14.2). ④ §13.2 필수 필드의 활성화 시점 강제는 E-10(active lens)이다.

이로부터 후보 옵션별 판정:

| 옵션 | 판정 |
|---|---|
| candidate metadata에 제안 redirect_to 값 기재 | **불가** — 미등록 lens는 routing contract의 어떤 redirect 결과에도 아직 포함되지 않으므로, 비어 있지 않은 값은 ②의 일치 요건을 위반한다 |
| 빈 redirect_to (`[]` 명시) | **canonical 정합 — 채택** — ③이 필수 목록 필드의 명시적 빈 값을 허용하며, ②와 충돌하지 않는 유일한 기재 방식 |
| 필드 생략 | **금지** — lens extension 필드는 전부 필수이며 누락은 validation error (related_lenses 규칙과 동일 논리) |
| Routing 등록 전 authoring 차단 | **불요** — authoring standard §13이 "per-lens 문서를 먼저 쓰더라도 라우팅 포함 불가"로 선작성을 명시적으로 상정한다. 차단 지점은 활성화(E-10, E-11)다 |
| 기타 절차 | redirect 편입 제안은 metadata 필드가 아니라 **본문 서술과 design/handoff 문서**에 유지하고, Routing Contract 개정(§9-5단계)으로 편입이 확정된 뒤 redirect_to를 채운다 |

- 주석·placeholder 문법은 권고하지 않는다 — metadata standard는 이를 허용하는 규정이 없고, 알 수 없는 필드는 error(§14.2)다. 직전판 §6의 "빈 목록 + 제안 주석 vs 개정 후 기재 — owner 확인 필요"는 본 판정으로 **대체·정정**한다.

**6.4 activation_gate 재검증 (해소 — canonically supported)**

- 원문 (authoring standard §3.1): `lens-registration-v1`은 MVP 공통 gate ID이며, 그 의미는 "**§13 신규 렌즈 등록 전 체크리스트 완료 + `finance-routing-fixtures.md` 전체 regression 통과**"의 결합이다. "기존 Lens 10종은 migration 시 공통 gate ID를 사용할 수 있다"는 문장은 기존 10종에 대한 **허용 조항**이지 적용 범위를 migration으로 한정하는 제한 조항이 아니다.
- gate의 의미 자체가 **신규 렌즈 등록 절차**로 정의되어 있으므로, 신규 asset-class 후보에의 적용은 **canonically supported**로 분류한다. 직전판의 "owner confirmation required"를 **정정**한다.
- 잔여 사항 (결정 아님, 참고): ① 자산군 특유의 추가 검증이 필요해지면 "별도 gate ID 또는 gate registry 확장"의 canonical 경로를 사용한다 (신규 Runtime 상태 enum 금지 — candidate 문서들이 이미 준수). ② metadata standard §15 예시의 activation_gate 값은 prose 서술("routing-fixture regression 통과 …")이나, 작성 규칙의 canonical owner인 authoring standard §3.1은 "긴 prose 저장 금지, machine-readable gate ID 기재"를 요구한다 — 신규 문서는 gate ID(`lens-registration-v1`)를 기재하며, §15 예시와의 표기 정렬은 metadata standard 차기 개정 참고 사항이다 (§11 SHOULD IMPROVE).

**6.5 lens_key 충돌 (신규 발견)**

- overview §4(분류 체계, authoring standard보다 상위 서열)는 신규-후보 key를 이미 예약하고 있다: `asset-bond`, `asset-real-estate`, `asset-commodity`, `portfolio-structure` (§4.4), `fx-currency`, `interest-rate` (§4.3). overview §9.4 등록 게이트 1단계는 "overview §4 분류 체계에 배치"다.
- design pack의 key와의 관계: `bond-income-structure` ↔ `asset-bond` (도메인 중복 — 직접 충돌), `commodity-structure` ↔ `asset-commodity` (직접 충돌), `reit-structure` ↔ `asset-real-estate` (부분 — 상장 리츠 vs 실물 부동산으로 범위가 다르나 분담 정의 필요), `allocation-principles` ↔ `portfolio-structure` (도메인 인접 — 원칙 학습 vs 자가 점검, overview 쪽은 "비중 수치 제안·리밸런싱 지시 절대 금지"를 이미 명시), `etf-index-structure` (대응 예약 없음 — 충돌은 없으나 §4 분류 배치 신설 필요).
- 어느 key도 catalog에 등록되지 않았으므로 canonical 위반은 아니다. 그러나 등록 게이트가 overview §4 배치를 요구하므로, **key 채택(overview 예약 key 승계 vs overview 개정 제안 vs 분담 공존)은 등록 전 필수 결정**이며, key가 문서 서두 필드·파일명을 결정하는 bond/commodity는 **착수 전 확정을 권고**한다 (§7, §11).

---

## 7. ETF and Bond P1 readiness

**판정: 두 P1 후보 모두 authoring 착수 가능 — 아래 결정·보완의 완료를 조건으로.** (직전판 대비: envelope 대조·redirect_to·activation_gate·작성 형식 항목은 해소되어 제거, key 조정·depends_on 항목이 추가됨.)

**착수 전 반드시 완료할 것 (drafting 개시 조건)**

1. design approval — design proposal + handoff의 공식 승인.
2. ETF/Index 통합 범위·ETN 포함 여부 확정 (Lens owner) — trigger·Domain boundary 직접 의존.
3. Bond/Bond ETF 통합 확정 (Lens owner).
4. **lens_key 조정 (신규)**: `bond-income-structure` vs overview §4.4 예약 후보 `asset-bond` — 승계/개정 제안/분담 중 확정 (Lens owner + overview owner). etf는 충돌 없음 — overview §4 분류 배치만 등록 전까지 완료하면 됨.
5. **envelope 보완 (신규)**: per-lens 문서에 depends_on(document_id 기준 — lens-catalog·lens-authoring-standard·policyguard 예시 준용)과 document_id(`finance.lens.<key>` 형식) 부여 계획 반영. category는 enum 원문 `자산군 확장`, redirect_to는 `[]`, 작성 형식은 authoring standard §3 블록 — 본 리뷰로 확정된 방식 적용.

**착수와 병행 가능하되 등록 전 확정할 것**

6. fixture family 체계: overview §10의 자산군 계열(FX/BD/CM/PF…) 채택 vs 기존 4계열 흡수 (fixture owner) — handoff §8 권고와의 상충 해소 (§5-D).
7. redirect 편입과 macro-rate-fx 분담 (Routing owner) — 확정 시 redirect_to 채움 (§6.3).
8. "우열 단정 유도" 표현군의 intent 귀속 (policyguard owner) (§5-A).
9. overview §4 분류 배치 완료 (etf 포함 — 등록 게이트 1단계).

- 1~5 완료 전 drafting 개시는 불가. 6~9는 drafting을 막지 않으나 catalog 등록의 전제다 (authoring standard §13 — 하나라도 미완이면 미존재 취급).

---

## 8. Expansion readiness

**`reit-structure` — BLOCKED 유지, blocking 범위 확대**

- 기존 blocking("valuation 확장 vs 독립")에 더해, overview §4.4 `asset-real-estate`(부동산 일반 — 특정 지역/매물 판단 금지, RE-01 "지금 집 사도 돼?" 실물 케이스)와의 **범위 분담**이 분류 배치 단계에서 함께 확정되어야 한다: 상장 리츠(증권)와 실물 부동산은 도메인이 다르므로 공존 가능하나, 분담 정의 없는 등록은 trigger 충돌을 낳는다.
- earnings와의 분담(실적 일정·기대 vs 배당 원천 구분) 재확인 요건은 유지.

**`commodity-structure` — BLOCKED 유지, blocking 범위 확대**

- 기존 blocking(빈도 검증 AND etf-index-structure 완성)에 **`asset-commodity` key 조정**이 추가된다 (§6.5).
- vehicle 공통 블록(비용·유동성·괴리)의 etf-index-structure 소유 원칙과 참조 위임 방식은 canonical 충돌 없음 — 유지. CM 계열 후보 표기(overview §10)가 fixture 방향을 이미 예비하고 있어, family 결정(§7-6) 이후 확장 검토가 수월하다.

---

## 9. Defer and merge validation

**`allocation-principles` — DEFER 유지: 재검증됨, 해제 조건 1건 추가**

- 기존 defer 논리(거절 경로 fixture 선행, 형태 미결정, 안전 전제 미충족)는 직접 검증에서도 타당하다. 특히 overview §4.4가 `portfolio-structure`에 "**비중 수치 제안·리밸런싱 지시 절대 금지** — RISK_POSITION_SIZE / RISK_ACCOUNT_BASED_ADVICE 경계와 직결"을 명시하고, §6이 PF 계열 경계 fixture를 **필수**로 지정한 것은 candidate의 defer 사유(정책 근접성 최고, RF/PF 확장 선행)와 정확히 같은 판단이다.
- **해제 조건 추가**: 기존 3건(Lens vs Learn module 결정 / RF·PF 계열 fixture 등록·통과 / 서비스 정책 owner 경계 리뷰)에 **overview §4.4 `portfolio-structure`와의 관계 정리**(승계·통합·분담)를 추가한다. overview가 이미 이 도메인의 lens 후보를 예약했다는 사실은 "Lens 형태" 쪽 근거를 강화하지만, 결정 자체는 owner 소관이다.
- "초안 착수 포함 일체 금지"의 강도는 유지한다.

**`long-term-thesis-review` — NOT-A-LENS 유지: 재검증됨**

- merge 근거(journal-review 5구분으로의 환원, emotion-check 병행 커버, trigger 충돌)는 직접 검증에서 유지된다. overview §4.5의 신규-후보 `decision-process`(판단 근거 분류)·`behavior-pattern`(반복 패턴 관찰)은 thesis review와 **별개 도메인**이며 충돌하지 않고, BH-01/BH-02 후보(overview §10)가 "기록 기반 행동/추천 결합 차단" 경계를 이미 커버하므로 extension 제안의 안전 경계(RF-10, validator §12 journal review 검사 — "행동 결론으로 끝나면 실패")와 정합한다.
- 재검토 조건(기본값 제안 폐기, design proposal 개정 경유)은 변경 없음.

---

## 10. Canonical integration readiness

제안 순서: design approval → metadata confirmation → candidate authoring → catalog proposal → Routing/Fixture proposal → regression → Runtime activation review.

- **순서 대 게이트 (직접 검증으로 해소)**: overview §9.4는 등록 게이트 7항목을, authoring standard §13은 이를 작성자 체크리스트로 옮기며 "**per-lens 문서를 먼저 쓰더라도** 라우팅에 포함할 수 없다"고 명시한다. overview §11도 "순서가 아니라 게이트가 핵심"임을 명시한다. 따라서 handoff의 authoring(3단계) → catalog(4단계) 순서는 canonical과 **양립**한다 — 강제되는 것은 순서가 아니라 활성화 전 완전성(completeness)이다.
- **게이트 항목 대조 — handoff §9의 누락 3건**: overview §9.4/authoring standard §13의 게이트 중 ① overview §4 분류 배치, ② policyguard §7.1 패턴 보강 필요 여부 확인, ③ validator 금지 표현/검사 카테고리 갱신 필요 여부 확인이 handoff §9의 명시 단계에 없다. 게이트는 handoff 기재 여부와 무관하게 구속하므로 등록 전 충족은 필수이며, handoff 차기 개정 시 명시를 권고한다 (§11).
- metadata confirmation(2단계)의 실질: 직전판이 지목한 "envelope 원문 대조"는 본 리뷰로 **수행 완료**됐다. 잔여 실질은 depends_on/document_id 반영(§7-5)과 형식(§6.1)의 적용 확인으로 축소된다.
- 활성화 차단 지점의 canonical 대응: E-10(active lens 필수 필드), E-11(비active routable release 포함 금지), E-14(version constraint), lens-registration-v1(§13 체크리스트 + 전체 regression) — handoff의 "3단계 문서 존재해도 7단계 전 미로드" 서술과 전부 정합.

---

## 11. Required corrections

**MUST FIX before authoring (P1 drafting 개시 전)**

- [ ] per-lens metadata 제안에 **depends_on(document_id 기준)과 document_id 부여 계획을 보완** — lens는 depends_on 필수 (metadata standard §13.1), handoff 제안에 누락 (§6.1)
- [ ] category는 enum 원문 **`자산군 확장`** 사용 — handoff의 "자산군 확장 후보" 표기를 per-lens 문서에 이식하지 않음 (직접 검증으로 확정, §6.2)
- [ ] redirect_to는 Routing 편입 확정 전까지 **명시적 빈 목록 `[]`** — 제안값 기재 금지, 필드 생략 금지, 주석·placeholder 불사용 (§6.3)
- [ ] 작성 형식은 **authoring standard §3의 텍스트 metadata 블록** — YAML Front Matter 선적용은 migration 절차와 결합된 owner 결정 없이 하지 않음 (§6.1)
- [ ] `bond-income-structure`의 **lens_key 조정** (vs overview §4.4 `asset-bond`) — key가 파일명·서두 필드를 결정하므로 착수 전 확정 (§6.5)

*(직전판의 "envelope 원문 대조 수행" 항목은 본 리뷰로 이행 완료되어 목록에서 제거한다.)*

**MUST DECIDE before registration (catalog 등록 전)**

- [ ] lens_key 조정 잔여분과 overview §4 분류 배치: `commodity-structure`↔`asset-commodity`, `reit-structure`↔`asset-real-estate` 분담, `allocation-principles`↔`portfolio-structure` 관계, `etf-index-structure`의 §4 배치 신설 (Lens owner + overview owner) (§6.5)
- [ ] fixture family 체계: overview §10의 자산군 계열(FX/BD/RE/CM/PF/BH/EQ) 채택 vs 기존 SF/RF/DG/AM 흡수 — handoff §8 권고와의 **상충 해소** (fixture owner) (§5-D)
- [ ] 신규 lens의 redirect lens 조합 편입과 macro-rate-fx 분담 (Routing owner) — 확정 후 redirect_to 기재 (§5-C, §6.3)
- [ ] "우열 단정 유도" 표현군의 intent 귀속 (policyguard owner) (§5-A)
- [ ] 각 후보의 기존 blocking 결정: reit(valuation 확장 vs 독립), commodity(빈도 검증), allocation(Lens vs Learn module) (§4)

*(직전판의 "activation_gate 적용 가부" 항목은 canonically supported로 해소되어 제거한다 — §6.4.)*

**SHOULD IMPROVE**

- [ ] handoff §9에 등록 게이트 3항목(overview §4 분류 배치 / policyguard 패턴 확인 / validator 갱신 확인)을 명시 단계로 추가 (차기 개정) (§10)
- [ ] validator 검토 후보 표현군의 처리 방향 명시: **신규 검사 카테고리 신설 불요 추정** — 상품 우열·랭킹 → forbidden recommendation, 방향 전망 → performance guarantee/unsupported market data claim, 배당 안전성 판정 → unsupported market data claim, 비율 제시 → forbidden position sizing 등 기존 13종 카테고리의 표현 목록 확장으로 수용 가능. 확정은 validator 문서 개정 소관 (overview §9.3, validator §3) (§6)
- [ ] 리밸런싱 시점 질문의 ACCOUNT_BASED 승격 조건을 RF/PF 확장 설계 시 명문화 (§5-B)
- [ ] metadata standard §15 예시의 activation_gate prose 표기와 authoring standard §3.1의 gate ID 규칙 간 표기 정렬 (metadata standard owner 참고) (§6.4)

**NO CHANGE REQUIRED**

- §3 경계 점검 전 항목 (신규 intent/action 부재, PolicyGuard 비완화, fixture ID 미생성, 현재형 단정 부재, 추천·비중·목표가·타이밍 출력 부재, 등록/활성화 미완 표기)
- defer(allocation)·merge(thesis-review) 판정과 절차 (§9)
- P1 → P2 authoring 순서, P2 경계 분담 원칙, handoff의 단계-활성화 분리 서술 (§8, §10)
- related_lenses·fixture_refs·output_style·lens_tier·trigger/forbidden_intents 방침 — 전부 canonical 원문과 직접 합치 확인 (§6.2)

---

## 12. Final readiness decision

| candidate | verdict | authoring 개시 |
|---|---|---|
| `etf-index-structure` | **READY-WITH-DECISIONS** | **개시 가능 (조건부)** — §11 MUST FIX 중 해당 4건(depends_on/document_id, category, redirect_to `[]`, 작성 형식) + design approval + ETF/Index 범위·ETN 결정 완료 시 즉시 drafting 가능 |
| `bond-income-structure` | **READY-WITH-DECISIONS** | **개시 가능 (조건부)** — 위 공통 조건 + Bond/Bond ETF 통합 + **lens_key 조정(vs `asset-bond`)** 완료 시 drafting 가능. etf-index-structure 순차 착수 권장 유지 |
| `reit-structure` | **BLOCKED** | 불가 — valuation 확장 vs 독립 + `asset-real-estate` 범위 분담 해소까지 |
| `commodity-structure` | **BLOCKED** | 불가 — 빈도 검증 + P1-1 완성 + `asset-commodity` key 조정까지 |
| `allocation-principles` | **DEFER** | 불가 — 초안 착수 포함 금지 유지. 해제 조건 4건(AND): 형태 결정, RF/PF fixture 선행, 정책 owner 리뷰, `portfolio-structure` 관계 정리 |
| `long-term-thesis-review` | **NOT-A-LENS** | Lens authoring 대상 아님 — journal-review extension 제안으로 이관 유지 |

- MUST DECIDE 항목(§11)은 drafting을 막지 않으나 catalog 등록의 전제이며, 등록 게이트(authoring standard §13) 미완 렌즈는 미존재로 취급된다.
- 본 리뷰는 non-authoritative이며 각 owner의 승인을 대체하지 않는다. candidate authoring의 어떤 진행도 catalog 등록이나 Runtime activation을 의미하지 않는다.
