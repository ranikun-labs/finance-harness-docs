# finance-asset-class-p2-owner-decision-brief.md

## 1. Document status

```text
document type         : P2 Owner Decision Brief (Commodity Lens 착수 판정)
authority             : non-authoritative
lifecycle             : review draft
runtime_loadable      : false
routable              : false
implementation_status : review-only (metadata standard 기준 report류 —
                        migration 시 contract-only + non_authoritative로 정규화)
distribution_boundary : repo-only
```

- **판정 대상 단일 질문**: Commodity Lens 후보의 **candidate authoring을 지금 시작해도 되는가.** 그 외(등록, Routing 편입, 활성화, 다른 P2 후보)는 본 brief의 범위가 아니다.
- **근거 문서**: pack design §5.D·§9, authoring handoff §2·§3·§4.4·§6, readiness review 개정판 §4·§6.5·§8, 완성된 후보 초안 `finance-lens-etf-index-structure.md`, canonical overview / authoring standard / metadata standard / policyguard / fixtures / validator / grounding.
- 본 brief는 canonical 문서를 수정하지 않고, 실제 Lens를 작성하지 않으며, fixture ID·expected behavior를 만들지 않고, 수요 검증을 근거 없이 주장하지 않는다. 권고는 owner 승인을 대체하지 않는다.

---

## 2. 판정 요약

**권고: GO-WITH-CONDITIONS — 아래 §8의 조건 5건 충족을 전제로 candidate authoring 착수 가능.**

| 쟁점 | 판정 |
|---|---|
| 1. key 충돌 (commodity-structure vs asset-commodity) | **asset-commodity 채택** — overview 예약 key 승계로 충돌 소멸 (§3) |
| 2. ETF 완성 선행 조건 | **실질 충족, 형식 조건부** — vehicle 블록 경계는 확립됐으나 ETF 문서가 미리뷰 draft이므로 결합 방식 조건 필요 (§4) |
| 3. 질문 빈도 실증 | **부재** — 어떤 제출 문서에도 수요 근거 없음. 본 brief는 수요를 검증된 것으로 취급하지 않는다 (§5) |
| 4. Product owner 우선순위에 의한 착수 승인 가능 여부 | **가능 — 단 명시 조건부** (안전 게이트가 아니라 제품 우선순위 조건이므로 owner 결정으로 대체 가능, 결정의 서면화 필수) (§6) |
| 5. 경계 확정 | §7 경계표로 확정 제안 — commodity 문서 경계 섹션에 이식 |

---

## 3. 쟁점 1 — lens_key 충돌 해소

- 충돌 구조: pack design은 `commodity-structure`를 제안했으나, canonical `finance-analysis-lens-pack-overview.md` §4.4는 자산군 확장 범주에 `asset-commodity`를 신규-후보 key로 이미 예약하고 있다 (readiness review §6.5에서 직접 검증). 등록 게이트는 overview §4 분류 배치를 요구하므로, 서로 다른 key로 같은 도메인을 지칭하는 상태로는 등록이 불가능하다.
- 해소 옵션: ① overview 예약 key `asset-commodity` 승계, ② overview 개정으로 `commodity-structure` 등재, ③ 분담 공존.
- **권고: ① 승계.** 근거: (a) overview는 문서 서열상 authoring standard보다 상위이며 분류 체계의 소유자다. (b) ③은 동일 도메인(선물 구조·롤비용)을 두 key가 나눌 실체가 없다 — pack design §5.D의 도메인 정의와 overview §4.4의 `asset-commodity` 정의는 같은 대상을 가리킨다. (c) ②는 개정 비용만 있고 이득이 없다. (d) 채권 후보에서 이미 동일 유형의 충돌(`bond-income-structure` vs `asset-bond`)이 "overview 예약 key 우선 검토" 방향으로 정리 중인 선례와 일관된다.
- **채택 시 확정값 (GO 조건 C1)**: `lens_key: asset-commodity`, 파일명 `finance-lens-asset-commodity.md`. pack design·handoff의 `commodity-structure` 표기는 개정하지 않고(본 brief는 두 문서를 수정하지 않는다), 후속 per-lens 문서와 등록 절차에서 `asset-commodity`를 사용하며 key 이력은 per-lens 문서 서두에 주석으로 남긴다.

---

## 4. 쟁점 2 — "ETF 완성" 선행 조건의 충족 여부

