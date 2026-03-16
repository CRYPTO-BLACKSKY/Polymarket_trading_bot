# Polymarket BTC 5-Minute Trading | Arbitrage | Bot
An automated trading bot designed to identify and execute short-term opportunities in Bitcoin markets on Polymarket. The bot analyzes market data in real time and places trades on 5-minute prediction intervals, allowing it to capitalize on rapid price movements and micro-market inefficiencies.

**🌐 Language:** [English](README.md) | [中文](README.zh-CN.md) | [Français](README.fr.md) | [Español](README.es.md)

**📞 Contact:** [S.E.I](https://t.me/sei_dev) (Telegram)

---

🤖 Automated trading bot for Polymarket BTC 5-minute up/down markets. Trade 24/7 with three strategies:

| Strategy | Description | Bot |
|----------|-------------|-----|
| **Strategy 1** | Arbitrage at the middle of the market | [@sei_arb_bot](https://t.me/sei_arb_bot)(~30 min), Script Bot |
| **Strategy 2** | High-opportunity trading at end of market cycle | [@seitrading_bot](https://t.me/seitrading_bot)(~1 hour), Script Bot |
| **Strategy 3** | Buy one of both UP/DOWN; when liquidity changes, get winning shares for $0.01 | Script Bot

📹 **Watch on YouTube**

[![YouTube – Polymarket 5min Trading Bot](https://img.youtube.com/vi/teeMT-c4S3o/maxresdefault.jpg)](https://www.youtube.com/watch?v=teeMT-c4S3o)

---

## Strategy 1: Arbitrage (mid-market)

Buy both sides, merge to recover USDC. **Try in ~30 min:** [@sei_arb_bot](https://t.me/sei_arb_bot)
📹 **Guide Demo:** [Watch on YouTube](https://www.youtube.com/watch?v=NsRDKPQrRIs)
### Screenshots

|  |  |  |
|--|--|--|
| ![image1](assets/image1.png) | ![image2](assets/image2.png) | ![image3](assets/image3.png) |

| Result |
|--------|
| ![Result](assets/result.png) |

### Features

- 🔍 Auto Market Discovery – Finds active BTC 5-minute markets
- 📊 Smart Position Management – Monitors UP/DOWN positions
- 🛡️ Risk Protection – Auto-sells before market close
- 💰 Token Merging – Recovers USDC from equal positions

### How It Works

1. Finds the current BTC 5-minute market  
2. Monitors UP/DOWN token positions  
3. Merges equal positions to recover USDC  
4. Force-sells before market close (30s threshold)  
5. Places orders for the next market automatically  

---

## Strategy 2: End-of-cycle trading

High-opportunity trading at the end of the market cycle. **Try in ~1 hour:** [@seitrading_bot](https://t.me/seitrading_bot)

### Screenshots

| Result 1 |
|----------|
| ![Result 1](assets/result1.png)
### Features

- High-opportunity detection at end of market cycle
- Automated timing and order placement
- Risk-managed exposure

### How It Works

1. Monitors the current 5-minute market toward resolution  
2. Identifies high-opportunity moments at end of cycle  
3. Places or adjusts orders accordingly  
4. Manages positions and exits before market close  

---

## Strategy 3: Win with $0.01 when liquidity shifts 

**Strategy:** You generally buy **one of both** UP and DOWN. When market liquidity changes, you can get **winning shares bought with $0.01** – minimal risk, same crypto up/down market.

### Screenshots

| Result |
|--------|
| ![Result 2](assets/result2.png) |

### Features

- Ultra-low risk – target $0.01 per side (UP and/or DOWN)
- Exploits liquidity shifts in the crypto 5-minute up/down market
- When liquidity changes, winning shares can be had for $0.01
- Same market structure; different entry (one or both sides at minimal size)


## Quick Start

### 1. Install dependencies

```bash
npm install
```

### 2. Configure environment

```bash
cp .env.example .env
```

Then edit `.env` and set at least:

- `TARGET_WALLET` — Polymarket wallet address to copy (`0x...`)
- `WALLET_PRIVATE_KEY` — Your wallet private key (`0x...`)
- `RPC_URL` — Polygon RPC URL (for example, QuickNode)

For initial testing, use conservative sizing (for example, `MAX_TRADE_SIZE=5`, `POSITION_MULTIPLIER=0.1`).

### 3. Run the bot

```bash
npm start
```

On first run in EOA mode, the bot may submit approval transactions for USDC.e/CTF spenders. Ensure the wallet has sufficient POL for gas.

---

## Architecture

| Component           | Role |
|---------------------|------|
| **TradeMonitor**    | Polls the Polymarket Data API via REST to discover new trades from the target wallet. |
| **WebSocketMonitor**| Provides optional real-time updates via Polymarket `market` or `user` WebSocket channels. |
| **TradeExecutor**   | Sizes copy trades, verifies balances and allowances, and places orders via the CLOB client (FOK/FAK/LIMIT). |
| **PositionTracker** | Maintains in-memory positions based on successful fills and updates. |
| **RiskManager**     | Enforces a session notional cap and per-market notional cap prior to execution. |

High-level execution flow: detect trade → (if BUY) subscribe to WebSocket if needed → compute copy size → apply risk checks → execute order → record fill and update statistics.

---

## Environment variables

| Variable | Required | Description |
|----------|----------|-------------|
| `TARGET_WALLET` | Yes | Polymarket wallet address to copy (for example, `0x...`). |
| `WALLET_PRIVATE_KEY` | Yes | Your wallet private key (0x-prefixed). |
| `RPC_URL` | Yes | Polygon RPC URL (for example, QuickNode). |
| `SIG_TYPE` | No | Polymarket signature type. `0` = EOA (default), `1` = Poly Proxy, `2` = Poly Polymorphic. |
| `PROXY_WALLET_ADDRESS` | For 1/2 | Funder/proxy/safe address when using `SIG_TYPE=1` or `2`. Leave empty for EOA. |
| `POLYMARKET_GEO_TOKEN` | No | Geo token if provided by Polymarket for your region. |
| `POSITION_MULTIPLIER` | No | Copy size multiplier (default `0.1` = 10%). |
| `MAX_TRADE_SIZE` | No | Maximum copy trade size in USDC (default `100`). |
| `MIN_TRADE_SIZE` | No | Minimum copy trade size in USDC (default `1`). |
| `SLIPPAGE_TOLERANCE` | No | Slippage tolerance (default `0.02` = 2%). |
| `ORDER_TYPE` | No | `FOK`, `FAK`, or `LIMIT` (default `FOK`). |
| `USE_WEBSOCKET` | No | `true` / `false` (default `true`). |
| `USE_USER_CHANNEL` | No | Whether to use the user channel for WebSocket (default `false`). |
| `POLL_INTERVAL` | No | REST poll interval in milliseconds (default `2000`). |
| `WS_ASSET_IDS` | No | Comma-separated asset IDs for the market WebSocket. |
| `WS_MARKET_IDS` | No | Comma-separated market/condition IDs for the user channel. |
| `MAX_SESSION_NOTIONAL` | No | Session notional cap in USDC; `0` disables the cap. |
| `MAX_PER_MARKET_NOTIONAL` | No | Per-market notional cap in USDC; `0` disables the cap. |
| `MIN_PRIORITY_FEE_GWEI` | No | Minimum priority fee for Polygon transactions (default `30`). |
| `MIN_MAX_FEE_GWEI` | No | Minimum max fee for Polygon transactions (default `60`). |

---

## Authentication (SIG_TYPE and PROXY_WALLET_ADDRESS)

- **`SIG_TYPE=0` (EOA)**  
  The signer is an EOA. Do not set `PROXY_WALLET_ADDRESS`. API credentials are derived or created from `WALLET_PRIVATE_KEY` at startup.

- **`SIG_TYPE=1` (Poly Proxy)**  
  Set `PROXY_WALLET_ADDRESS` to your Polymarket proxy/safe address (funder).

- **`SIG_TYPE=2` (Poly Polymorphic)**  
  Set `PROXY_WALLET_ADDRESS` to your polymorphic safe address (funder).

All CLOB interactions (`trader`, `generate-api-creds`, `test-api-creds`) use authentication derived from configuration, driven by `SIG_TYPE` and `PROXY_WALLET_ADDRESS` in `.env`.

---

## Commands

| Command | Description |
|---------|-------------|
| `npm start` | Run the bot in development mode. |
| `npm run generate-api-creds` | Generate and write API credentials to `.polymarket-api-creds` (uses `SIG_TYPE` and `PROXY_WALLET_ADDRESS` from `.env`). |
| `npm run test-api-creds` | Validate static API credentials using the same authentication environment variables. |
| `npm run build` | Compile the TypeScript source to `dist/`. |
| `npm run start:prod` | Run the compiled `dist/index.js`. |

---

## Example output

**Startup**

```
 Polymarket Copy Trading Bot
================================
Target wallet: 0x...
Position multiplier: 10%
Max trade size: 100 USDC
Order type: FOK
WebSocket: Enabled
Auth: EOA (signature type 0)
================================

 Configuration validated
 Trader initialized
 WebSocket monitor initialized (market channel)
 Bot started! Monitoring via: WebSocket + REST API
```

**When a BUY is detected and copied**

```
 NEW TRADE DETECTED
   Side: BUY YES
   Size: 12.5 USDC @ 0.620
   ...
 Executing copy trade (FOK): Copy notional: 1.25 USDC
 Successfully copied trade!
 Session Stats: 1/1 copied, 0 failed
```

**When a SELL is detected (skipped)**

```
  Skipping SELL trade (BUY-only safeguard enabled)
```

---

## Disclaimer

This code has **not** been audited. Use at your own risk. You should independently review and test the code, and obtain a professional security review before using real funds or deploying to production. Start with very small position sizes (for example, `MAX_TRADE_SIZE=5` or lower) while you validate the behavior.

---

## References

- [Building a Polymarket Copy Trading Bot \| QuickNode](https://www.quicknode.com/guides/defi/polymarket-copy-trading-bot)
- [Polymarket](https://polymarket.com/) — prediction markets on Polygon
- [Polymarket CLOB](https://github.com/Polymarket/clob-client) — order book client
