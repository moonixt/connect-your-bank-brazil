---
name: connect-your-bank-brazil
description: >-
  Use this when connecting Brazilian banks for free personal Open Finance via
  Meu Pluggy (own CPF). Agent drives the browser; user only does
  login/OAuth/2FA. Covers Meu Pluggy, Dashboard connector 200, Demo OAuth (one
  Item per bank), credentials on disk, and Pluggy API smoke test.
---
# Connect your Bank (Brazil)

Drive **free personal** Open Finance setup in Brazil via Meu Pluggy + Pluggy API (user’s own CPF only). Not the commercial multi-user plan (~R$ 2.500/mo).

## How the agent must behave (read first)

- **You run the workflow.** Open `meu.pluggy.ai` and `dashboard.pluggy.ai` in the browser; click through menus; copy Item IDs from the Demo UI yourself.
- **User only does secrets:** passwords, bank consent, 2FA, Meu Pluggy OAuth approve. Hand the desktop over for those; do not ask them to “follow a long checklist.”
- **One short message at a time.** Say what you’re doing now and what you need from them (one line). Never paste the whole skill as instructions for the user.
- **Never** ask for Client Secret, passwords, or full email dumps in chat. Secret → local file path the user controls (they paste into an editor/file on the machine). Client ID and Item IDs may be stored in agent memory after you copy them from the UI.
- **Do not** invent “prepare pluggy.cfg / helper scripts” unless the user asks. Goal is: banks linked + credentials on disk + Item IDs + a successful API smoke test.
- If Meu Pluggy already has banks but Demo lacks Items, **skip to Demo OAuth** — don’t restart from zero.

## Free vs paid (for the agent)

| Path | Cost | Use |
|---|---|---|
| MeuPluggy connector **`200`** | Free, no expiry (per Pluggy) | Personal / own CPF |
| Direct Nubank/Itaú/etc. in widget | Trial then paid | Commercial multi-user |

Ignore the 15-day trial banner for personal MeuPluggy use. **Never** select live bank brands in Demo for the free path — only **MeuPluggy**.

## Item model (common confusion)

- **1 Item = 1 bank** (institution). Checking + card of that bank share one Item.
- Two Items ≠ “max 2 apps”; it means two banks. Add more banks the same way.
- Development apps allow on the order of **~100 Items** (Pluggy FAQ).
- Do **not** create duplicate Items for the same CPF + same institution (burns Open Finance rate limits).
- Meu Pluggy Overview shows a bank but API doesn’t → Demo OAuth for that bank not done yet.

## Agent procedure (from zero)

### A — Meu Pluggy banks

1. Open https://meu.pluggy.ai ; if login wall → hand off to user; continue after.
2. Open **Conexões** / connect flow. For each bank the user wants: start connect, hand off for bank auth, confirm it appears on Overview.
3. Tell the user briefly which banks are connected (names + rough balances). Stop inventing banks.

### B — Dashboard app + MeuPluggy connector

1. Open https://dashboard.pluggy.ai ; login handoff if needed.
2. Ensure an **Application** exists (Development OK). Create one only if missing.
3. Enable connector **MeuPluggy (`200`)** in the app’s connector / personalize settings.
4. Open **Credenciais**. Read/copy **Client ID** yourself. For **Client Secret**: ask user to reveal/copy into a local file (e.g. workspace secret file); you do not paste secret into chat. Confirm the file is non-empty without echoing its contents.

### C — Demo OAuth (once per bank)

1. **Aplicações → Iniciar Demo / Ir para Demo**.
2. **Conectar conta** → select **MeuPluggy** only (search “Meu”).
3. Hand off for OAuth / which Meu Pluggy connection to authorize.
4. On success, from Demo item menu (⋮) **Copiar Item ID**. Save it labeled with the bank name.
5. Repeat C for **each** bank on Meu Pluggy until Demo item count matches.

### Wrong-path errors

- Message like *“Contas de teste só podem conectar conectores sandbox…”* while on Nubank/Itaú → wrong connector. Back out; use **MeuPluggy**.
- Sales form **Liberar dados reais / Falar com Vendas** → do not fill for personal free path; stay on MeuPluggy Demo OAuth.
- Sandbox Pluggy Bank (`user-ok` / `password-ok`) is synthetic only — not real balances.

### D — API smoke test

Base: `https://api.pluggy.ai`

1. `POST /auth` with `clientId` + `clientSecret` from local secret file → `apiKey` (`X-API-KEY`).
2. For each Item ID: `GET /accounts?itemId=…` — balances should match Overview.
3. Transactions: use **`GET /v2/transactions`** with `accountId`, `dateFrom`, `dateTo`, and cursor `next`/`after`. Legacy `GET /transactions` returns **410**.
4. Amount heuristics: BANK negative ≈ expense; CREDIT positive ≈ purchase. Prefer `description` over flaky categories. Optionally exclude `Same person transfer` and card payments when summarizing spend.
5. Report success briefly to the user (banks + Item IDs count). Keep secret on disk unless they ask to delete.

## Amounts / spend (optional follow-up)

Only after D works. Pull a month of `/v2/transactions` across accounts, aggregate by description/category, answer in the user’s language. Don’t re-dump setup instructions.

## Out of scope

- Bacen registration as a recipient
- Guaranteeing institution coverage or refresh latency
- Multi-tenant / other people’s banks on the free path
- Dumping long DIY manuals into chat

## Done when

- [ ] Meu Pluggy shows the intended banks  
- [ ] MeuPluggy (200) enabled on the Dashboard app  
- [ ] Client ID known; Client Secret in a local file (not chat)  
- [ ] One Demo Item ID per bank  
- [ ] `/auth` + `/accounts` OK for each Item  
- [ ] `/v2/transactions` works (if spend was requested)
