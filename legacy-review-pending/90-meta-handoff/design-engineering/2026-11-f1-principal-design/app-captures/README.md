# 실제 앱 캡처 (기존 화면 검수 증거)

- 종류: **실제 앱**. 고정 HEAD를 이 세션에서 직접 빌드해 Playwright(Chromium, deviceScaleFactor 1, ko-KR, Asia/Seoul)로 캡처했다. 시안이 아니다.
- 데이터: fixture·합성 데이터만. 고객 이름(박세입, 최내원)과 종목 이름(반도체 기업 A 등)은 각 저장소 fixture의 합성 값이다. 실데이터·개인정보 없음.
- 원본 캡처는 제품당 171장(9경로 × 17폭 Light + 2폭 Dark)이고, 여기에는 직접 열어 본 모음 시트와 경계 캡처, 측정값만 넣었다.

| 제품 | HEAD | 빌드·구동 | 시계 |
|---|---|---|---|
| Finance | `finance-harness-fe@fda50a620658b57598cf8c689123aa4e796d94eb` (dev) | `pnpm build` → `node e2e/support/financeE2eServer.mjs` (fixture API + preview) | 고정 안 함(fixture 날짜 고정값) |
| Carelog | `carelog-fe@963f1737b62d83319a3b1e556f4916c950d62556` (dev) | `pnpm build:e2e` → `vite preview --outDir dist-e2e` (인메모리 fixture, 인증 상태 주입) | 2026-08-11 12:00 KST 고정 |

## 폭

320×640, 375×812, 390×844, 393×852, 767×1024, 768×1024, 1023×768, 1024×768, 1024×1366, 1099×800, 1100×800, 1179×800, 1180×800, 1279×1366, 1280×1366, 1280×800, 1440×900 (Light). Dark는 390×844, 1440×900(`.dark` 클래스 강제. 두 제품 모두 사용자에게 Dark를 노출하지 않는다).

## 파일

모음 시트 한 장의 구성: 윗줄 왼쪽부터 390×844(세로), 768×1024(세로), 1024×768(가로). 아랫줄 1440×900(가로). 모두 Light.

| 파일 | 제품 | 화면 | 경로 |
|---|---|---|---|
| `finance/start.png` | Finance | 검토 시작 | `/app` |
| `finance/result.png`, `finance/result-390-tall.png`(390 전체 높이) | Finance | 검토 결과 | `/app/ask?ref=…` (검토 시작에서 질문을 입력해 진입) |
| `finance/journal-list.png` | Finance | 저널 목록 | `/app/journal` |
| `finance/journal-detail.png` | Finance | 저널 상세 | `/app/journal/550e8400-…` |
| `finance/journal-review.png` | Finance | 복기 | `/app/journal/550e8400-…/review` |
| `finance/journal-new.png` | Finance | 새 기록 선택 | `/app/journal/new` |
| `finance/auth.png` | Finance | 인증 진입 | `/auth` |
| `finance/onboarding.png` | Finance | 온보딩 | `/app/onboarding` (캡처만 하고 본문 검수는 하지 않음) |
| `finance/new-auth-detail-390-tall.png` | Finance | 새 기록·인증·저널 상세 390 | 위와 같음 |
| `finance/journal-detail-1279x1366.png`, `-1280x1366.png` | Finance | 저널 상세, 세로 경계 | F1-01 F-1의 증거 |
| `carelog/schedule.png`, `carelog/schedule-eventnew-390-tall.png` | Carelog | 일정, 일정 추가(고객 선택 단계) | `/app/schedule`, `/app/events/new` |
| `carelog/customers.png` | Carelog | 고객 목록 | `/app/customers` |
| `carelog/customer-detail.png` | Carelog | 고객 상세 | `/app/customers/customer-tenant-1` |
| `carelog/customer-new.png` | Carelog | 고객 추가 | `/app/customers/new` (캡처만) |
| `carelog/event-detail.png` | Carelog | 일정 상세(기록됨) | `/app/events/event-tenant-transitioned` |
| `carelog/event-new.png` | Carelog | 일정 추가 | `/app/events/new` |
| `carelog/settings.png` | Carelog | 계정 | `/app/settings` (캡처만) |
| `carelog/customer-detail-320x640.png` | Carelog | 고객 상세 320×640 | 하단 내비 101px와 본문 높이(D-7의 증거) |
| `carelog/customer-detail-1179x800.png`, `-1180x800.png` | Carelog | 고객 상세, 가로 경계 | 1단 → 2단 전환 증거 |
| `dark-390-finance-carelog.png` | 둘 다 | Finance 결과·저널 상세, Carelog 일정·고객 상세 390 Dark | F1-01 C-1의 증거 |
| `finance/metrics.json`, `carelog/metrics.json` | — | 캡처별 측정값(내비 위치·크기, 넘침, 44·24 미만 대상, 12px 미만 글자, 진한 버튼, major surface 수) | 키 = `<화면>-<폭>x<높이>[-dark]` |

## 보지 못한 화면 (NOT_VERIFIED)

Finance 판단 기록·학습 노트 작성, 실제 복기 입력 폼, 인증 성공 뒤 복귀. Carelog 인증 화면, Assistant(`/app/assistant`는 직접 진입 시 404), 정리 필요·예정·취소 상태의 일정 상세, 일정 추가 폼 단계.
