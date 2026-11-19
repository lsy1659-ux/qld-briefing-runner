# qld-briefing-runner

QLD 아침 브리핑을 **돌리기만 하는** 공개 저장소입니다.

- 여기에는 **실행 순서표(워크플로)** 만 있습니다. 전략 코드 · 시세 자료 · 기록은 비공개 저장소에 있고, 실행할 때마다 읽어 옵니다.
- 공개 저장소의 GitHub Actions 는 무료라서, 비공개 저장소의 월 사용량과 상관없이 매일 돕니다.
- 실행 기록(로그)은 누구나 볼 수 있으므로 **가격 · 신호 · 비중 · 계좌 숫자를 로그에 남기지 않습니다.** 결과는 텔레그램으로만 갑니다.
- 매매 주문을 하지 않습니다. 메리츠 연결은 **읽기 전용**이고, 주문 · 정정 · 취소 · 환전 주소는 코드가 부를 수 없게 막혀 있습니다.
- 계좌 조회는 아래 «내 계좌 주문 수량» · «계좌 기록» 두 가지뿐이며, 둘 다 **기본 꺼짐**입니다.

## 워크플로

| 파일 | 하는 일 | 언제 |
|---|---|---|
| `briefing.yml` | 공식 브리핑 — 판단 · 기록 · 텔레그램 (한 세션 한 번). 켜 두면 바로 뒤에 «내 계좌» 한 통 | 외부 정시 예약 (`scheduled=true`) · 예비로 GitHub 예약 |
| `shadow-meritz.yml` | 메리츠 종가로 «같은 답이 나왔을까» 기록 (공식 아님) | 마감 직후 여러 번 |
| `watchdog.yml` | 오후에 오늘 기록이 없으면 텔레그램 경고 | 한국 14:27 |
| `account-brief-test.yml` | «내 계좌» 메시지를 `[TEST]` 로 한 통 보내 보기 (켜기 전 확인용) | 직접 `Run workflow` 할 때만 |
| `account-ledger.yml` | (선택) 실제 계좌의 QLD · TQQQ · QQQ 보유 · 체결을 비공개 브랜치 `account-history` 에 쌓기 | **꺼짐** — 직접 `Run workflow` 하거나 변수 `QLD_ACCOUNT_LEDGER=on` 일 때만 한국 06:50 |
| `trade-approve.yml` | (선택) 승인형 자동 주문 — 계좌방에 승인 요청, «승인 코드» 로 답하면 MOC 로 냄 | **꺼짐** — 변수 `QLD_TRADE_MODE=dry\|approve` 일 때만. 외부 예약(뉴욕 평일 09:05~17:55 10분마다)은 켤 때 만듦 |

### 승인형 자동 주문 (선택 · 기본 꺼짐)

| 변수 | 값 | 뜻 |
|---|---|---|
| `QLD_TRADE_MODE` | `dry` / `approve` / 없음 | `dry` 시험(승인해도 주문 안 냄) · `approve` 실제 주문 · 없으면 꺼짐 |
| `QLD_TRADE_MAX_USD` | 예: `500` | 하루 주문 총액 상한. `approve` 에는 꼭 필요. 넘으면 하나도 안 냄 |
| `QLD_TRADE_PILOT` | `on` / 없음 | 종목마다 1주만 (첫 실전 시험용) |

메리츠에는 모의투자 환경이 없습니다 — `approve` 를 켜면 실제 주문이 나갑니다. 켜는 순서는 비공개 저장소 `docs/AUTO_TRADE.md`.

### 내 계좌 주문 수량 (선택 · 기본 꺼짐)

공식 알림 바로 뒤에, 메리츠 계좌의 보유 · 예수금으로 «오늘 몇 주 사고팔지» 를 계산한 한 통이 더 옵니다.
계산기 · 거래일 규칙은 기존 것 그대로입니다(조정하는 날에만 주문 줄). 주문은 직접 넣습니다.

`Settings` → `Secrets and variables` → `Actions` → **`Variables`** 탭에서:

| 변수 | 값 | 뜻 |
|---|---|---|
| `QLD_ACCOUNT_MODE` | `on` / `trade` / 없음 | `on` 매 세션 · `trade` 조정하는 날만 · 없으면 꺼짐 |
| `QLD_ACCOUNT_BUDGET_USD` | 예: `20000` / 없음 | 없으면 계좌 전체가 전략 몫. 숫자면 전략 몫 상한(달러) |
| `QLD_ACCOUNT_INCLUDE_KRW` | `on` / 없음 | `on` 이면 원화 예수금도 환율로 셈. 없으면 달러만 |
| `QLD_ACCOUNT_FEE_BPS` | 예: `7` / 없음 | 주문 수수료(0.01% 단위). 없으면 5 |

메리츠 API 키는 신청할 때 등록한 계좌 하나에 묶여 있어, 그 계좌의 것을 전부 봅니다. 전략 전용 계좌로 바꾸려면
그 계좌로 API 를 신청해 `MERITZ_APP_KEY` · `MERITZ_APP_SECRET` 만 바꿔 넣으면 됩니다.

### 계좌 기록 켜기 · 끄기

- 한 번만: `Actions` → `QLD Account Ledger` → `Run workflow`
- 매일 자동: **`Variables`** 탭 → 이름 `QLD_ACCOUNT_LEDGER`, 값 `on`
- 끄기: 그 변수를 지우거나 값을 `off` 로. 쌓인 기록은 그대로 남습니다.

## 비밀 (Settings → Secrets and variables → Actions)

| 이름 | 무엇 |
|---|---|
| `QLD_REPO_TOKEN` | 비공개 저장소 하나만 읽고 기록 브랜치에 덧붙일 수 있는 토큰 (fine-grained · Contents 읽기/쓰기) |
| `QLD_TELEGRAM_TOKEN` · `QLD_TELEGRAM_CHAT_ID` | 텔레그램 봇 (기존 값과 같음) |
| `MERITZ_APP_KEY` · `MERITZ_APP_SECRET` | 메리츠증권 Open API (그림자 검증 · 선택 기능의 계좌 조회) |
| `QLD_ACCOUNT_TELEGRAM_TOKEN` · `QLD_ACCOUNT_TELEGRAM_CHAT_ID` | (선택) 계좌방 — 계좌 전용 두 번째 봇. 둘 다 있으면 «내 계좌» 메시지는 이 방으로만, 없으면 기존 알림방으로 |

비밀은 GitHub 가 암호화해 보관하고 로그 · 복제본(fork) · 수정 요청(pull request) 어디에도 넘어가지 않습니다.
이 저장소는 `pull_request` 로 실행되지 않으며, 쓰는 외부 액션은 GitHub 공식 `checkout` · `setup-python` 둘을 커밋으로 고정한 것뿐입니다.
