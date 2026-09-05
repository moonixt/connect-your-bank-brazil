---
name: connect-your-bank-brazil
description: >-
  Use this when connecting Brazilian banks for free personal Open Finance via
  Meu Pluggy (own CPF): Meu Pluggy connections, Dashboard app, connector 200,
  Demo OAuth, Item ID, and Pluggy API accounts/transactions.
---
# Connect your Bank (Brazil)

Reusable workflow to aggregate **your own** Brazilian bank data via Pluggy at **no monthly cost**. This is **not** the commercial multi-user plan (~R$ 2.500/mo).

## Free vs paid

| Path | Cost | Use when |
|---|---|---|
| **Meu Pluggy + connector `200`** | Free, no expiry (per Pluggy) | Only **your** CPF / your banks |
| Direct bank connectors (Nubank, Itaú…) in Dashboard | Trial ~15 days, then paid | Product for other people |

The Dashboard may show a **15-day trial** banner. That applies to **commercial** features. Personal Meu Pluggy access is a separate free path — as long as you connect through **MeuPluggy (200)**, not live bank connectors in the widget.

## Prerequisites

- Brazilian bank account(s) that support Open Finance
- Browser access to `meu.pluggy.ai` and `dashboard.pluggy.ai`
- User completes bank consent / OAuth / 2FA (the agent must not enter passwords)

## 1 — Connect banks in Meu Pluggy

1. Open https://meu.pluggy.ai and sign up / sign in.
2. Under **Conexões** (Connections), connect each bank (Open Finance consent in the bank app).
3. Confirm balances on **Overview**.

Do this first. The Dashboard only proxies what already exists in Meu Pluggy.

## 2 — Developer Dashboard app

1. Open https://dashboard.pluggy.ai and create/sign in.
2. Create or open an **Application** (Development is fine).
3. In connectors / personalize settings, enable **MeuPluggy** (connector id **`200`**).
   - Personal free path = MeuPluggy.
   - Do **not** rely on connecting Nubank/Itaú **directly** in the Demo if the goal is free personal use.
4. Copy **Client ID** and **Client Secret** from **Aplicações → Configurar → Credenciais**.
   - Store the secret in a local file the agent/user controls (e.g. workspace). Never paste secrets into chat.
   - Agents often cannot reveal the secret in the Dashboard UI — the user must copy it.

## 3 — Link Meu Pluggy via Demo OAuth

1. Dashboard: **Aplicações → Iniciar Demo / Ir para Demo**.
2. **Conectar conta** → search/select **MeuPluggy** (not the bank brand).
3. Complete Meu Pluggy OAuth / authorize.
4. Repeat once per bank connected in Meu Pluggy (one Item per bank, not per account).

### Common error (wrong connector)

If you select **Nubank** (or another live bank) and see:

> Contas de teste só podem conectar conectores sandbox (Pluggy Bank). Solicite acesso a dados reais…

You chose the **commercial/live** path. Go back and select **MeuPluggy**.

Sandbox **Pluggy Bank** (`user-ok` / `password-ok`) is synthetic only.

### “Liberar dados reais” form

Overview may push **Solicitar acesso à produção / Liberar dados reais** (sales-style form). That unlocks commercial live connectors. For the free personal path, prioritize **MeuPluggy OAuth in Demo**. Do not assume the sales form is required for connector 200; do not commit to a paid plan unless the user wants multi-user commercial access.

## 4 — Copy Item ID(s)

1. In Demo, open the connected item menu (⋮).
2. **Copiar Item ID**.
3. Save each Item ID with the Client ID (secret stays in the local file).

You need: `client_id`, `client_secret`, and one or more `item_id`s.

## 5 — Call the API

Base URL: `https://api.pluggy.ai`

### Auth

`POST /auth` with JSON `{ "clientId", "clientSecret" }` → `apiKey`.

Send later requests with header `X-API-KEY: <apiKey>`.

### Accounts

`GET /accounts?itemId=<ITEM_ID>`

### Transactions (v2 + cursor)

Legacy `GET /transactions` returns **410 Gone**. Use:

`GET /v2/transactions?accountId=<ACCOUNT_ID>&dateFrom=YYYY-MM-DD&dateTo=YYYY-MM-DD`

Pagination: follow response `next` / `after` until empty. See Pluggy docs.

### Interpreting amounts (typical)

- **BANK**: negative `amount` ≈ outflow (expense).
- **CREDIT**: positive `amount` ≈ purchase; negative often payment/credit.
- Categories can be wrong (e.g. a hoster labeled “School”) — prefer `description` / merchant in user-facing summaries.
- When answering “what did I spend most on”, optionally exclude `Same person transfer` and card payment categories.

### Bills (credit cards)

`GET /bills?accountId=<CREDIT_ACCOUNT_ID>` is useful for invoice totals.

## 6 — Sanity check

1. Auth succeeds.
2. Accounts match Meu Pluggy Overview (balances).
3. Pull one month via `/v2/transactions` and summarize top categories/descriptions.

## Security

- Never put Client Secret in chat, public screenshots, or git.
- Prefer a local file on the agent machine; only delete it if the user asks.
- Do not use this flow for **other people’s** banks — that needs a paid commercial plan and proper product consent UX.

## Out of scope

- Becoming a Bacen-regulated recipient yourself
- Guaranteeing bank coverage or refresh latency
- Multi-tenant apps on the free Meu Pluggy path

## Checklist

- [ ] Banks connected on `meu.pluggy.ai`
- [ ] Dashboard app with **MeuPluggy (200)** enabled
- [ ] Client ID + Secret saved locally
- [ ] Demo OAuth via **MeuPluggy** (not direct bank)
- [ ] Item ID(s) copied
- [ ] `POST /auth` works
- [ ] `GET /accounts` matches Overview
- [ ] `GET /v2/transactions` with `dateFrom`/`dateTo` + cursor works
