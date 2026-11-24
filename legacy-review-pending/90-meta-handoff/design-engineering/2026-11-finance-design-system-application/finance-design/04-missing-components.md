# 04 누락 컴포넌트·패턴 명세와 우선순위

기준: PR #49(0409274) 컴포넌트 9종(Button 7 variant, Badge, Card(+inset), Checkbox, SelectChip, SectionHeader, InfoRow/InfoList, ErrorState, Dialog) + `origin/dev`의 `src/components/common/*`. 화면 ID는 01·03 문서. 렌더·테스트는 모두 NOT_RUN.

## 1. 보유 현황 요약

| 요청된 이름 | 상태 [확인] | 판정 |
|---|---|---|
| PageHeader | dev에 있음(`common/PageHeader.tsx`): h-14, ghost 아이콘 "←", 16px 제목, **어떤 화면에서도 안 씀** | 존재하나 Foundation 미정렬 → **재정렬(P0)** |
| EmptyState | dev에 있음: 아이콘/제목/설명/action, `text-sm` | **재정렬(P0)**: 타입 역할·action 규칙 |
| PolicyNotice | dev에 있음: `border + bg-muted/50`, `text-sm`, tone neutral/destructive | **재정렬(P0)**: 테두리 제거, inset, 상태 tone 분리 |
| TextField / Textarea | **없음**(화면마다 클래스 복붙, `bg-background`) | **신규(P0)** |
| FieldMessage(helper/error) | 없음(`JournalFormField`가 도메인 안에서 대행) | **신규(P0)**, `JournalFormField` 흡수 |
| Notice(상태 inset) | 없음(PolicyNotice destructive가 대행) | **신규(P0)** |
| StateView(loading/auth) | 없음(문장/박스 제각각) | 신규(P1) |
| StickyActionBar | 없음 | 신규(P1) |
| ListRow | 없음(`JournalRecordCard`가 카드형으로 대행) | 신규(P1) |
| Disclosure | 없음(`<details>` 직접) | 신규(P1) |
| ChoiceGroup(Radio) | `JournalChoiceGroup`(도메인 위치, 알약 radio) | 정렬·승격(P1) |
| Banner(오프라인·세션) | 없음 | 신규(P2, **제안**) |
| RecordTagBadge | dev: 메타를 Badge로 | **교체**: `RecordTagText`(텍스트) (P1) |
| Skeleton | `PageSkeleton`(레이아웃용) | P2 |
| AppHeader | AppShell 내부 함수 | P2(정렬만) |

## 2. 우선순위 기준

