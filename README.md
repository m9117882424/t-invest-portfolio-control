# T-Invest Portfolio Control

A read-only portfolio monitoring and decision-support tool for T-Invest accounts.

The project combines broker portfolio data, MOEX ISS market data, configurable target allocation, technical filters, fundamental risk rules and Telegram notifications.

> This repository is a public engineering showcase. It is not investment advice, not brokerage software and not an automated trading system.

## What it does

- Reads current portfolio positions from T-Invest API.
- Compares actual allocation with a configurable target portfolio.
- Uses MOEX ISS for quotes and daily candles.
- Calculates technical status: trend, SMA, RSI, volatility and recent price changes.
- Applies fundamental filters: P/E, P/B, EV/EBITDA, ROE, Debt/EBITDA and dividend yield.
- Supports sector-specific risk thresholds.
- Builds final decisions: buy, hold, wait, risk, overweight or no cash.
- Sends compact Telegram reports.
- Can run continuously on a Linux server through systemd.

## Architecture

```text
T-Invest API           -> portfolio, accounts, positions, orders
MOEX ISS               -> quotes, candles, market fallback
YAML configuration     -> target portfolio and risk rules
Python decision engine -> allocation, technical and fundamental filters
Telegram bot           -> reports and alerts
systemd                -> server-side scheduled monitoring
```

## Safety model

The default mode is `READ_ONLY`.

In this mode the service:

- does not create orders;
- does not sell assets;
- does not rebalance automatically;
- only reads portfolio data and builds recommendations.

Real tokens and account identifiers must be stored only in a local `.env` file. The repository ignores `.env`, runtime cache, local backups, logs and virtual environments.

## Example Telegram report

```text
📊 Контроль портфеля

💼 Сводка
• RUB на счёте: 6 444.88 ₽
• Доступно RUB: 6 444.88 ₽
• Бюджет на решения: 5 026.00 ₽

📋 Позиции
🟢 SBER: факт 29.17% / цель 30.00% / откл. -0.83% — держать
🔵 LKOH: факт 21.28% / цель 17.00% / откл. +4.28% — не докупать
💤 YDEX: факт 8.46% / цель 13.00% / откл. -4.54% — нет бюджета
⏸ PLZL: факт 9.01% / цель 14.00% / откл. -4.99% — ждать
```

## Tech stack

- Python 3.10+
- T-Invest Python SDK
- MOEX ISS HTTP API
- Rich CLI tables
- YAML configuration
- Telegram Bot API
- systemd deployment

## Installation

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\\Scripts\\activate
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```

The T-Invest SDK package may be unavailable on PyPI. Install it directly from the official GitHub repository:

```bash
pip install --no-deps git+https://github.com/RussianInvestments/invest-python.git
```

## Configuration

Create local environment file:

```bash
cp .env.example .env
```

Fill it with a read-only token:

```env
T_INVEST_TOKEN=replace_with_read_only_token
T_INVEST_ACCOUNT_ID=
APP_MODE=READ_ONLY
```

Find available accounts:

```bash
python -m tinvest_portfolio accounts
```

Then set `T_INVEST_ACCOUNT_ID` in `.env`.

## CLI commands

```bash
python -m tinvest_portfolio doctor
python -m tinvest_portfolio accounts
python -m tinvest_portfolio report
python -m tinvest_portfolio market
python -m tinvest_portfolio fundamental
python -m tinvest_portfolio decision
python -m tinvest_portfolio notify
python -m tinvest_portfolio watch
```

MOEX ISS check:

```bash
python -m tinvest_portfolio moex SBER
python -m tinvest_portfolio moex GAZP --days 90
```

## Configuration files

```text
config/target_portfolio.yaml      target allocation and rebalance threshold
config/fundamental_rules.yaml     sector limits, metric thresholds and manual metrics
.env                              local secrets, ignored by Git
```

Fundamental data is intentionally manual/cache-based in the public version. Fill multipliers from trusted sources, then update cache:

```bash
python -m tinvest_portfolio fundamentals_update
python -m tinvest_portfolio fundamental
```

## Server deployment

The project can run as a Linux service:

```bash
sudo APP_DIR=/opt/t-invest REPO_URL=https://github.com/your-user/t-invest-portfolio-control.git BRANCH=main bash deploy/install_server.sh
```

Check logs:

```bash
journalctl -u tinvest-portfolio-watch -f
```

## Repository hygiene

Before using with a real account:

- never commit `.env`;
- use read-only API tokens first;
- keep personal target portfolios private;
- keep `data/fundamentals_cache.json` out of Git;
- rotate any token that was ever committed by mistake;
- review GitHub secret scanning alerts.

## Roadmap

- Add tests for decision engine rules.
- Add GitHub Actions CI.
- Add optional HTML report export.
- Add pluggable fundamental data providers.
- Add dashboard mode for local browser view.
- Add stricter risk limits before any trading-related mode.

## Disclaimer

This project is for portfolio monitoring and software engineering demonstration. It does not provide financial advice. Use at your own risk and verify all calculations independently.