- 조건의 원 취지 (handoff §2·§6, review §8): vehicle 공통 항목(비용·유동성·괴리·설정환매)의 **소유가 ETF 렌즈로 확정된 뒤**에야 commodity 문서가 해당 항목을 재서술 없이 참조 위임으로 처리할 수 있다 — 목적은 시간 경과가 아니라 **경계 품질**이다.
- 현재 상태: `finance-lens-etf-index-structure.md` v0.1이 작성 완료됐고, vehicle 공통 항목의 소유가 문서로 고정됐다 — §1 구분 2~4(복제/비용·추적/거래 구조)와 §4 블록 3~6(복제·비용·추적괴리·거래 구조)이 그것이다. commodity가 위임할 대상이 이제 실재한다.
- 잔여 위험: ETF 문서는 **lifecycle draft·미리뷰 상태**다. 리뷰에서 블록 구획이 바뀌면 commodity의 참조가 흔들린다.
- **판정: 실질 충족 — 조건부.** 착수를 막을 이유는 소멸했으나, (a) commodity 문서는 ETF 문서를 **섹션 번호가 아니라 구분/블록의 "이름"으로** 참조해 결합도를 낮추고, (b) ETF draft의 vehicle 블록 구획에 대한 리뷰 동결(또는 최소한 셀프 체크 통과 확인)이 commodity draft **확정 전**에 이루어져야 한다 (조건 C3). 참조는 본문 산문으로만 하며 `related_lenses` 값으로 만들지 않는다 — 양쪽 key 모두 미등록이므로 canonical 규칙상 불가.

---

## 5. 쟁점 3 — 질문 빈도 실증 존재 여부

- **판정: 존재하지 않는다.** pack design(§9 "질문 빈도가 미검증"), handoff(§3 선행 조건 "질문 빈도 검증"), readiness review(§4·§8 BLOCKED 사유)와 이번에 제출된 어떤 문서에도 원자재 질문의 빈도·수요를 보여주는 데이터(질의 로그, intent 분포, 사용자 조사)가 없다.
- 본 brief는 수요가 검증됐다고 주장하지 않으며, 이후 어떤 문서도 본 brief를 "수요 검증 완료"의 근거로 인용할 수 없다. 이 부재를 어떻게 처리할지가 쟁점 4다.

---

## 6. 쟁점 4 — 우선순위 결정으로 착수를 승인할 수 있는가

- 성격 분석: "질문 빈도 검증" 조건은 canonical 안전 게이트가 아니다. canonical 게이트(authoring standard §13, `lens-registration-v1`, fixture regression, PolicyGuard/Validator 불변)는 수요 근거를 요구하지 않는다. 빈도 검증은 **non-authoritative design 문서가 스스로 부과한 제품 우선순위 조건**으로, 그 목적은 안전이 아니라 **작성 노력의 낭비 방지**다.
- 따라서: **Product owner는 명시적 우선순위 결정으로 이 조건을 대체할 수 있다.** 후순위 tier 후보를 수요 미검증 상태로 착수하는 것은 안전 구조에 어떤 변화도 만들지 않는다 — 위험 intent 처리·grounding·validator 게이트는 렌즈가 몇 개든 동일하게 적용되고, 등록·활성화 게이트도 그대로다.
- 단, 대체가 성립하려면: (a) 결정이 **"수요 근거 부재를 인지한 우선순위 판단"으로 서면 기록**되어야 하고(부재가 결정으로 소급 치유되지 않는다), (b) 감수하는 비용이 명시되어야 한다 — 하방 위험은 안전이 아니라 **작성·리뷰·fixture 확장 노력이 저수요 도메인에 선투입되는 것**이다. (c) 이 승인은 착수까지만이며, 등록·활성화의 어떤 게이트도 면제하지 않는다. (조건 C2)

---

## 7. 쟁점 5 — 경계 확정 (commodity 문서 경계 섹션 이식용)