P0 = Pilot A(H1)와 모든 후속 화면의 선행(없으면 화면 PR이 복붙 확대). P1 = 둘째 화면 군(H2·J1~J3)에 필요. P2 = 후순위 또는 계약 없음.
하지 않을 것: Toast, Tooltip, Date picker, Table, 가격/차트 컴포넌트, 종목 카드(보류 #47·#48). 계약에 없고 03에서 필요하지 않다.

## 3. 명세

### 3.1 TextField / Textarea / FieldMessage — **P0 신규** (해당: H1·J2a·J2b·J4·A1·J3 수정)

| 항목 | 명세 |
|---|---|
| 목적 | 입력 필드 회색 문제(X5) 해소 + 라벨·도움말·오류·카운터를 한 곳에서 보장 |
| 구성 | `<TextField label hint error optionalLabel … />`: `<label for>` → 입력 → `FieldMessage`(hint 항상, error 있으면 error 우선 추가) |
| props 초안 | `id?`(없으면 `useId`), `label: ReactNode`, `optionalLabel?`, `hint?`, `error?`, `counter?: {current, max}`, `multiline?`(textarea, `rows`/`minRows`), 나머지 native 속성 pass-through. 도메인 key·i18n을 모른다 |
| 시각 | fill = `card`, 외곽선 1px `--input`, 반경 control(10), 높이 48(textarea min 120, reading 입력은 min 160), 글자 body 15/24(reading 입력은 16/27), placeholder = tertiary. 포커스 = 전역 2px brand + offset 2. 오류 = 외곽선 danger + 아이콘+텍스트(색만 금지). 비활성 = `text-disabled`+control 면 |
| 접근성 | native label, `aria-invalid`, `aria-describedby=hint+error`, 오류는 `role=alert`, 필수는 `required`+텍스트(별표만 금지), 터치 44+ |
| 금지 | `bg-background`·`bg-muted`로 fill, 개별 `ring` 재정의 |
| 테스트 | label↔input 연결, describedby 조합, 오류 시 aria-invalid, 다크 토큰 해석(contrast.test 확장), 키보드 |
| 대체하는 것 | `JournalFormField`·인라인 textarea 클래스(H1, A1, J4 ×4, DecisionContext ×2, SavedRetrospectives ×3) |

### 3.2 Notice — **P0 신규**

| 항목 | 명세 |
|---|---|
| 목적 | 상태 안내(info/success/warning/danger/conflict)와 정책 안내(neutral)를 같은 inset 문법으로. 테두리·아이콘 박스 장식 없음 |
| props | `tone: 'neutral'|'info'|'success'|'warning'|'danger'|'conflict'`, `title?`, `children`, `action?`, `icon?`(기본 tone별 lucide) |
| 시각 | `inset` 면(neutral) 또는 `*-bg` 면(상태), 글자 body/supporting, 반경 inset 12, 테두리 없음. 아이콘+제목으로 상태 전달 |
| 접근성 | neutral·info·success = `role="status"`(또는 `note`), danger·warning(오류성) = `role="alert"`. 정적 정책 고지는 `role="note"` |
| 관계 | `PolicyNotice`는 `Notice tone=neutral` 래퍼로 축소(기존 사용처 호환) |
| 규칙 | 같은 안내 문장을 섹션에서 반복 금지(P7) |

### 3.3 PolicyNotice / EmptyState / PageHeader — **P0 재정렬**

| 컴포넌트 | 변경 |
|---|---|
| PolicyNotice | `border`·`bg-muted/50` 제거 → `Notice tone=neutral`. `destructive` tone 제거 → `Notice danger`로 이관(사용처 확인 필요, grep 후 PR에서 처리) |
| EmptyState | 제목 `type-content-title`, 설명 `type-supporting text-secondary`, action 슬롯 그대로. 아이콘 선택 사항(원칙: 없음). **action은 호출부가 Button variant를 정함**(내부에서 strong 강제 금지) |
| PageHeader | 뒤로 = Button `text-neutral` icon(44, ←는 lucide `ArrowLeft`+`aria-label`), 제목 = **`h1` page-title(24/32)** 또는 앱바형 옵션(`variant='bar'`: 16 semibold 중앙, 현행 화면의 앱바 대체). trailing 슬롯. `onBack` 없으면 뒤로 미렌더(현행 유지). 화면은 h1을 정확히 1개 유지(H2는 헤더가 h1) |

### 3.4 ListRow — **P1 신규** (해당: H1 예시 질문·최근 기록, J1 목록, J3 질문 목록)

- 목적: 카드형 `JournalRecordCard`(테두리·막대·이중 배지) 대신 `행 + divider`.
- 구성: 선행(선택 표시 영역) · 본문(title content-title + meta 13/18 + 선택적 supporting 1줄) · 후행(chevron). 높이 ≥ 56(2줄 72). 전체가 `<a>`/`<button>` 하나(내부 중첩 상호작용 금지).
- 상태: default / hover(control 면) / selected(brand subtle 면 + `aria-current="page"`) / pressed / disabled.
- 접근성: 이름 = 제목+메타, 터치 영역 전체, 선택은 색만 아님(굵기·`aria-current`).
- 규칙: 배지 금지(상태 텍스트가 필요하면 Status 라벨 1개).

### 3.5 StickyActionBar — **P1 신규** (해당: H2, J2a/b, J4)

- 목적: 폰에서 화면의 단일 Primary를 고정(Foundation C 규칙). 데스크톱에서는 해제.
- 구성: `page` 면 + 상단 hairline, 내부 Button 1~2개, `padding-bottom: max(12px, env(safe-area-inset-bottom))`.
- 규칙: strong 1개만, 보조는 neutral/text. 키보드가 열리면 sticky 해제하거나 `visualViewport` 고려 [NOT_RUN].
- 접근성: WCAG 2.4.11 — 콘텐츠 컨테이너 `scroll-padding-bottom ≥ 바 높이`, DOM에서 본문 뒤에 위치(포커스 순서), `role` 없음(landmark 남용 금지).
- 대체: 현행 본문 끝 큰 버튼, 하단 CTA 카드(H2).

### 3.6 StateView — **P1 신규** (해당: 모든 목록·상세·결과)

- variants: `loading`(skeleton 또는 상태 문장), `auth`(비인증: 이유·보존되는 것·계속 버튼), `notFound`(EmptyState 래핑). `error`는 기존 `ErrorState`.
- loading: `role=status`, 자리 유지(레이아웃 점프 최소), 3초 초과 시 보조 문구(타이머는 호출부).
- auth: 제목(왜 로그인이 필요한가) + 보존 문장 + strong 1개(계속). UX-Δ §6 문구 사용.

### 3.7 Disclosure — **P1 신규** (해당: J2a Decision Context 접기, J4 선택 항목)

- native `<details>/<summary>` 래퍼. summary 최소 높이 48, 화살표 아이콘(회전) + 텍스트(상태는 `open`로 전달), 테두리 없음, 상하 divider만. 내용은 일반 흐름.
- 포커스: summary에 전역 포커스 스타일. 접힘 기본값은 호출부.

### 3.8 ChoiceGroup(Radio) — **P1 정렬·승격** (해당: J2a 행동·감정, 필터)

- 현 `JournalChoiceGroup`(sr-only radio + 알약) → `features/…`에서 `ui/`로 승격하고 스타일을 SelectChip과 동일 문법(켜짐 = brand subtle + 체크, 반경 full, 44)으로.
- **단일 선택 = radio(`fieldset/legend`)**, 다중/토글 = SelectChip(`aria-pressed`). 행동 4개는 색 차등 금지(GUARD).
- 감정 5종은 `EMOTION_TAGS` 상수만 사용(변경 금지).

### 3.9 RecordTagText — **P1 교체** (`RecordTagBadge` 대체, 해당: J1·J3·J4)

- 유형·행동·감정을 **텍스트**로 표기(InfoRow 값 또는 meta 문장 조합). tone 색 없음(FOMO·혼란 danger 제거).
- 기존 i18n key(`recordTags.*`)·union 컴파일 보장(switch)은 유지. 파일 이름만 바뀌는 리팩터가 아니라 **시각 계약 변경**이므로 단독 PR + 접근성 이름 유지 확인.

### 3.10 Banner — **P2 제안(계약 없음)** (오프라인·세션)

- 상단 inset 한 줄(`role=status`). 오프라인 시 "연결이 끊겼어요" + 영향 + 자동 재시도 여부. `navigator.onLine`만으로 단정하지 않는다(거짓 양성). **계약 승인 전에는 문서만, 구현 보류.**
- 기존 비운영 참조 세션 배너(S3)도 같은 컴포넌트로 흡수 가능하나 필수 아님.

### 3.11 기타

| 항목 | 우선순위 | 비고 |
|---|---|---|
| Switch | P2 | Decision Context 켜기: 새 컴포넌트 대신 `Checkbox`(native)로 표현 가능한지 Owner 확인[Q2]. UX-Δ §7.2 "Context toggle native semantics" = native input |
| AppHeader | P2 | 앱 이름·로그인 링크, 토큰 정렬만 |
| Skeleton | P2 | StateView에 포함 가능 |
| Dialog 이탈 보호 | 후순위 | `useBlocker` 필요(라우팅 변경) → 디자인 PR과 분리 |

## 4. 구현 순서 의존성

```text
#49(토큰·ui 9종) 병합
 → C1: TextField/FieldMessage + Notice + PolicyNotice·EmptyState·PageHeader 재정렬   (P0)
 → Pilot A: H1 검토 시작
 → C2: ListRow + StickyActionBar + StateView + Disclosure + RecordTagText            (P1, 각각 사용 화면 PR과 묶어도 됨)
 → 화면 PR들(05 문서)
```

공통 컴포넌트는 **첫 사용 화면 PR과 같이** 넣는 것을 기본으로 하되, TextField·Notice는 사용처가 5개 화면이라 선행 PR로 분리한다(리뷰 단위 크기·재사용 위험 때문). 트레이드오프: 분리하면 PR이 하나 늘고 "사용처 없는 컴포넌트" 기간이 생기지만(#49와 같은 상태), 화면 PR 리뷰가 시각 변경에만 집중된다.
