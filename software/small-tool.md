# Sample 3: Small tool, Wallet Health Card

**Service:** Small tool or script, $49, 72h · **Repo:** [astratc/wallet-health-card](https://github.com/astratc/wallet-health-card) (MIT) · **Live demo:** [astratc.github.io/wallet-health-card](https://astratc.github.io/wallet-health-card/) · **Video:** [demo.mp4](https://astratc.github.io/wallet-health-card/demo.mp4)

## What it does
Paste any EVM address and get a security report card in seconds. It scans Ethereum, Optimism, Arbitrum One, Gnosis and Scroll through the Blockscout PRO API (with opt-in Base, Polygon and ZKsync on a paid key) and reports:
- **Open token approvals**, decoded from on-chain calls, with the spender's name and verification status, flagging unlimited approvals to unverified contracts.
- **Address-poisoning attacks**: look-alike addresses that send you dust so you copy the wrong address. It raises a high alert if you ever paid one.
- **Spam and bait tokens**, using Blockscout token reputation plus bait-name heuristics.
- A **score and A–F grade**, with every finding linked to the transaction in Blockscout Explorer and a revoke link.

It's read-only: no wallet connect, no signatures, and the API key never leaves the browser.

## How it's built
| Part | Lines | Notes |
|---|---|---|
| `docs/core.js` | ~180 | Shared scanner: API client (3 concurrent requests, 429 retry, 20s timeout, 402 becomes "paid chain skipped"), approval and poisoning logic, scoring |
| `docs/app.js` + `index.html` | ~55 | Static web UI on GitHub Pages, with a recorded sample scan so it works without a key |
| `cli/health.mjs` | 16 | `node cli/health.mjs 0x… [--all] [--json] [--out r.json]` |
| `test/run.mjs` | ~50 | Offline tests against a mocked API, including the poisoning regression test from [Sample 2](bug-fix.md) |
| `proxy/worker.js` | 19 | Cloudflare Worker CORS proxy: GET-only, uses the caller's own key, stores and logs nothing |

No dependencies, Node 18+ for the CLI. Under 300 lines of logic, with tests and a README, which is the scope of this service.

## Try it
```
git clone https://github.com/astratc/wallet-health-card && cd wallet-health-card
node test/run.mjs                                   # offline, no key needed
BLOCKSCOUT_API_KEY=proapi_xxx node cli/health.mjs 0xYourAddress
```
Or open the [live demo](https://astratc.github.io/wallet-health-card/) and press **Sample scan**.

## README quality check
We hold our own README to the same checklist we use for client tools.

| Check | Status | Note |
|---|---|---|
| States the problem and what the tool does in the first screen | ✅ | One-line pitch, live link, video, CLI |
| Install / run / test commands that work from a clean clone | ✅ | Verified on Node 20: `node test/run.mjs` passes with no install step |
| Requirements and dependencies | ✅ | "Node 18+, no dependencies" |
| Explains external APIs and why each is used | ✅ | Endpoint table for the Blockscout PRO API |
| Security and privacy model | ✅ | Read-only, the key stays in the browser, and the proxy behaviour is described |
| Licence | ✅ | MIT |
| Limitations | ⚠️ | Missing: scan depth (about the last 100 txs and transfers per chain), look-alike rule (first 4 and last 4 hex), and that it isn't an audit |
| Wording matches the output | ⚠️ | Says "critical finding"; the tool's highest level is `high` |
| Screenshot in the README | ⚠️ | `screenshots/` exists but none is embedded |
| Contributor hygiene | ⚠️ | `proxy/*` is gitignored but tracked; no CI badge or test workflow |

**Verdict:** 6 of 10 pass. The README is strong on what the tool does and how to run it. The fixes are a "Limitations" section, one wording change, an embedded screenshot and a CI workflow running `node test/run.mjs`, which together take about 20 minutes.

## What you get when you order a tool
A public repo under your name or ours (or a zip), under 300 lines, with tests and a README like this one, plus `delivery_url` and notes. Two revisions are included. We don't build scrapers that break a site's terms of service, malware, or anything that handles your keys.