| 영역 | asset-commodity가 소유 | 위임 대상 (기존/후보 렌즈) | 경계 문형 |
|---|---|---|---|
| 원자재 **현물 시장 구조** | 현물-선물-상품의 3층 구분 개념, 현물 가격 형성에 보관·운송·품질 규격이 개입한다는 구조의 확인 항목, 재고/생산/수요라는 **확인 대상의 존재와 출처 유형** | 수급 사이클의 해석 관점은 `sector-cycle` | "재고·생산·수요를 어디서 확인할지는 본 렌즈, 그것을 업황 사이클로 읽는 관점은 sector-cycle 유지" |
| 원자재 **선물 구조** | 만기 구조, 콘탱고·백워데이션 개념, 롤오버·롤비용 경로, 내 상품이 담는 선물 만기의 확인 방법 — **본 렌즈의 핵심 고유 도메인** | (없음 — 단독 소유) | "현재 곡선이 어느 상태인지는 어떤 경로에서도 단정하지 않는다 (RISK_DATA_GROUNDING)" |
| **ETF vehicle 구조** | 상품-선물 연결 항목만 (이 상품이 무엇을 담는가) | 보수·유동성·괴리·설정환매·추적 전부 `etf-index-structure` (후보 간 산문 참조) | "vehicle 공통 확인 항목은 ETF 구조 렌즈의 블록을 따르며 본 렌즈가 재서술하지 않는다" |
| `sector-cycle` | — | 업황·사이클 국면의 확인 관점 전부 | "다른 산업에도 통하는 사이클 문장이면 sector-cycle 소유" |
| `supply-demand` | — (용어 충돌 방지: 본 렌즈는 "수급" 대신 재고/생산/수요·물량 용어 사용) | 투자자별 매매 동향의 확인 관점 | "투자자 수급은 supply-demand, 물량 수급은 본 렌즈" |
| `macro-rate-fx` | 표시 통화·환헤지가 상품 수익률에 개입하는 **구조 경로**만 | 환율·금리 **환경**의 확인 관점 | "환경 vs 구조" 공식 (02 pack 공통) |
| `event-calendar` | 이벤트(지정학·기상·만기)가 선물 구조에 작동하는 **경로의 개념**만 | 이벤트 **일정·사실**의 확인 | "일정 확인은 event-calendar, 구조 경로는 본 렌즈" |

- 이 표의 모든 관계는 산문 경계이며 formal `related_lenses` metadata로 전환하지 않는다 (조건 C4).

---

## 8. 최종 권고와 조건

**GO-WITH-CONDITIONS.** 아래 5건이 충족되면 `asset-commodity` candidate authoring을 시작할 수 있다.

| # | 조건 | owner | 시점 |
|---|---|---|---|
| C1 | lens_key **`asset-commodity`**, 파일명 **`finance-lens-asset-commodity.md`** 채택 (overview 예약 key 승계 — §3) | Lens owner + overview owner | 착수 전 |
| C2 | 수요 근거 부재를 명시한 **Product owner의 우선순위 결정 서면화** (§6 — 안전 게이트 면제 아님을 결정문에 포함) | Product owner | 착수 전 |
| C3 | ETF draft의 vehicle 블록 구획 **리뷰 동결 확인**, commodity 문서는 블록 "이름" 기준 산문 참조만 (§4) | Lens owner | commodity draft 확정 전 |
| C4 | §7 경계표를 commodity 문서의 경계 섹션에 이식, "수급" 용어 대체 규칙 포함 | 작성자 | authoring 중 |
| C5 | metadata는 ETF 후보와 동일 방식: envelope 전 필드 + `document_id: finance.lens.asset-commodity`, depends_on(document_id 기준), `redirect_to: []`, `related_lenses: []`, `fixture_refs: []`(placeholder 없음), `activation_gate: lens-registration-v1`, contract-only / draft / 비routable | 작성자 | authoring 중 |

- **NO-GO가 아닌 이유**: 착수를 막던 세 조건 중 key 충돌은 해소 경로가 확정 가능하고(§3), ETF 선행은 실질 충족됐으며(§4), 수요 부재는 안전 문제가 아니라 우선순위 문제로 owner 결정으로 대체 가능하다(§6).
- **무조건 GO가 아닌 이유**: C2가 없으면 "수요 검증" 조건이 근거 없이 소멸된 것으로 기록이 남고, C3가 없으면 미리뷰 draft 간 결합으로 재작업 위험이 남는다.
- 착수 승인의 효력 범위: **candidate 문서 작성까지.** Catalog 등록, overview §4 배치 완료, fixture 등록, regression, Routing 편입, Runtime 활성화는 각각의 canonical 게이트를 별도로 통과해야 하며 본 brief가 어느 것도 선취하지 않는다.
