---
title: "Token Due Diligence Sample: Collector Crypt (CARDS) on Solana, 20 Oct 2026"
tags: crypto, solana, research, defi
published: false
---

*Sample report by Astra Trading Company. All figures were pulled from public sources on 20 Oct 2026, around 23:45 (UTC+4). Markets move, so treat every number as a snapshot. This is research, not financial advice.*

## Why this token

Collector Crypt (CARDS) was one of the coins in CoinGecko's **trending list** on 20 Oct 2026. It is a Solana token tied to a platform that tokenises vaulted physical trading cards and sells "digital repacks" through a gacha machine. It is a mid-cap: CoinGecko ranks it **#140**.

- **Mint:** `CARDSccUMFKoPRZxt5vt3ksUbxEFEcnZ3H2pd3dKxYjp`
- **Token program:** SPL Token (`TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA`), 6 decimals
- **Website:** collectorcrypt.com and gacha.collectorcrypt.com. **X:** @Collector_Crypt
- **CoinGecko categories:** Solana Ecosystem, Real World Assets (RWA), Trading Card RWA Platform

## 1. Market snapshot (CoinGecko)

| Metric | Value |
|---|---|
| Price | $0.3456 |
| 24h change | +14.38% |
| 7d change | +21.63% |
| Market cap | $311.9M |
| Fully diluted valuation | $682.3M |
| 24h volume | $34.8M |
| Circulating supply | 902.6M |
| Total supply | 1,974.2M |
| Max supply | 2,000M |
| All-time high | $0.3814 (14 Sep 2025) |

**Reading it:** the price sits about 9% below its all-time high after a strong week. Volume is about 11% of market cap per day, which is healthy for a mid-cap. Only **about 46% of total supply is circulating**, so FDV is roughly **2.2× the market cap**. That gap is supply that can still reach the market.

## 2. Contract checks (Solana RPC, via `getAccountInfo` on the mint)

| Check | Result | Verdict |
|---|---|---|
| Mint authority | `null` (revoked) | ✅ No one can mint new tokens |
| Freeze authority | `null` (revoked) | ✅ No one can freeze holder accounts |
| Transfer fee | 0% (RugCheck) | ✅ No hidden tax |
| On-chain supply | 1,974,157,991.77 | Matches CoinGecko total supply |
| Metadata | **Mutable**. Update authority `redFWCNF…oEpuj`, which is also the creator wallet (RugCheck) | ⚠️ The name, symbol and logo can still be changed |

The core contract is clean. Supply is fixed, and holders can't be frozen. Mutable metadata is common and low severity, but it means the team can still change how the token displays in wallets.

## 3. Holder concentration (RugCheck API)

RugCheck counts **217,836 holders**. Concentration is the main issue:

| Rank | Holder (owner) | Share of supply |
|---|---|---|
| 1 | `3PnVBrb4…j5kF1QB` | **54.28%** |
| 2 | `96cJVtxc…2do4` | 4.19% |
| 3 | `4xkDss3j…xog7` | 3.26% |
| 4 | `Av47AL9p…MzyZz` | 3.26% |
| 5 | `EEaezt3o…72ea` | 3.26% |
| 6–10 | various | 0.76–0.93% each |
| **Top 10 total** | | **72.53%** |

- RugCheck flags both "Single holder ownership" and "Top 10 holders >70%" at **danger** level.
- We checked the top owner address on Solana RPC. It is a plain system-owned wallet (owner program `11111111111111111111111111111111`), **not** a vesting or lock program. So the tokens are not locked on-chain by any contract we could identify.
- Three wallets hold exactly 3.26% each. Identical balances usually point to team, investor or treasury allocations rather than organic buyers.
- RugCheck's insider-graph analysis reports **3,448 linked "insider" wallets**. That is a heuristic, not proof, but it is worth noting.

The 54% wallet is very likely the uncirculated treasury or unvested allocation, since it lines up with the ~54% gap between circulating and total supply. But **"likely" is not "verified"**. Without a public on-chain vesting contract, buyers are trusting the team's off-chain promises.

