# The $14,500 Question

**5 free, open source GitHub repos that do what a paid Solana trading bot does.**

I was paying a bot 1% per trade. Here is what that actually costs, and the
five repositories that replace it for nothing.

---

## The math nobody puts on the landing page

| Input | Value |
|---|---|
| Trades per year (active memecoin wallet) | 2,900 |
| Average clip | $500 |
| Volume routed | $1,450,000 |
| Bot fee at 1% | **$14,500** |
| Realistic all-in round trip (fee + priority + slippage) | **3.2% to 6.6%** |

The 1% is the number you agree to. The 3.2-6.6% is the number you pay.
Priority fees, slippage tolerance and the spread you eat on the way out are
not in the fee tab, and they are larger than the fee.

**None of the five repos below predicts a price. Every one of them deletes a
way to lose money you did not know you had.**

---

## Comparison

| # | Repo | Stars | License | Language | What it removes from you |
|---|---|---|---|---|---|
| 1 | [warp-id/solana-trading-bot](https://github.com/warp-id/solana-trading-bot) | 2.3k | - | TypeScript | Buying into a token that was never going to let you out |
| 2 | [chainstacklabs/pump-fun-bot](https://github.com/chainstacklabs/pump-fun-bot) | 985 | Apache-2.0 | Python | Blind market orders into a bonding curve |
| 3 | [rckprtr/pumpdotfun-sdk](https://github.com/rckprtr/pumpdotfun-sdk) | 819 | MIT | TypeScript | The middleman taking a percent of every trade |
| 4 | [jito-labs/jito-ts](https://github.com/jito-labs/jito-ts) | 197 | Apache-2.0 | TypeScript | Being sandwiched in the public mempool |
| 5 | [degenfrends/solana-rugchecker](https://github.com/degenfrends/solana-rugchecker) | 76 | MIT | TypeScript | Holder distribution you were never going to open |

Star counts as of the date of this README.

---

## 1. solana-trading-bot

**https://github.com/warp-id/solana-trading-bot**

A buy/sell bot for Raydium with a filter stack that runs **before** the buy,
not in the postmortem:

- mint authority renounced
- LP burned or locked
- freeze authority
- mutable metadata
- minimum pool size

Each filter is a separate check you can switch on and off. The value is not
the trades it takes. It is the ones it refuses.

```
# .env, the part that matters
CHECK_IF_MINT_IS_RENOUNCED=true
CHECK_IF_BURNED=true
CHECK_IF_FREEZABLE=true
CHECK_IF_MUTABLE=true
MIN_POOL_SIZE=5
```

**Use it for:** a pre-trade veto you cannot talk yourself out of at 3am.

---

## 2. pump-fun-bot

**https://github.com/chainstacklabs/pump-fun-bot**

Chainstack's educational implementation of the pump.fun bonding curve, in
Python, in scripts you can actually run. Listeners for new tokens (logs,
blocks, geyser), curve state decoding, buy and sell paths.

The reason it is on this list: you can put **your own clip size** into the
curve math and watch what your order does to your own fill before you send
it. Most people are not losing to the coin. They are losing to their own
market order.

**Use it for:** understanding why a $2,000 buy fills 11% worse than a $200
one on the same chart.

---

## 3. pumpdotfun-sdk

**https://github.com/rckprtr/pumpdotfun-sdk**

A TypeScript SDK that talks to the pump.fun program directly.
`createAndBuy`, `buy`, `sell`, plus event listeners for create, trade and
complete.

```ts
import { PumpFunSDK } from "pumpdotfun-sdk";

const sdk = new PumpFunSDK(provider);

await sdk.buy(
  keypair,            // your wallet
  mint,               // token mint
  BigInt(0.1 * 1e9),  // 0.1 SOL
  300n,               // 3% slippage in basis points
);
```

That `300n` is the whole point. It is your slippage, set by you, in basis
points, instead of a default you never saw.

**Use it for:** removing the per-trade percent permanently.

---

## 4. jito-ts

**https://github.com/jito-labs/jito-ts**

Jito's TypeScript client for the block engine. Sends your transaction as a
**bundle** instead of dropping it into the public mempool where anyone
running a searcher can see it and build a sandwich around it.

```ts
const bundle = new Bundle([tx], 5);
bundle.addTipTx(keypair, tipLamports, tipAccount, blockhash);
await client.sendBundle(bundle);
```

Bundles execute atomically and in order. Nothing gets inserted in front of
your buy or behind your sell.

**Use it for:** not being exit liquidity for someone who watches pending
transactions for a living.

---

## 5. solana-rugchecker

**https://github.com/degenfrends/solana-rugchecker**

A rug score assembled from metadata, top holder concentration and liquidity.
Small repo, single job, no service to sign up for.

**Use it for:** the 20 seconds of holder distribution you were never going to
look at manually.

---

## Quick start

```bash
# 1. the veto layer
git clone https://github.com/warp-id/solana-trading-bot
cd solana-trading-bot && npm install
cp .env.copy .env      # set the CHECK_IF_* flags to true

# 2. the curve math
git clone https://github.com/chainstacklabs/pump-fun-bot
cd pump-fun-bot && pip install -r requirements.txt

# 3. direct execution
npm i pumpdotfun-sdk

# 4. bundle submission
npm i jito-ts
```

You need an RPC endpoint. A free public one will rate limit you on the
listeners; a paid one is $0 to $50 a month depending on the provider, which
is still less than one month of the $14,500.

---

## What this list is not

- It is not a strategy. Nothing here tells you what to buy.
- It is not a guarantee. A filter stack reduces the ways you lose. It does
  not make you right.
- It is not financial advice. These are repositories.

## Before you run any of it

1. **Read the code.** All five are open source. That is the feature.
2. **Never paste a private key you care about.** Use a fresh wallet funded
   with an amount you would shrug at.
3. **Test on a token you do not care about first.** Every one of these can
   send a real transaction with real money.
4. **Check the license** in each repo before you use it in anything of your
   own.

---

*Star counts and repo details verified at the time of writing. Repos change.
Check them yourself.*
