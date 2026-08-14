# Blitz AIO - Complete Guide

> NFT Minting Bot for Ethereum, Base, Robinhood Chain & Sepolia (v1.11.30)

> 🇫🇷 Version française : [cliquer ici](#/fr) (may lag behind — this English guide is the reference)

---

## Table of Contents

1. [Installation](#1-installation)
2. [First Launch](#2-first-launch)
3. [RPC Configuration](#3-rpc-configuration)
4. [Wallet Management](#4-wallet-management)
5. [Creating a Mint Task](#5-creating-a-mint-task)
6. [OpenSea Drop Mode](#6-opensea-drop-mode)
7. [Transaction Management](#7-transaction-management)
8. [Force Gas](#8-force-gas)
9. [Distributor & Recollector](#9-distributor--recollector)
10. [Bridge](#10-bridge)
11. [Eligibility Check & Approvals](#11-eligibility-check--approvals)
12. [Settings](#12-settings)
13. [Analytics & Gas Monitor](#13-analytics--gas-monitor)
14. [Privacy Mode](#14-privacy-mode)
15. [Discord Notifications](#15-discord-notifications)
16. [Scheduled Tasks](#16-scheduled-tasks)
17. [Profit Calculator](#17-profit-calculator)
18. [OpenSea Listing Tool](#18-opensea-listing-tool)
19. [Drop Discovery Tool](#19-drop-discovery-tool)
20. [Activity Feed](#20-activity-feed)
21. [Quick Task](#21-quick-task)
22. [Security](#22-security)
23. [Auto-Update](#23-auto-update)
24. [FAQ / Troubleshooting](#24-faq--troubleshooting)

---

## 1. Installation

### Requirements
- Windows 10/11
- Internet connection
- An Ethereum RPC (Alchemy recommended, free)

### Install
1. Get the `Blitz Setup X.X.X.exe` file
2. Run the installer and follow the steps
3. Blitz launches automatically after installation
4. Updates are automatic (banner inside the app)

---

## 2. First Launch

On first launch, Blitz asks you to create a **master password**.

This password:
- Encrypts all your private keys (AES-256-GCM)
- Is required every time you open the app
- CANNOT be recovered if forgotten
- Minimum 6 characters

> **Important**: if you lose this password, your private keys are lost. Keep a copy of your private keys somewhere safe.

### The Dashboard (home page)

After login you land on the **Dashboard** — your at-a-glance overview (redesigned in v1.10.x).

#### Stat cards (responsive, clickable)
| Card | Shows | Click |
|---|---|---|
| **Wallets** | Wallet count + total ETH balance | -> Wallets page |
| **Tasks Running** | Active tasks `/` total | -> Tasks page |
| **Success Rate** | % of successful TXs over full history | -> Analytics |
| **Gas Spent** | Total ETH burned on gas (all time) | -- |
| **Today** | Successful mints today (+ failed count) | -- |

#### Active task banner
If a task is currently executing (`running`/`minting`), a pulsing green banner appears at the top with the task name + current status message. Click -> Tasks page.

#### Recent Transactions
Table of the last 20 TXs with:
- TX hash (click to copy)
- Task name + Wallet name
- **When** (relative timestamp: `2m ago`, `1h ago` - hover = full date)
- Status badge (success / failed / partial)
- **Explorer icon** (network-aware: Etherscan / Basescan / Robinhood Blockscout)
- Filter chips: `all` / `success` / `failed`

#### Gas Trend
Mini chart of mainnet gas over the last 16 minutes (sampled every 12s, persisted in localStorage).
- Current value shown large (bottom-left, accent color)
- Min/max overlay
- Auto-ranged Y axis so even small variations are visible

#### Quick Actions
4 buttons: `+ Task` / `Quick Task` / `Browse Drops` / `+ Wallet`.

#### Last refresh
Small timestamp at the bottom showing the last load time (auto-refreshes on `task-status` events).

---

## 3. RPC Configuration

Blitz needs an RPC to talk to the blockchain. Supported networks: Ethereum Mainnet, Base L2, Robinhood Chain (Arbitrum Orbit L2, chain id 4663), and Sepolia (testnet).

### Get a free RPC (Alchemy)
1. Create an account on [alchemy.com](https://www.alchemy.com)
2. Create an app on **Ethereum Mainnet**
3. Copy the HTTPS URL (e.g. `https://eth-mainnet.g.alchemy.com/v2/your-key`)
4. Optional: also copy the WSS URL for snipe mode

### Add an RPC in Blitz
1. Go to **Settings**
2. Click **Add RPC**
3. Paste the URL
4. Pick the type: **HTTPS** (standard) or **WSS** (for snipe mode)
5. Pick the **network**: Ethereum, Base, or Robinhood Chain
6. Click **Test Connection** to verify
7. Click **Add**

### Default RPCs
- **Base**: `https://mainnet.base.org` (pre-configured)
- **Robinhood Chain**: `https://rpc.mainnet.chain.robinhood.com` (pre-configured)
- **Sepolia**: `https://rpc.sepolia.org` (pre-configured)
- **Ethereum**: needs an external RPC (Alchemy recommended)

### Multiple RPCs
You can add several RPCs per network. Blitz picks the first active RPC for each network.

### Dedicated Drops Scan RPC (optional, v1.11.20)
The **Drops** page scan sends heavy `eth_getLogs` batches that can saturate a free-tier RPC and slow down your mints (both fight for the same RPC). In **Settings > Ethereum RPC**, the **Drops Scan RPC** field lets you point the Drops scan at a separate RPC. Leave it empty and the scan uses the main RPC (default behavior). Recommended if you use the Drops page while tasks are running.

### RPC indicator
The colored dot at the top right of the window shows the connection state:
- Green = connected
- Red = disconnected or no RPC configured

---

## 4. Wallet Management

### Wallet Groups
Wallets are organized in **groups** (e.g. "Main", "Burners", "WL Wallets").
- Click a group in the left panel to select it
- **Create Group**: create a new group
- Deleting a group moves its wallets to "Main"

### Add a Wallet
Click the **+** button in the header, then:
- **Nickname**: name to identify the wallet
- **Private Key**: the wallet's private key (will be encrypted)
- **Group**: group to place the wallet in

### Create Wallets (Batch)
Click the "Create" icon in the header:
- Enter the number of wallets to generate
- Blitz creates random wallets with unique private keys
- Keys are encrypted and stored locally

### Import Wallets (CSV)
Click the "Import" icon in the header:
- Paste the CSV content
- Supported formats:
  - `address,privateKey,nickname` (Blitz/Breeze format)
  - `nickname,privateKey`
  - `privateKey` (one key per line)

### Export Wallets (CSV)
Click the "Export" icon in the header:
- Exports all wallets of the selected group
- Format: `address,privateKey,nickname`
- **Warning**: the exported file contains private keys in plain text!

### Check Balances
Click the "Balance Check" icon in the header to refresh the ETH balances of all wallets.

If **Auto Refresh Balances** is enabled in Settings, balances refresh automatically every 60 seconds.

> Balances shown on the Wallets page are **Ethereum mainnet** balances. The Distributor / Recollector / Bridge tools show balances on whichever chain you select there.

### ENS Resolution
Blitz automatically resolves ENS names (e.g. `vitalik.eth`) for each wallet. If a wallet has an ENS name, it shows in blue next to the nickname in the table. In privacy mode the ENS name is hidden.

### EIP-7702 Delegations (v1.11.21)
Since the Pectra upgrade, an EOA wallet can carry an **EIP-7702 delegation** (attached code, from a "smart account" wallet like MetaMask Smart Account, Ambire, etc.). Mint contracts that require a pure EOA reject those wallets with a `NotEOA` error.

Click the **shield** icon in the Wallets tab header to:
- Scan all wallets on-chain and detect delegated ones (red `7702` badge next to the address).
- Revoke the delegation of selected wallets in one click: Blitz sends a type-4 transaction that re-delegates to the zero address. Once confirmed, the wallet is a pure EOA again.

Each wallet to revoke needs a little ETH for gas. Revoking never costs you access to the wallet.

### Per-Wallet OpenSea API Key (optional, v1.9.15+)

Each wallet can have its **own OpenSea API key**. Useful to bypass the OS rate limit (4 req/s per key).

#### What for?
- When 10 wallets mint a WL drop, each must call the OS API individually (signatures are unique per address)
- With 1 shared key: 4 req/s total = ~2.5s to fetch 10 wallets sequentially
- With 10 unique keys: effectively 40 req/s = ~250ms total

#### It's optional
If you set nothing, the mint engine uses:
1. The task's key (if you set one in the OS Drop form)
2. Otherwise the global key (Settings > OpenSea API Key)

Per-wallet keys are **purely a throughput optimization**. Everything works without them.

#### Set a key on one wallet
Click the key icon (next to Edit/Delete in the wallets table). The icon turns green once a key is set. The modal asks for the key (password input, leave empty to clear).

#### Bulk paste (Wallets toolbar > key icon)
Faster to set N keys at once:
1. Paste one key per line in the textarea
2. Pick the mode:
   - **In order**: each key goes to the matching wallet (1st wallet -> 1st key, etc.). With fewer keys than wallets, the rest are unchanged.
   - **Round-robin**: cycles the keys if you have fewer than wallets (e.g. 3 keys / 12 wallets = 4 wallets per key)
3. Click "Assign Keys"

#### Important note
OpenSea does **not** provide a public endpoint to generate keys programmatically. You must create them manually at [docs.opensea.io](https://docs.opensea.io/reference/api-keys) (~5 keys per OS account).

---

## 5. Creating a Mint Task

### Create a Task
1. Go to **Tasks**
2. Click the **+** button (blue circle)
3. Configure via the 4 tabs:

### Default tab
| Field | Description |
|-------|-------------|
| **Wallet Group** | The wallet group that mints |
| **Function Name** | The contract function name (e.g. `mint`, `publicMint`, `0xa0712d68`) |
| **Wallet(s)** | "All wallets in group" or a specific wallet |
| **Function Parameters** | Function parameters (e.g. `2` to mint 2 NFTs). Exclude the payable amount |
| **Gas Price Method** | Rapid / Custom / Custom+Priority / Manual (see below) |
| **Transaction Cost** | ETH amount to send with the TX (mint price) |
| **Contract Address** | Smart contract address |

> Note: the network (Ethereum/Base/Robinhood/Sepolia) is set by the **task group**, not by the individual task.

#### Gas Price Method in detail (v1.11.14+)

| Method | Behavior |
|---|---|
| **Rapid** | `maxFee = 2 × base fee`, `priority (tip) = base fee`. Aggressive and competitive for a mint; the tip follows the market. |
| **Custom** | You enter a single `Max Fee` (GWEI). The **priority automatically equals the Max Fee** — the effective tip is `maxFee - base fee`, which guarantees inclusion as long as `maxFee > base fee`. |
| **Custom+Priority** | You enter `Max Fee` AND `Priority Fee` separately (full EIP-1559 control). |
| **Manual** | Fixed manual gas limit on top of the gas price. |

> Before v1.11.14, "Custom" without a priority left the tip at the ethers default (~1 gwei), which lost mint races. Fixed: the tip now follows the Max Fee.

### Advanced tab
| Field | Description |
|-------|-------------|
| **Flashbots** | Toggle to send via the Flashbots relay (Ethereum only, private TX) |
| **Schedule Start** | Date/time to auto-start the task (countdown visible in the STATUS column) |
| **Dead Blocks** | Number of blocks to wait after detection before sending (anti-bot protection) |
| **Send on Pending** | Send as soon as the TX is detected pending (faster, riskier) |
| **Spam Mode** | Re-send the TX in a loop (with interval and max retries) |
| **Custom ABI** | Contract ABI JSON (optional, for complex functions) |

### Flip tab (Snipe Mode)
| Field | Description |
|-------|-------------|
| **Flip Mode** | Enable mempool snipe mode |
| **Gas Limit Method** | Auto or Manual |
| **Contract Owner** | Deployer address that will enable the sale |
| **Flip Function** | Name of the function that enables minting (e.g. `flipSaleState`) |
| **Flip Value** | Expected value (`*` = wildcard) |
| **Hybrid Mode** | Auto retry if the snipe fails |

### Task actions
- **Play** (green): start the task
- **Start All**: start every task in the group
- **Clone**: duplicate a task with all its parameters (name + " (copy)")
- **Explorer**: visible once a task finished (success/partial/failed) with a txHash — opens the TX on the group's block explorer
- **Batch Edit**: select multiple tasks (checkboxes); a "Batch Edit (N)" button appears to mass-edit gas/contract

### Start a Task
- Click the green **Play** button next to the task
- Or click **Start All** (green play in the header) to start every task

### Task Groups (network per group)
Organize your tasks into groups (e.g. "Azuki Drop", "Free Mints").
- Click **Create task group** in the left panel
- Pick the group's **network**: **Ethereum** (accent blue), **Base** (blue #0052FF), **Robinhood** (green #00C805), or **Sepolia** (testnet yellow)
- Every task in the group inherits the chosen network
- Groups persist across sessions
- The color badge next to the group name shows the network

Ethereum is the default network — Base/Robinhood are opt-in per group. To mint on Robinhood Chain, create a group with the Robinhood network and add your tasks there.

### Task Presets
Save your frequent task configurations:
- In the creation modal, click **Save Preset** after configuring
- Presets appear as clickable badges that pre-fill the form
- Delete a preset with the X button

---

## 6. OpenSea Drop Mode

For drops that go through OpenSea (most big drops). Rebuilt as a **streaming pipeline** since v1.9.15.

### Setup
1. **OpenSea API Key**: Settings > OpenSea API Key (global key). Optional: per-wallet keys to bypass rate limits.
2. Create a task > **OpenSea Drop** tab

### OpenSea fields
| Field | Description |
|-------|-------------|
| **Collection Slug** | The collection slug (e.g. `pudgypenguins`). URL: `opensea.io/collection/slug` |
| **OpenSea API Key** | Per-task API key (optional; falls back to the global or per-wallet key) |
| **Mint Price Override** | Optional. The price is auto-detected from the contract via `mintPrice()` / `price()` / `cost()` etc. Set it only if auto-detection fails. |
| **Quantity per Wallet** | NFTs to mint per wallet (1-100) |
| **Wallet Group** | The wallet group that mints. Includes `All Wallets (N)` (v1.11.3+) to create a task on every wallet cross-group. |
| **Gas Price Method** | Rapid, Custom, Custom+Priority |

The OS drop endpoint is chain-agnostic (the slug identifies the drop) — for a drop on Base or Robinhood Chain, just put the task in a group with that network.

### How it works (streaming pipeline)

For each wallet, an independent pipeline runs in parallel:
1. **Poll the OS API** `POST /drops/{slug}/mint` (throttled per key at ~4 req/s)
2. **Direct probe** — `estimateGas` on the contract runs in parallel (short-circuits the poll as soon as the contract accepts)
3. As soon as a wallet has its TX data (200) -> the TX is sent **immediately** without waiting for the others
4. Meanwhile, the other wallets keep polling/sending

**No sync barrier**: the fastest wallet sends first, never blocked by the others.

### Direct Mode (always on)
Since v1.9.15, direct mode **always runs alongside the OS API**:
- ABI fetched from Etherscan (automatic, cached 1h) — on Robinhood Chain the ABI comes from Blockscout instead, no API key needed
- Price auto-detected from the contract
- `estimateGas` raced at ~200ms to catch the drop the moment it accepts
- **Public mint**: every wallet armed locally (no API call) -> ~0ms latency
- **WL mint**: detection, then OS API for per-wallet signatures

The old "Direct Mode" toggle was removed (always ON is strictly better).

### Phase 422 - wallet not on WL (fixed in v1.9.15)
Before: if the test wallet (first in the list) was not on the WL, OS returned 422 and the bot polled forever.

Now: 422 = "drop is live, this wallet just isn't eligible" -> switches immediately to per-wallet fetch. WL'd wallets get their 200 and mint, the others fail cleanly.

### Benefits
- No need to know the contract address, ABI, or mint function
- Automatic Merkle proof handling for allowlists
- Automatic stage selection
- Direct mode = full OS API bypass for public mints

### Common errors
| Error | Meaning |
|--------|---------------|
| 409 | The drop is not active yet (not started, ended, or paused) |
| 422 | Drop is live, but this wallet isn't eligible (allowlist, max, etc.) — the other wallets still try |
| 404 | Wrong slug, collection not found |
| 401 | Invalid API key |
| 429 | OS rate limit hit — the bot backs off 2-3s automatically |

### RPC resilience (v1.11.1+)
When several tasks run in parallel on the same contract, the RPC can saturate (Alchemy CU/s 429s, etc.). The bot now handles this properly:

- **JSON-RPC batching disabled** (`batchMaxCount: 1`) on the ethers provider. Before: ethers merged concurrent calls into one HTTP request, and a 429 on the batch crashed **every** in-flight task with "could not coalesce error". Now each request is isolated — a 429 only affects the task that triggered it.
- **Direct prober — exponential backoff** on 429: 200ms -> 400 -> 800 -> 1600 -> 4000ms (cap), then halves back down on success. Slows down instead of hammering.
- **Direct prober — clean exit** once every wallet in the task finished (success or failure). Before: the prober kept running 1h after a successful mint, blocking task finalization and burning RPC budget for the next tasks.
- **Resilient receipt wait**: `txResponse.wait()` can 429 while polling for the receipt even though the TX is already on-chain. The bot falls back to manual `getTransactionReceipt` polling with backoff (1.5s -> 8s) for 5 minutes. No more successful mints marked "failed" because of a 429.
- **Resilient candidate pruning**: at setup, mint-candidate pruning retries 4x with backoff on 429 instead of dropping valid candidates just because the RPC complained.

If you still hit frequent 429s with many parallel tasks: upgrade your RPC plan (Alchemy Growth, etc.) or reduce concurrent tasks on the same contract.

### Multi-key throughput
| Setup | WL fetch throughput |
|---|---|
| 1 global key, 10 wallets | ~4 req/s -> 2.5s for 10 wallets |
| 10 per-wallet keys, 10 wallets | ~40 req/s -> 250ms for 10 wallets |

For WL gas wars, per-wallet keys make the difference.

---

## 7. Transaction Management

### Task statuses
| Status | Description |
|--------|-------------|
| **Waiting** | Task configured, ready to start |
| **Scheduled** | Task scheduled, countdown visible, auto-starts on time |
| **Running** | Task executing |
| **Minting** | TXs sent, waiting for confirmation |
| **Success** | All TXs succeeded |
| **Partial** | Some TXs succeeded, others failed |
| **Failed** | All TXs failed |

### Cancel TX
When a task is in **Minting** status (TX pending in the mempool):
1. Click the **X** (cancel) button in the ACTIONS column
2. Blitz sends an empty TX at the same nonce with +30% gas
3. Miners replace the pending TX with the cancel TX
4. Result: the original TX is cancelled; you only pay the cancel TX's gas

**When to use Cancel TX:**
- You sent a TX with too little gas and it's stuck
- The mint turned out to be a scam
- You changed your mind

### Speed Up TX
When a task is in **Minting** status:
1. Click the **>>** (speed up) button in the ACTIONS column
2. Blitz re-sends the same TX at the same nonce with +50% gas
3. Miners pick the TX with the highest gas
4. Result: your TX confirms faster

**When to use Speed Up:**
- Your TX is stuck (gas too low for current congestion)
- You want to be included in the next block

---

## 8. Force Gas

The **Force Gas** field in the Tasks page header overrides the gas of ALL tasks at once.

### Usage
- Enter a single value: `50` (max fee = 50 GWEI)
- Or an EIP-1559 format: `50/3` (max fee = 50, priority fee = 3 GWEI)
- Click the red **Force Gas** button

Useful when gas spikes during a mint and you want to adjust every task quickly.

---

## 9. Distributor & Recollector

Available from the **Wallets** sub-menu in the sidebar.

Both tools have a **Chain selector** (Ethereum / Base / Robinhood) at the top. Ethereum is the default; pick another chain to move ETH there — balances in the table update to show the selected chain.

### Distributor (1-to-many)
Send ETH from one source wallet to several wallets.
1. Select the **source wallet** (the one holding the ETH)
2. Select the **destination wallets**
3. Enter the **amount per wallet** (in ETH)
4. Click **Distribute**

Use case: funding your mint wallets before a drop.

### Recollector (many-to-1)
Pull the ETH from several wallets back into one wallet.
1. Select the **destination wallet**
2. Select the **source wallets**
3. Click **Recollect**

Each source wallet sends `balance - gasCost` to the destination wallet.

Use case: after a mint, sweep the leftover ETH from your burners.

### Mass Transfer (NFTs)
Also in the Wallets sub-menu: transfer ERC-721/ERC-1155 tokens from your wallets to a destination address, with the same chain selector (Ethereum / Base / Robinhood). Token IDs are assigned to wallets in order.

---

## 10. Bridge

**Wallets > Bridge** in the sidebar. Bridge ETH between Ethereum, Base and Robinhood Chain directly from your Blitz wallets, powered by [Relay](https://relay.link).

### How to use it
1. Pick the **From** and **To** chains (⇄ button swaps the direction)
2. Enter the **amount per wallet** (ETH)
3. Select the wallets to bridge from — balances shown are on the origin chain
4. A **quote preview** appears automatically: estimated amount received, ETA (usually a few seconds), and fees (% and $)
5. Click **Bridge from N Wallet(s)** and confirm

### What happens
- Each wallet gets its own fresh Relay quote and sends a single **deposit transaction** on the origin chain
- The Relay network fills on the destination chain, to the **same address** — usually within seconds
- Wallets run in parallel; the table shows the live status per wallet (Getting quote… → Sending… → Confirming… → Relaying… → Bridged)
- Results include the deposit TX hash with an explorer link

### Good to know
- Native ETH only (no ERC-20 bridging yet)
- Fees are Relay's (~1% on small amounts, less on bigger ones) — the preview shows the exact figure before you confirm
- If a fill fails or is refunded, the status says so explicitly (Failed / Refunded / Delayed) with the deposit hash so you can follow up on relay.link. Your ETH is never silently lost.
- Start with a small test amount (e.g. 0.001 ETH) the first time

---

## 11. Eligibility Check & Approvals

### Eligibility Check (Tools > Eligibility) — multi-phase + token-gate pre-check (v1.11.5+)
Check whether your wallets are eligible for **every** phase of an OpenSea drop, **before** the phase starts when possible.

1. Enter the **Drop Slug** (e.g. `the-florentines`)
2. Enter your OpenSea **API Key**
3. Pick a **Wallet Group** (incl. `All Wallets` for cross-group) or individual wallets
4. Click **Check**

#### How it works (v1.11.5)
1. **Stage enumeration** via `GET /api/v2/drops/{slug}` — grabs uuid, label ("FCFS: BAYC, Chimpers, …"), price, start/end, max/wallet for each phase. Source of truth for order, names and timing.
2. **Active phase** -> probes `POST /drops/{slug}/mint` per wallet -> true 200 (eligible) / 422 (not eligible). OS-side truth.
3. **Token-gated stages** (FCFS-style WL with a collection list):
   - **On-chain first**: reads SeaDrop `getEnumeratedAllowedTokens` + `getTokenGatedDrop`, matches stages by `start_time` (+-60s), checks `balanceOf` per wallet.
   - **Heuristic fallback**: if on-chain returns nothing, parses the label "FCFS: name1, name2, …", resolves each name via `/collections/{slug}` then `/search?query=…`, checks `balanceOf` -> cell marked `(heuristic)`.
4. **Future public phase** -> derived ✓ Eligible (pending phase) — open to everyone at opening.
5. **Signed-mint WL without a list in the label** (e.g. personal GTD) -> ⌛ "Check when live" (the public OS API cannot pre-check it; the endpoint the OS frontend uses is undocumented).

Once `stage_uuid` is rejected by OS (`400 "Unknown field"` confirmed on all variants), the module automatically switches to `os-api-single` mode: a single probe per wallet for the active phase, derived statuses for the rest. Visible in the banner ("Source: os-api-single · stage_uuid ignored").

#### Streaming progress (v1.11.5)
- No cache: every re-check is fresh.
- `done/total` progress bar over the cells during the scan.
- Cells fill in live (pulsing "...Probing" dot -> filled as results arrive).
- Main-process logs (`npm run dev` terminal):
  - `[eligibility] on-chain: found N token-gated stage(s) for SLUG with M gate contract(s)`
  - `[eligibility] resolved "NAME" -> SLUG -> 0x...`
  - `[eligibility] label-heuristic: phase "..." -> 7/12 gates resolved`

#### Reading the results
- **Source banner**:
  - Green "Drop is LIVE - current phase: X" = drop active, true per-wallet status for the active phase
  - Blue "Drop configured but not currently active" = drop not started, derived statuses
  - Red "Drop not found" = wrong slug
- **Phase strip** at the top: one card per stage with type badge, Live/Upcoming/Ended status, price, max/wallet, relative timing.
- **Stats**: Checked / Any Phase / On Allowlist / Public Only / Not Eligible
- **Wallet x phase matrix**: ✓ (eligible) / ✓ pending (eligible once the phase opens) / ✕ (not eligible) / ⌛ (checkable when live) / ◔ (not started) / – (ended) / ? (unknown). The secondary text explains why ("Holds 1 gate NFT(s): 0x8fe1...9cd8 (heuristic)" for example).

Great for: seeing before the drop which wallets are on the FCFS via NFT holdings (BAYC, Mfers, FWOG, etc.), knowing which one to use for the WL and which to keep for public, validating that the API key works for the drop.

#### Known limitations (v1.11.5)
- Personal signed-mint WLs (a "GTD" with no list in the label) cannot be pre-checked — the OS frontend endpoint hasn't been identified yet (DevTools Network inspection required).
- The label-heuristic fallback resolves ~60% of names on Florentines FCFS (7/12) — names that don't match a direct OS slug (Chimpers, Mundos, Fauvtoshi, Fugz, GVC) stay unresolved. Temporary workaround: if you know a wallet is WL'd via one of those collections, trust the mint over the pre-check.
- ~2-3s latency per token-gated stage resolved via labels (12 names x 200ms sequential to stay under the OS 4 req/s rate limit).

### Approvals (Tools > Approvals)
Grant `setApprovalForAll` to an operator (OpenSea Seaport, Blur) for several wallets in batch.

1. Enter the NFT collection's **Contract Address**
2. Pick the **operator** (Seaport 1.6 or Blur) or enter a custom address
3. Select the wallets
4. Click **Approve**

---

## 12. Settings

| Setting | Description |
|---------|-------------|
| **Ethereum RPC** | List of configured RPCs. Add/remove/test |
| **OpenSea API Key** | Global API key for OpenSea Drop mode |
| **Etherscan API Key** | API key for the Profit Calculator (scans TX history) |
| **Discord Webhook** | Discord webhook URL for task notifications (success/fail) |
| **Default Gas Strategy** | Default Max Fee and Priority Fee for new tasks |
| **Sound Notifications** | Play a sound on successful mint (on by default) |
| **Auto Refresh Balances** | Auto-refreshes wallet balances (every 60s) |
| **Quick Task Settings** | Default wallet group, wallets, gas method, auto-start. If gas method = Custom/Custom+Priority, **Max Fee / Priority Fee** fields appear and are applied to Quick tasks (v1.11.17) |
| **Export/Import Config** | JSON export (without private keys) / import with merge |
| **Logs** | Open Log Folder button for the log files |

---

## 13. Analytics & Gas Monitor

### Analytics
- **Total Mints**: total TXs sent
- **Success Rate**: percentage of successful TXs
- **Total Spent**: estimated gas spent
- **Avg Cost**: average cost per TX
- **7-day chart**: mint activity over the last week

### Gas Monitor
- **Gas Cards**: low/current/high prices in GWEI
- **30-block history**: gas price chart over the last 30 blocks
- **EIP-1559**: current Base Fee, Max Fee, Priority Fee
- **Gas Estimator**: compute the ETH cost from custom gwei, wallet count, gas used
- Auto-refresh every 15 seconds

---

## 14. Privacy Mode

The **eye** button in the top bar toggles privacy mode.

### What gets hidden
- **Wallets**: addresses (only the first 6 characters stay visible), balances, ENS names
- **Tasks**: contract addresses
- **Wallet Groups**: total balances in the left panel

### Usage
- Click the **eye** icon in the Topbar (next to the clock)
- The icon turns blue/accent while privacy mode is on
- Click again to disable

Useful for screen sharing or screenshots without exposing your addresses and balances.

---

## 15. Discord Notifications

Get a Discord notification when a task finishes (success, failed, or partial).

### Setup
1. Create a **Discord webhook** in your server (Channel settings > Integrations > Webhooks)
2. Copy the webhook URL
3. Go to **Settings** > **Discord Webhook**
4. Paste the URL and save

### Notification format
- **Embed** titled "Blitz - Task Success/Failed/Partial"
- Color: green (success), yellow (partial), red (failed)
- Fields: Task ID, Status, Details (error message), TX Hash
- Automatic timestamp

Only final statuses are sent (not intermediate ones like "running" or "minting").

---

## 16. Scheduled Tasks

Schedule a task to start automatically at a precise time.

### Setup
1. Create or edit a task
2. Go to the **Advanced** tab
3. Fill the **Drop Time** field with the mint date and time
4. Optional: set **Start Before** (in seconds) to start the task X seconds before the drop
5. Create the task

### Behavior
- The task is created with the **Scheduled** status
- A **countdown** shows in the STATUS column (e.g. "2h 15m 30s (-5s)")
- The countdown updates every second
- The `-Xs` shows the configured "Start Before" offset
- When the timer reaches 0 (minus the offset), the task starts automatically
- If the app was closed and reopened past the scheduled time, the task starts immediately

### Start Before
The **Start Before** field starts the task a few seconds before the drop time. Useful to have the TX in the mempool at the exact mint moment.
- Default: 5 seconds
- Example: drop at 18:00:00, Start Before = 5s → task starts at 17:59:55

Great for mints that open at a precise time (e.g. 6pm UTC). Configure everything ahead and let Blitz fire at the right moment.

---

## 17. Profit Calculator

Compute your profit and loss (PnL) on NFT and ERC-20/memecoin trades, and generate a shareable success card.

### Open the Profit Calculator
**Tools > Profit Calculator** in the sidebar.

### Requirements
- **Etherscan API Key**: go to **Settings** > **Etherscan API Key** and enter your key
  - Get a free key at [etherscan.io/apis](https://etherscan.io/apis)
  - Limit: 5 requests/second on the free tier

### Usage
1. Select the **wallets** to scan (by group or individually)
2. Enter the token/NFT **contract address**
3. Pick the type: **NFT** (ERC-721) or **ERC-20** (coins/memecoins)
4. Click **Calculate**

### Displayed metrics

| Metric | Description |
|----------|-------------|
| **Invested** | Total ETH spent buying |
| **Secured** | Total ETH received from sales |
| **Realized P/L** | Realized profit/loss (Secured - Invested for the sold portion) |
| **Holding** | Estimated value of tokens/NFTs still held (OpenSea floor for NFTs, DexScreener for coins) |
| **Unrealized P/L** | Unrealized profit/loss (holding value - remaining acquisition cost) |
| **Total P/L** | Realized + Unrealized |
| **ROI %** | Return on investment as a percentage |

### OPEN vs CLOSED position
- **OPEN** (blue): you still hold tokens/NFTs of this collection
- **CLOSED** (gray): everything sold, position closed

### Success Card
After the calculation, a **success card** is generated automatically:
- 800x600px image with your PnL stats
- `$SYMBOL` format for coins, collection name for NFTs
- OPEN/CLOSED badge, ROI %, green/red profit pill

### Custom background
- Click **Change BG** to pick a custom background image
- The image is **saved automatically** (persisted via localStorage)
- Click **Reset BG** to restore the default background
- No need to re-upload each session

### Download the card
Click **Download** to save the success card as a PNG.

### NFT detection
- Blitz scans ERC-721 Transfer events via the Etherscan API
- Detects buys/sells in native ETH AND WETH (OpenSea sales via Seaport)
- Floor price via the OpenSea V2 API

### ERC-20/memecoin detection
- Blitz scans ERC-20 token transfers
- Detects sells via internal transactions (ETH received)
- Current price via the DexScreener API
- Micro-cap friendly display (subscript notation for very small prices)

> Note: PnL currently covers Ethereum mainnet (Etherscan V2 free tier).

---

## 18. OpenSea Listing Tool

List and delist your NFTs directly on OpenSea from Blitz. Works on **Ethereum, Base and Robinhood Chain** (chain selector at the top of the page, v1.11.27+).

### Open the Listing Tool
**Tools > Listing** in the sidebar.

### Requirements
- **OpenSea API Key**: configured in Settings or in the global field
- **RPC** for the selected chain: for approvals and delisting (on-chain transactions)

### List NFTs

1. Pick the **chain**, enter the collection **Contract Address**, and click **Load**
2. The collection loads with its name, slug, OS Fee (%) and Royalty (%)
3. Select a **Wallet Group** (default: `All Wallets (N)` since v1.11.3, cross-group scan) and click **Scan NFTs**
4. Your wallets' NFTs appear in the grid
5. Select the NFTs to list (click them, or All/None)
6. Configure in the right panel:

| Field | Description |
|-------|-------------|
| **Price per NFT** | Sale price in ETH |
| **Ladder Listing** | Enables incremental pricing (each NFT costs more than the previous) |
| **Increment** | Price increase per NFT (e.g. 0.01 ETH) |
| **Duration** | Listing duration (1h to 1 month) |
| **Royalties** | Toggle on/off (grayed if enforced on-chain) |

7. The **summary** shows: item count, total, OS fee, royalties, and **You receive** (net total)
8. Click **List X NFTs**

### Already-listed NFTs
- On scan, Blitz automatically detects NFTs already listed on OpenSea
- They appear **first** in the grid with a **green badge** showing the price
- The label under the token ID reads "Listed" in green

### Repricing without delisting (v1.11.28)
Listing an already-listed NFT at a new price does **not** require a delist first:
- **Price decrease**: pure gasless re-signature (`X → Y ETH (gasless)`, no transaction)
- **Price increase**: on-chain cancel of the old order first, then the new signature

### Open an NFT on OpenSea (v1.11.18)
Each grid tile has a small **OpenSea logo button** at the bottom right. Click -> opens the NFT's OpenSea page in your browser. Clicking the logo doesn't toggle the card selection.

### Delist NFTs

1. Select the listed NFTs (green badge)
2. The red **Delist X NFTs** button appears
3. Click to send an on-chain cancel transaction (Seaport)
4. The green badge disappears after the delist

### Automatic approval
Blitz checks the Seaport Conduit approval before listing. If a wallet hasn't approved the collection, the approval is sent automatically before listing. The conduit is chain-specific — Blitz uses the right one per chain automatically.

### Ladder Listing
Ladder listing lists several NFTs at incremental prices:
- **Start Price**: price of the first NFT
- **Increment**: increase per following NFT
- Example: Start 0.05, Increment 0.01 → 0.05, 0.06, 0.07, 0.08...
- The range preview shows under the increment field

### Wallet chips
If the group holds several wallets, **chips** appear to filter by wallet. Click a chip to scan only that wallet.

---

## 19. Drop Discovery Tool

Available from Sidebar > **Tools > Drop** (v1.9.15+).

The tool combines **3 discovery sources** to find live and upcoming OpenSea drops, with an OS-only filter that kills false detections (Uniswap V4 NFT positions, ENS, etc.).

### The 3 sources

| Source | Finds | Coverage |
|---|---|---|
| On-chain mint scan | Drops **currently minting** (Transfer events from `0x0`) | ~100% of live drops |
| SeaDrop monitor | **Upcoming** drops (configured but not started) | ~70-80% of OS drops |
| OS Featured | Drops featured by OS | ~3 gated drops |

Combined: **~95% coverage** of public OS drops.

### OS-only filter (kill false positives)
Every candidate contract is verified via `POST /api/v2/drops/{slug}/mint` with a sentinel:
- `200` -> live drop
- `422` -> live drop with a restrictive phase (allowlist)
- `409` -> configured drop, not started
- `404` -> not an OS drop -> **excluded** from the feed

That's why you no longer see "Uniswap V4 Positions NFT" or other NFT spam in the feed.

### UI - Tabs

**Drop Feed**: the grid of detected drops
- Header filters: All / Minting / Upcoming / OS Featured / Hidden
- Scan range selector: ~40min / ~1.7h / ~3.5h / ~7h (on-chain blocks scanned)
- Scan button for manual refresh (auto-refresh every 2min otherwise)
- Source status at the bottom: colored dots showing each source's health

**Lookup**: check a specific drop by slug
- Type an OS slug (e.g. `pudgypenguins`)
- Click "Check Drop"
- See the status: Live / Not started / No drop page / Active (restricted)

### Card badges

| Badge | Meaning |
|---|---|
| `OS` (orange) | Drop featured by OpenSea (`/api/v2/drops`) |
| `OS ✓` (green) | Drop verified via the mint endpoint probe (= legit OS drop) |
| `SeaDrop` (purple) | Caught by the on-chain SeaDrop event monitor (= upcoming drop config detected early) |
| `Minting` (pulsing green) | Phase active right now |
| `Upcoming` (purple) | Configured, not started |

### Progress bar
Read straight from the on-chain contract:
- **Current minted**: `totalSupply()` / `totalMinted()` / etc.
- **Max supply**: `maxSupply()` / `MAX_SUPPLY` / etc.
- Color code: green <70%, orange 70-90%, **red >=90%** (bold)
- If either value isn't available on-chain, **no bar** (better than a lying 100%)

### Smart sorting
1. Featured drops on top
2. Live drops by mint progress DESC (closest to sold-out rise up)
3. Upcoming drops by startTime ASC (soonest first)
4. The rest by recent TX count

### Hide / Unhide
**✕** button overlaid top-right on each card. Click -> the card slides into the Hidden section.
- The "Hidden (N)" filter appears in the bar once there are hidden drops
- Inside Hidden, the button becomes **↩** for individual unhide
- "Unhide all" button to reset
- **Persists across restarts**: the contract stays blacklisted after a restart

### Background alerts & watchlist (v1.11.28)
- The last scan is remembered: the page shows instantly on mount (snapshot), and a background watcher re-scans every 5 minutes when enabled (**alerts toggle on the Drop page itself**)
- **New drop alerts**: a toast (and optional Discord webhook) fires when a brand-new contract shows up
- **Watchlist**: star a drop to get a T-5min alert before its start time
- **Create Task prefill**: the drop's start time pre-fills the task's Drop Time field (ignored if already past)

### + Create Task (bridge to Tasks)
Button on every card (accent-highlighted if the drop is live, muted otherwise):
1. Click -> Blitz navigates to the Tasks page automatically
2. The creation modal opens, pre-filled with:
   - `mode: opensea`
   - `openseaSlug: <drop slug>`
   - `contract: <contract address>`
   - `name: Mint <collection name>`
3. You finish up (wallet group, gas, schedule) and confirm

Full workflow: discovery -> task creation in 2 clicks.

### SeaDrop monitor in detail
The monitor scans `PublicDropUpdated` events on the SeaDrop 1.0 contract (`0x00005EA00Ac477B1030CE78506496e8C2dE24bf5`). These events contain:
- the drop's `startTime` / `endTime`
- `mintPrice` (in wei)
- `maxTotalMintableByWallet`
- `feeBps`

So as soon as a project **configures** its drop on SeaDrop (often hours/days before the start), Blitz shows it in the Upcoming section with a countdown.

**Limit**: ~70-80% of public OS drops go through SeaDrop. The remaining 20-30% (custom contracts, the new OS Studio system) appear in the feed once they start minting (via the on-chain scan) with a few blocks of latency.

---

## 20. Activity Feed

**Feed** tab (v1.11.25+): the activity of all your wallets aggregated into a single date-sorted stream. Listings, sales, buys, mints, offers, NFT transfers and token swaps.

### Run a scan

Refresh is **manual** (each wallet x chain costs API requests). Before clicking **Refresh**, set the scope:
- **Wallet group** to scan (or All Wallets)
- **Chains**: Ethereum / Base checkboxes
- **History** (dropdown 7 / 30 / 90 days, default 30): lookback depth applied only on a wallet's **first** scan

A progress bar shows `done/total` during the scan. The scope you set is remembered between sessions (v1.11.28).

### Sliding window

A wallet's first scan goes back as far as the chosen depth. Subsequent scans **resume where the previous one stopped** — history is only paid once per wallet; later refreshes are fast and only fetch what's new. Events accumulate in a local cache (500 cap) instead of restarting from scratch.

If the **"partial history — run Refresh again"** message appears, a results page was full: just hit Refresh again to fetch the rest.

The **Clear** button wipes the cache and cursors: use it to force a full rescan from zero.

### Event types

| Badge | Meaning |
|---|---|
| `Listed` | NFT put on sale |
| `Sold` / `Bought` | NFT sale / purchase |
| `Mint` | Mint received |
| `Offer in` / `Offer out` | Offer received / made |
| `Cancelled` | Order cancelled |
| `NFT in` / `NFT out` | Incoming / outgoing NFT transfer |
| `Swap` | Token swap (e.g. `1.5 ETH -> 4200 USDC`) |
| `Token in` / `Token out` | Token transfer without counterpart |

**All / NFT / Tokens** tabs to filter, plus a search by collection, token or wallet. Every row links to OpenSea or Etherscan.

### Where the data comes from

Two complementary sources, scanned in parallel:
- **OpenSea** for everything NFT. It's the **only** possible source for listings: Seaport orders are off-chain, they exist in no block.
- **Etherscan** for ERC-20 swaps, which OpenSea doesn't see at all.

**Anti-spam filter**: a token arriving with no counterpart, in a TX you didn't initiate, is an unsolicited airdrop — it's cut before reaching the feed. Your real buys pass, since they're always initiated by your wallet.

**Limit**: the free Etherscan V2 tier only covers Ethereum. On Base, the NFT side (OpenSea) works, but token swaps don't show up.

---

## 21. Quick Task

Available via **Ctrl+Shift+Q** or the Quick icon in the Tasks header. Creates a task from an existing TX (explorer URL or hash).

### Prerequisite configuration (Settings > Quick Task)
- **Default Wallet Group**: group to use
- **Default Wallets**: specific wallets in that group (check them in the multi-select)
- **Default Gas Method**: rapid / custom / customPriority. With custom/customPriority, **Max Fee / Priority Fee** fields appear and are actually applied to created tasks (fixed in v1.11.17 — before, that setting was ignored).
- **Auto Start**: toggle to start the task immediately after creation

### Usage
1. Copy a TX hash OR an Etherscan/Basescan/Blockscout URL of a mint
2. Open Quick Task (Ctrl+Shift+Q)
3. Paste in the field — **validation runs automatically** (no more "Parse TX" button since v1.11.17)

Blitz then:
1. **Auto-detects the chain** (tries mainnet / base / robinhood / sepolia)
2. **Fetches the contract ABI** — Etherscan V2 for mainnet/base/sepolia (needs your Etherscan key), Blockscout for Robinhood Chain (no key needed) — for clean argument decoding
3. **Shows a preview**: contract, full function signature, decoded args, value, from, detected chain, decode quality (green/orange/red badges)

### Quality badges

| Badge | Meaning |
|---|---|
| `ABI decoded` (green) | Contract verified on the explorer, full decode |
| `Known selector` (orange) | Selector matches a hardcoded list (mint/claim/etc.), partial decode |
| `Raw selector only` (red) | Unverified contract, raw hex fallback — the bot will send the raw calldata |

In every case the task can be created. Raw hex selectors are supported (`buildTransaction` encodes via `AbiCoder.defaultAbiCoder`).

### Creation
Click "Create Task":
- 1 task created **per wallet selected in Settings** (matches the standard "1 task = 1 wallet" pattern)
- Tasks go to the currently active task group (never orphaned)
- `network` = the TX's detected chain (not the active group's)
- If `autoStart: ON`, the tasks start immediately

Confirmation toast: "Quick task created for N wallet(s) in 'Group X'".

### Use cases
- You see a degen mint a new drop live — paste their TX -> Blitz creates 10 identical tasks with your wallets
- You want to re-mint exactly the same function as a historical TX
- You're testing a custom function: paste the TX, the bot encodes your args cleanly

---

## 22. Security

### Encryption
- All private keys are encrypted with **AES-256-GCM**
- The encryption key is derived from the master password via **PBKDF2** (600,000 iterations, SHA-512)
- The master password is NEVER stored — only its hash is kept
- Constant-time hash comparison (timing-safe) to prevent timing attacks

### Local storage
- Data is stored locally in `electron-store` (encrypted JSON file)
- No data is sent to any external server
- Private keys never leave your machine (except to sign the TXs sent to the RPC)

### Best practices
- Use a strong, unique master password
- NEVER share your data file (`blitz-data.json`)
- Keep a copy of your private keys somewhere safe
- Use dedicated wallets for minting (not your main wallet)
- Test on Sepolia (testnet) first before Mainnet

---

## 23. Auto-Update

Blitz updates itself automatically when a new version is available.

### How it works
1. On launch, the app checks for a new version on the server (after 10s)
2. If an update is found, a **blue banner** appears under the top bar: "Update vX.X.X available"
3. Click **Download** to download the update in the background
4. Once downloaded: "Update ready - restart to install"
5. Click **Restart Now** to install and relaunch the app
6. The banner can be dismissed with the **x** button if you want to update later

### Manual check
Updates are automatic, and also checked on every app launch.

---

## 24. FAQ / Troubleshooting

### "No RPC configured"
You haven't added an RPC, or no RPC is active. Go to Settings > Add RPC.

### "Master password not set"
The app isn't unlocked. Restart and enter your password.

### My TX is stuck (pending for a long time)
Gas is probably too low. Use **Speed Up** to re-send with +50% gas, or **Cancel** to abort.

### "Wallet not in allowlist" (OpenSea)
Your wallet isn't on the drop's whitelist. Check with the project that your address is on the list.

### "Drop is not currently active" (OpenSea)
The mint hasn't started yet or has already ended. Wait for the start time or check the collection's OpenSea page.

### "Contract not live after 5 minutes" / the task stays on "Waiting for contract to go live"
In manual mode, Blitz loops `estimateGas` until the contract accepts the mint. While it reverts, this message shows. Possible causes:
- **The drop isn't open yet** -> waiting is normal.
- **Permanent error** (wallet already minted, sold out, max reached) -> since v1.11.13 Blitz **decodes the revert** and fails immediately with the real reason (e.g. "Cannot mint: this wallet has already minted") instead of waiting 5 minutes.
- **Wrong mint price** -> the Transaction Cost field must exactly match the contract's price.
- **Saturated RPC** (Alchemy free tier) -> the message becomes "RPC slow - estimateGas timed out". Use a paid RPC or wait for the load to drop.

Details (revert code, attempt count) are written to `blitz.log` (Settings > Open Log Folder) since v1.11.12+.

### A task shows "Failed" but the mint succeeded on-chain
Bug fixed in v1.11.16 (`provider is not defined`): the mint went out and succeeded, but receipt tracking crashed and wrongly marked the task "Failed". Update to v1.11.16+. If you still see a false "Failed", check the tx hash on Etherscan before re-minting.

### Windows or my antivirus flags Blitz as a virus

It's a false positive, but an expected one — here's why, honestly.

Blitz is not **code-signed** (a code-signing certificate costs several hundred euros a year and requires a legal entity). Without a signature, Windows shows "Unknown publisher" and antiviruses have no reputation to lean on: every new version is one more unknown binary.

On top of that, the app's behavior looks a lot like a key stealer when viewed without context: it encrypts and stores private keys, contacts many endpoints, and downloads then executes a new binary on every update. Heuristic engines trigger on that.

**Install despite the warning:**
1. SmartScreen -> "More info" -> "Run anyway"
2. If Defender quarantines the file: Windows Security > Virus protection > History > restore the file, or add an exclusion on the install folder

**Verify your file is the right one**: every release publishes the installer's SHA-256. Compare it before running:
```powershell
Get-FileHash "Blitz-Setup-X.X.X.exe" -Algorithm SHA256
```
If the hash doesn't match the release's, **don't run the file** — download it again from the official releases page.

> Don't disable your antivirus entirely. A targeted exclusion on the Blitz folder is enough.

### The app won't start
- Run as administrator if needed
- Check that no antivirus is blocking the app (see the previous question)
- Reinstall from the latest Setup .exe

### Balances don't show
- Check that your RPC works (test in Settings)
- Click the Balance Check button on the Wallets page
- Enable Auto Refresh Balances in Settings

### How do I find a mint's parameters?
1. Go to **Etherscan** > contract address > **Contract** tab > **Read Contract**
2. Look for the mint functions (`mint`, `publicMint`, `claim`, etc.)
3. Check the price in `mintPrice` or `PRICE` (in wei; divide by 10^18 for ETH)
4. The max per wallet is often in `maxMintPerWallet` or `MAX_PER_TX`
5. For the ABI: Contract tab > ABI, copy the JSON

### How do I find the OpenSea slug?
The collection URL is `opensea.io/collection/slug`. The slug is the last part of the URL (e.g. `pudgypenguins`, `azuki`, `boredapeyachtclub`).

### Keyboard shortcuts

| Shortcut | Action |
|-----------|--------|
| Ctrl+Shift+S | Start All Tasks |
| Ctrl+Shift+X | Stop All Tasks |
| Ctrl+Shift+Q | Quick Task |
| Ctrl+1 | Dashboard |
| Ctrl+2 | Tasks |
| Ctrl+3 | Wallets |
| Ctrl+4 | Settings |