## 4. Liquidity and trading (DexScreener and RugCheck)

DexScreener lists **30 trading pairs**. Their reported liquidity adds up to about **$7.0M**. RugCheck's own market-liquidity measure is lower, at **$2.09M**, because the two tools count different pools in different ways.

| DEX | Pair | Liquidity | 24h volume | 24h buys / sells |
|---|---|---|---|---|
| Raydium | CARDS/USDC | $4.61M | $13.87M | 68,470 / 52,654 |
| Orca | CARDS/SOL | $0.50M | $1.29M | 4,458 / 3,497 |
| Meteora | CARDS/USDC | $0.41M | $0.11M | 321 / 238 |
| Meteora | CARDS/SOL | $0.36M | $4.75M | 26,008 / 21,854 |
| Meteora | CARDS/SOL | $0.25M | $0.35M | 982 / 1,865 |
| Meteora | CARDS/USDC | $0.24M | $1.23M | 6,519 / 5,799 |

- Most pools were created between late August and early September 2025, according to DexScreener's pair-creation timestamps.
- On the main Raydium pool, buys outnumbered sells about 1.3 to 1 over 24h, which matches the price rise.
- **LP lock:** RugCheck shows **0% locked LP** on the Orca and Meteora pools it lists. The only locker entry holds about $0.04 of value. That's normal for concentrated-liquidity and market-maker pools, but it means liquidity can be pulled.
- Compared with a $34.8M daily volume, the roughly $4.6M in the main pool is thin. A single large holder selling would move the price sharply.

## 5. Socials and fundamentals

- There is an active website, a separate gacha product site, and an X account (@Collector_Crypt).
- In its CoinGecko description, the project claims "**$75m revenue and growing**." **We could not verify this figure** from any on-chain or audited source in this review. Treat it as a marketing claim until it is backed by evidence.
- The RWA angle (physical cards held in a vault) adds custody risk that no on-chain check can cover: who holds the cards, and is the vault insured and audited?

## 6. Red-flag summary

| # | Finding | Severity |
|---|---|---|
| 1 | One wallet holds 54.28% of supply, with no on-chain lock we could identify | 🔴 High |
| 2 | Top 10 wallets hold 72.53% | 🔴 High |
| 3 | Only ~46% circulating, FDV 2.2× market cap, so future unlock or sell pressure | 🟠 Medium |
| 4 | LP on the listed pools is unlocked | 🟠 Medium |
| 5 | $75M revenue claim not independently verified | 🟠 Medium |
| 6 | Off-chain custody risk for the physical cards | 🟠 Medium |
| 7 | Mutable metadata held by the creator wallet | 🟡 Low |
| ✅ | Mint and freeze authority revoked, 0% transfer fee, deep multi-DEX trading, 217k+ holders | Positive |

## Verdict

**The contract is clean, but the risk sits in who holds the supply.** CARDS passes the hard rug checks: there is no mint, no freeze and no tax. It trades actively on many pools, which is better than most trending tokens. The main risk is supply: one unlocked wallet controls more than half of all tokens. Before putting in meaningful size, we would want (a) a published, on-chain vesting schedule for the 54% wallet, (b) evidence for the revenue claim, and (c) proof-of-reserve details for the vaulted cards. Any position should be sized small enough to survive a sudden treasury sell.

### Sources
- CoinGecko API: `/coins/collector-crypt` and `/search/trending`
- Solana mainnet RPC: `getAccountInfo` for the mint and the top-holder owner
- DexScreener API: `/latest/dex/tokens/CARDSccUMFKoPRZxt5vt3ksUbxEFEcnZ3H2pd3dKxYjp`
- RugCheck API: `/v1/tokens/CARDSccUMFKoPRZxt5vt3ksUbxEFEcnZ3H2pd3dKxYjp/report`

---
Need one for your project? Hire Astra on LaborX: https://laborx.com/freelancers/users/id475339
