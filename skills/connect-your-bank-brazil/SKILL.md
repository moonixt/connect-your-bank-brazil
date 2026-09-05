---
name: connect-your-bank-brazil
description: >-
  Use this when the user wants free personal Open Finance in Brazil via Meu
  Pluggy. Deliver a detailed setup tutorial in chat (no browser automation).
  Then help with Pluggy API using Client ID, local secret file, and Item IDs
  (one per bank).
---
# Connect your Bank (Brazil)

When the user wants to connect Brazilian banks for **free personal** Open Finance (own CPF) via Meu Pluggy + Pluggy API, **do not drive a browser or assume browser-use / computer-use**. Many agents have no desktop automation.

## What you do instead

1. Detect the user’s language (default Portuguese if they write in PT).
2. **Deliver a clear, complete setup tutorial** in chat (structured steps, warnings, what to copy at the end).
3. After they finish, help with **API usage** (auth, accounts, `/v2/transactions`) using Client ID, local secret file, and Item IDs they provide — still never ask them to paste the Client Secret into chat; a file path is fine.
4. Stay available for troubleshooting (wrong connector error, missing Item after new bank, 410 on old transactions URL).

Do **not**: open `meu.pluggy.ai` / `dashboard.pluggy.ai` yourself, claim you will log in for them, or invent helper scripts unless they ask.

---

## Tutorial to give the user

Paste/adapt the following guide (translate if needed). Keep the warnings intact.

### Overview

You will:

1. Connect your banks on **Meu Pluggy** (free consumer portal).
2. Create a **Pluggy Dashboard** developer app and enable connector **MeuPluggy (id 200)**.
3. Link each bank in the **Demo** via MeuPluggy OAuth (one **Item ID per bank**).
4. Save Client ID + Client Secret + Item IDs for API access.

This is the **personal free** path. Connecting Nubank/Itaú **directly** in the Dashboard widget is the **paid/trial commercial** path — avoid it for personal use.

### Step 1 — Meu Pluggy (banks)

1. Open https://meu.pluggy.ai  
2. Sign up or sign in (email or Google/GitHub/Microsoft/Facebook).  
3. Go to **Conexões** (Connections).  
4. **Conectar** each bank you want; approve Open Finance in the bank app / site.  
5. Confirm each bank on **Overview** (ignore marketing “demo” numbers on the marketing landing page before login).

### Step 2 — Dashboard app + credentials

1. Open https://dashboard.pluggy.ai and sign up / sign in.  
2. Create an **Application** in **Development** (any name).  
3. Open the app → **Configurar → Credenciais**.  
4. Copy **Client ID** and **Client Secret**.  
   - Store the **Client Secret** in a local file only (e.g. `pluggy-client-secret.txt`). Do not paste it into chat.  
5. In the app’s connectors / personalize settings, enable **MeuPluggy** (connector **200**).  
6. You may see a **15-day trial** banner — that is for commercial features. Personal MeuPluggy access is separate and stays free when you use connector 200.

### Step 3 — Demo OAuth (Item ID per bank)

1. Dashboard → **Aplicações → Iniciar Demo / Ir para Demo**.  
2. **Conectar conta**.  
3. Search and select **MeuPluggy** — **not** the bank brand (Nubank, Inter, BB, PicPay, …).  
4. Authorize; pick the Meu Pluggy connection for that bank if asked.  
5. When the item appears, open **⋮ → Copiar Item ID**. Save it labeled with the bank name.  
6. **Repeat steps 2–5 once per bank** connected in Meu Pluggy.

#### Item rules (read carefully)

| Rule | Detail |
|---|---|
| 1 Item = 1 bank | Checking + credit card of the same bank share one Item |
| Not a “max 2” cap | Two Items means two banks; add more the same way |
| Dev limit | Development apps allow on the order of ~100 Items |
| No duplicates | Don’t create multiple Items for the same CPF + same institution (Open Finance rate limits) |
| API lag | Bank visible on Meu Pluggy Overview but missing in API → Demo OAuth for that bank not done yet |

#### If you see this error

> Contas de teste só podem conectar conectores sandbox (Pluggy Bank). Solicite acesso a dados reais…

You selected a **live bank** connector. Go back and choose **MeuPluggy** only.  
Skip **Liberar dados reais / Falar com Vendas** for the free personal path.  
Sandbox **Pluggy Bank** (`user-ok` / `password-ok`) is fake data only.

### Step 4 — What you should have

- Client ID  
- Client Secret in a **local file** (path only if sharing with an agent)  
- One Item ID per bank  

### Step 5 — API smoke test (agent or user)

Base URL: `https://api.pluggy.ai`

1. `POST /auth` with JSON `{ "clientId", "clientSecret" }` → `apiKey`.  
2. Header: `X-API-KEY: <apiKey>`.  
3. `GET /accounts?itemId=<ITEM_ID>` for each Item — balances should match Overview.  
4. Transactions: **`GET /v2/transactions?accountId=…&dateFrom=YYYY-MM-DD&dateTo=YYYY-MM-DD`** with cursor `next`/`after`.  
   - Legacy `GET /transactions` returns **410 Gone** — do not use it.  
5. Amounts (typical): BANK negative ≈ expense; CREDIT positive ≈ purchase. Prefer `description` over flaky categories; optionally exclude `Same person transfer` and card payments when summarizing spend.

### Optional: after setup

Offer to summarize last month’s spending or list balances once credentials + Item IDs exist — without re-sending the whole tutorial unless they ask.

## Out of scope

- Browser/desktop automation for this skill  
- Bacen registration as a recipient  
- Multi-tenant / other people’s banks on the free path  
- Guaranteeing bank coverage or refresh latency
