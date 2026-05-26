# Security Policy

## Supported versions

Use the latest `main` branch for security fixes.

## Secrets

Never commit:

- `.env` files;
- T-Invest API tokens;
- Telegram bot tokens;
- real brokerage account ids;
- runtime cache from `data/`;
- local deployment logs.

If a secret was committed at any point, rotate it immediately. Removing it from a later commit is not enough because Git history may still contain it.

## Reporting a vulnerability

Open a private security advisory if available, or contact the repository owner directly. Do not publish working exploits or real tokens in public issues.

## Trading safety

This project is intended for read-only monitoring by default. Do not enable live trading without independent code review, strict risk limits and audit logging.
