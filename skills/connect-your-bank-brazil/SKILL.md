---
name: connect-your-bank-brazil
description: >-
  Use this for free personal Open Finance in Brazil via Meu Pluggy. Default:
  detailed setup tutorial in chat. If the user asks you to connect for them and
  you have browser/computer use, drive the flow with handoff only for
  login/OAuth/2FA. Then help with Pluggy API (one Item per bank).
---
# Connect your Bank (Brazil)

Free **personal** Open Finance in Brazil via Meu Pluggy + Pluggy API (user’s own CPF only). Not the commercial multi-user plan (~R$ 2.500/mo).

## Mode selection (read first)

| Situation | What to do |
|---|---|
| User asks how to set up / connect banks, or agent has **no** browser/computer use | **Tutorial mode** — deliver the detailed guide below in chat. Do not pretend to open sites. |
| User explicitly asks you to **do it for them** / “abre aí” / “conecta pra mim” **and** you have browser or computer-use | **Assisted mode** — drive the flow yourself; hand off only for password, bank consent, 2FA, OAuth approve. |
| User asks you to drive it but you **lack** browser/computer use | Say you can’t automate here, then run **Tutorial mode**. |

Never dump the whole skill as a wall of meta-instructions. In Tutorial mode, give a clean user-facing guide. In Assisted mode, one short status line at a time.

**Secrets:** never ask for Client Secret / passwords in chat. Secret → local file; user pastes into that file if needed. Client ID and Item IDs may be saved after you or the user copy them from the UI.

---

## Tutorial mode (default)

Detect language (Portuguese if they write in PT). Deliver this guide (adapt wording, keep warnings).

### Overview

1. Connect banks on **Meu Pluggy**.  
2. Create a **Pluggy Dashboard** app and enable **MeuPluggy (connector 200)**.  
3. In **Demo**, OAuth via **MeuPluggy** once per bank → copy each **Item ID**.  
4. Keep Client ID + Client Secret (file) + Item IDs for the API.

Avoid connecting Nubank/Itaú **directly** in the widget (paid/trial). Use **MeuPluggy** only for free personal use.

### Step 1 — Meu Pluggy

1. https://meu.pluggy.ai — sign up / sign in.  
2. **Conexões** → connect each bank; approve Open Finance in the bank.  
3. Confirm banks on **Overview** (ignore pre-login marketing demo balances).

### Step 2 — Dashboard

1. https://dashboard.pluggy.ai — sign up / sign in.  
2. Create an **Application** (Development OK).  
3. **Configurar → Credenciais** → copy Client ID and Client Secret (secret only in a local file).  
4. Enable connector **MeuPluggy (`200`)**.  
5. A **15-day trial** banner is commercial; personal MeuPluggy stays free via connector 200.

### Step 3 — Demo OAuth (one Item per bank)

1. **Aplicações → Iniciar Demo / Ir para Demo**.  
2. **Conectar conta** → select **MeuPluggy** (not the bank brand).  
3. Authorize; copy **⋮ → Item ID**; label with the bank name.  
4. Repeat once per bank on Meu Pluggy.

**Item rules:** 1 Item = 1 bank (checking + card share it). Not a max-2 cap. Dev ~100 Items. Don’t duplicate same CPF + institution. Overview has bank but API doesn’t → Demo OAuth missing for that bank.

**Error:** *Contas de teste só podem conectar conectores sandbox…* → you picked a live bank; use **MeuPluggy**. Skip **Liberar dados reais / Falar com Vendas** for personal free path. Sandbox Pluggy Bank is fake data.

### Step 4 — Hand back to the agent

- Client ID (chat OK)  
- Client Secret **file path** (not the secret text)  
- Item ID per bank  

Then the agent can run the API smoke test (below).

---

## Assisted mode (browser / computer use only)

Only if the user asked you to perform setup **and** you can operate a browser.

### Behavior

- You click through Meu Pluggy and Dashboard.  
- Hand desktop to user only for login / bank consent / 2FA / Meu Pluggy OAuth.  
- Short updates (“Opening Demo…”, “Your turn to authorize PicPay”).  
- Copy Item IDs from Demo yourself. Confirm secret file non-empty without echoing contents.  
- Don’t fill sales forms. Don’t select live bank brands in Demo.

### Procedure

**A — Meu Pluggy:** open site → login handoff if needed → connect each requested bank (handoff per bank) → confirm Overview.

**B — Dashboard:** open site → login handoff → ensure Application → enable MeuPluggy 200 → credentials page → user saves Client Secret to local file; you note Client ID.

**C — Demo:** Ir para Demo → Conectar → **MeuPluggy** only → OAuth handoff → copy Item ID → repeat per bank until Demo matches Meu Pluggy.

**D — Smoke test:** run API section below; report banks + Item count briefly.

If Meu Pluggy already has banks, skip to B/C as needed.

---

## API smoke test (both modes)

Base: `https://api.pluggy.ai`

1. `POST /auth` `{ "clientId", "clientSecret" }` → `apiKey` → header `X-API-KEY`.  
2. `GET /accounts?itemId=…` per Item — match Overview.  
3. Transactions: **`GET /v2/transactions?accountId=&dateFrom=&dateTo=`** + cursor `next`/`after`. Legacy `GET /transactions` → **410**.  
4. Amounts: BANK negative ≈ expense; CREDIT positive ≈ purchase. Prefer `description`; optionally exclude `Same person transfer` and card payments when summarizing spend.

Optional follow-up: month spending summary once setup works — don’t re-send the full tutorial unless asked.

## Free vs paid (reference)

| Path | Cost |
|---|---|
| MeuPluggy connector **200** | Free personal |
| Direct bank connectors in widget | Trial then paid commercial |

## Out of scope

- Claiming browser automation when you don’t have it  
- Bacen recipient registration  
- Other people’s banks on the free path  
- Guaranteeing coverage / refresh latency
