# qld-briefing-runner

QLD 아침 브리핑을 **돌리기만 하는** 공개 저장소입니다.

- 여기에는 **실행 순서표(워크플로)** 만 있습니다. 전략 코드 · 시세 자료 · 기록은 비공개 저장소에 있고, 실행할 때마다 읽어 옵니다.
- 공개 저장소의 GitHub Actions 는 무료라서, 비공개 저장소의 월 사용량과 상관없이 매일 돕니다.
- 실행 기록(로그)은 누구나 볼 수 있으므로 **가격 · 신호 · 비중을 로그에 남기지 않습니다.** 결과는 텔레그램으로만 갑니다.
- 매매 주문을 하지 않습니다. 메리츠 연결은 **읽기 전용**이고, 주문 · 정정 · 취소 · 환전 주소는 코드가 부를 수 없게 막혀 있습니다.
- 계좌 조회는 아래 `account-ledger.yml` 하나만 하며, **기본 꺼짐**입니다. 켜도 기록은 비공개 저장소에만 남고 로그에는 숫자가 없습니다.

## 워크플로

| 파일 | 하는 일 | 언제 |
|---|---|---|
| `briefing.yml` | 공식 브리핑 — 판단 · 기록 · 텔레그램 (한 세션 한 번) | 외부 정시 예약 (`scheduled=true`) · 예비로 GitHub 예약 |
| `shadow-meritz.yml` | 메리츠 종가로 «같은 답이 나왔을까» 기록 (공식 아님) | 마감 직후 여러 번 |
| `watchdog.yml` | 오후에 오늘 기록이 없으면 텔레그램 경고 | 한국 14:27 |
| `account-ledger.yml` | (선택) 실제 계좌의 QLD · TQQQ · QQQ 보유 · 체결을 비공개 브랜치 `account-history` 에 쌓기 | **꺼짐** — 직접 `Run workflow` 하거나 변수 `QLD_ACCOUNT_LEDGER=on` 일 때만 한국 06:50 |

### 계좌 기록 켜기 · 끄기

- 한 번만: `Actions` → `QLD Account Ledger` → `Run workflow`
- 매일 자동: `Settings` → `Secrets and variables` → `Actions` → **`Variables`** 탭 → 이름 `QLD_ACCOUNT_LEDGER`, 값 `on`
- 끄기: 그 변수를 지우거나 값을 `off` 로. 쌓인 기록은 그대로 남습니다.

## 비밀 (Settings → Secrets and variables → Actions)

| 이름 | 무엇 |
|---|---|
| `QLD_REPO_TOKEN` | 비공개 저장소 하나만 읽고 기록 브랜치에 덧붙일 수 있는 토큰 (fine-grained · Contents 읽기/쓰기) |
| `QLD_TELEGRAM_TOKEN` · `QLD_TELEGRAM_CHAT_ID` | 텔레그램 봇 (기존 값과 같음) |
| `MERITZ_APP_KEY` · `MERITZ_APP_SECRET` | 메리츠증권 Open API (그림자 검증용) |

비밀은 GitHub 가 암호화해 보관하고 로그 · 복제본(fork) · 수정 요청(pull request) 어디에도 넘어가지 않습니다.
이 저장소는 `pull_request` 로 실행되지 않으며, 쓰는 외부 액션은 GitHub 공식 `checkout` · `setup-python` 둘을 커밋으로 고정한 것뿐입니다.
