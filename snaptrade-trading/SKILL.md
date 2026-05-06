---
name: snaptrade-trading
description: >
  Execute trades and retrieve account data via the SnapTrade API using the
  snaptrade-python-sdk. Use this skill whenever OpenClaw needs to place a buy
  or sell order, check account balances, get current positions, retrieve order
  history, fetch symbol quotes, cancel an open order, or refresh account
  holdings through SnapTrade. Triggers on any trading action or account
  data request routed through SnapTrade.
credentials:
  - name: SNAPTRADE_CLIENT_ID
    description: SnapTrade partner client ID. Used to authenticate all API requests. Treat as sensitive.
    required: true
  - name: SNAPTRADE_CONSUMER_KEY
    description: SnapTrade partner consumer key. Used to sign all API requests. Treat as sensitive — do not share or log.
    required: true
  - name: SNAPTRADE_USER_ID
    description: SnapTrade user ID for the connected brokerage user. Grants access to that user's account data and trading permissions.
    required: true
  - name: SNAPTRADE_USER_SECRET
    description: SnapTrade user secret for the connected brokerage user. Acts as a per-user API key. Treat as sensitive — rotate via SnapTrade dashboard if compromised.
    required: true
warnings:
  - This skill can place real trades and cancel orders on connected brokerage accounts.
  - Default to requiring user confirmation per trade. Automated mode is supported if explicitly configured — enforce symbol allowlists, notional caps, position limits, and daily loss limits before enabling.
  - Use paper trading or a low-limit account during testing.
  - Store credentials in a .env file or secret manager — never hardcode, log, or pass them through untrusted channels.
  - Rotate SNAPTRADE_USER_SECRET immediately via the SnapTrade dashboard if there is any chance it was exposed.
  - Prefer a dedicated SnapTrade user with limited brokerage permissions for automated trading rather than using your primary account credentials.
---

# SnapTrade Trading Skill

## Setup

**First-time only:** run the setup script:
```bash
bash scripts/setup.sh
```

**Required env vars:**
```
SNAPTRADE_CLIENT_ID
SNAPTRADE_CONSUMER_KEY
SNAPTRADE_USER_ID
SNAPTRADE_USER_SECRET
```

## SDK Initialization (always required)

```python
import os
from snaptrade_client import SnapTrade

snaptrade = SnapTrade(
    consumer_key=os.environ["SNAPTRADE_CONSUMER_KEY"],
    client_id=os.environ["SNAPTRADE_CLIENT_ID"],
)
user_id = os.environ["SNAPTRADE_USER_ID"]
user_secret = os.environ["SNAPTRADE_USER_SECRET"]
```

---

## Reference Files — Read the relevant one before proceeding

| Task | Read |
|---|---|
| Get accounts, balances, positions, orders, historical value | `references/account-data.md` |
| Resolve a ticker to a symbol ID + get quotes | `references/symbol-resolution.md` |
| Place an equity trade (limit, market, bracket) | `references/place-orders.md` |
| Place an options order (single or multi-leg) | `references/options-trading.md` |
| Place a crypto order | `references/crypto-trading.md` |
| Cancel an order or refresh holdings | `references/cancel-refresh.md` |
| Get historical transactions / activity log | `references/historical-data.md` |

---

## Important Constraints

- `trade_id` from an impact check expires in **5 minutes** — place immediately after
- SnapTrade does **not** provide OHLCV/candlestick data — use a separate market data provider for technical analysis
- Not all brokerages support trading — check SnapTrade's broker support matrix
- Always resolve symbol IDs fresh via `references/symbol-resolution.md` rather than caching them long-term
- After placing or cancelling an order, trigger a manual refresh (see `references/cancel-refresh.md`)
