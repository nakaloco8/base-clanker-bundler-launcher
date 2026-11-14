<!-- aeonlauncher:header -->
<p align="center"><img src="./.github/social.png" alt="Aeon Launcher · Base" width="100%"></p>

<p align="center"><a href="https://api.trenchy.tools/api/create-api-key">Get an API key</a> · <a href="https://api.trenchy.tools/AGENTS.md">API rules</a> · <a href="https://api.trenchy.tools/openapi.json">OpenAPI</a></p>

# Aeon Launcher · Base

Aeon Launcher for launching on **Clanker V5** (Base, chain id 8453, native ETH) with the
[launch API](https://api.trenchy.tools/AGENTS.md). One Next.js app, one process, one `npm run dev`. The same app as
the pump.fun and pons starters: the same pages, the same launch flow, a different `lib/chain.ts`.

Creates go to **Clanker V5** (a tax hook on a Uniswap V4 pool paired with ETH; 1 % buy and sell tax by default, the creator's share is claimable from Clanker's fee escrow with **Close out → Claim**). Clanker charges an anti-snipe fee on buys in the first **20 seconds** after a launch (99 % falling to the normal tax), so a launch waits those 20 s before the bundle, snipers, later buyers and the volume maker start.

## What it does

- **Launch**: name, symbol, image (or **✨ Create with AI**), socials; who buys at launch (the main wallet, another wallet
  or a new one; first buyers, who buy right after the create (not in the same block); later buyers spread over a window; a volume maker in Calm / Normal / Wild); what happens
  after (auto-sell on outside money or a timer, the main wallet with the buyers, later or on its own trigger; auto-claim
  of the creator fees). A cost panel says what leaves the main wallet before anything is signed; a test run builds and
  simulates the create; the live log shows every step and hash.
- **Trading**: the chart, the live trades (ours marked), profit / loss from the chain (gas and fees in it), every wallet
  with Buy and 25 / 50 / 100 % sell, **Sell all at the same instant**, timed sells, and **Close out** (sell what is left,
  claim the creator fees, collect every wallet back to the main wallet, archive the run's wallets).
- **Wallets**: one list, each wallet a file in `./wallets`; fund, collect, send, export, archive.

The app holds your keys and signs locally with [viem](https://viem.sh). It never talks to an RPC node or IPFS: the API
builds every transaction (`POST /api/base/trade-local`), tells the signer what the chain knows (`GET /api/base/account`),
broadcasts (`POST /api/base/send`) and confirms (`GET /api/base/txs`). Live trades and the P/L come from
`GET /api/base/trades` and `GET /api/base/pnl`. Every trade pays the API's **0.5 % fee** as a small ETH transfer
that comes with it; nothing else is charged.

## 5-minute start

```bash
npm install
cp .env.example .env
curl "https://api.trenchy.tools/api/create-api-key?label=base-launcher"   # → { "apiKey": "…" }, shown once
# paste the key into .env as LAUNCH_API_KEY (or later in the app's Settings sheet)
npm run dev
```

Open http://localhost:4104. Create the main wallet at the top of the Launch page (its key file is written to `./wallets`
first, inside the clone, git-ignored) and send it 0.005 ETH on Base. Start small (dev buy chips: 0.001–0.1 ETH) and press
**Test run** before the first launch.

## Where your keys are

`./wallets` (or `WALLET_BACKUP_DIR`): one JSON file per wallet, `{ publicKey, privateKey, chain: "base", role, label }`,
plus `wallets.csv`. The folder can be shared with the other starters: each one lists only its own chain's files. A
wallet is written to its file **before** it is shown, funded or used, and nothing funds a wallet whose file is missing.
Archived wallets move to `archived/` (keys kept).

## Run it in a browser only

`NEXT_PUBLIC_DEMO=1 npm run dev`: the same pages, answered inside the tab (keys in the browser's storage, a backup file
downloads the moment a wallet is made). Nothing reaches this app's server.

## Change it

- `lib/chain.ts` is the chain: names, logos, explorer, amounts, fees, gas buffers, the API prefix and launchpad.
- `lib/launch-api.ts` is the API client (one method per endpoint); `lib/core/*` is the launch executor, trading and funding,
  shared by local and browser mode.
- Every endpoint: [openapi.json](https://api.trenchy.tools/openapi.json).

MIT licensed.

## Screenshots

Launch page:

![launch](.github/shot-launch.png)

Wallets page:

![wallets](.github/shot-wallets.png)

Trading page:

![trading](.github/shot-trading.png)
