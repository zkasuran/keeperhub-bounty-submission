# KeeperHub Bounty Submission

**Best KeeperHub Feature.** Eight pull requests to
[KeeperHub/keeperhub](https://github.com/KeeperHub/keeperhub): **three merged, five open and
passing CI.** Every claim on this page links to something you can re-run or re-verify. Nothing
below is asserted without a receipt.

- **Source:** https://github.com/KeeperHub/keeperhub
- **Demo video (150s, every command is real CLI output):** https://github.com/zkasuran/keeperhub/releases/download/keeperhub-bounty-demo/DEMO-keeperhub-bounty.mp4
- **Primary transaction through KeeperHub (Sepolia, status 0x1):** https://sepolia.etherscan.io/tx/0xc2cbd9ed1c35bc449875fc79dd9ac656a102e25b9d00bcd92ac81921e122ff2f

---

## Table of contents

1. [What this is](#what-this-is)
2. [The 8 PRs at a glance](#the-8-prs-at-a-glance)
3. [Feature area 1: protocol integrations](#feature-area-1-protocol-integrations)
4. [Feature area 2: event tracker and triggers](#feature-area-2-event-tracker-and-triggers)
5. [Proof that it runs: transactions through KeeperHub](#proof-that-it-runs-transactions-through-keeperhub)
6. [Verify everything yourself](#verify-everything-yourself)
7. [How the review went](#how-the-review-went)
8. [The demo video](#the-demo-video)
9. [Honesty note and AI assistance](#honesty-note-and-ai-assistance)

---

## What this is

KeeperHub executes on-chain work deterministically, with a full audit trail: an agent composes
a workflow, a human reviews it, and that exact workflow runs. For that execution layer to be
trusted it has to reach real protocols and keep firing the right triggers under real conditions.

These eight PRs push on both halves. Four widen what an agent can read and act on: Coinbase
cbETH, Renzo ezETH, ether.fi weETH/eETH, and a Sky/USDS split so L2 chains stop advertising
functions their deployments do not implement. Four harden the event tracker: the trace-call
matcher and its wiring into the tracker for reverted and internal-call triggers, ABI-derived
output field paths so advertised fields resolve at runtime, and a block-staleness watchdog that
reconnects a chain that answers the heartbeat but has stopped delivering blocks.

Everything shown in the demo and linked here was run through the KeeperHub CLI (`kh`) or the
test runner, and every on-chain write was re-verified against public RPC.

---

## The 8 PRs at a glance

| PR | Title | State | Area | Detail |
| --- | --- | --- | --- | --- |
| [#2393](https://github.com/KeeperHub/keeperhub/pull/2393) | trace-call matcher for reverted/internal-call triggers | **merged** | event tracker | [prs/2393.md](prs/2393.md) |
| [#2422](https://github.com/KeeperHub/keeperhub/pull/2422) | Coinbase cbETH read integration | **merged** | protocol | [prs/2422.md](prs/2422.md) |
| [#2468](https://github.com/KeeperHub/keeperhub/pull/2468) | block-staleness watchdog checks both subscriber types | **merged** | event tracker | [prs/2468.md](prs/2468.md) |
| [#2421](https://github.com/KeeperHub/keeperhub/pull/2421) | Renzo ezETH liquid restaking | open, CI green | protocol | [prs/2421.md](prs/2421.md) |
| [#2423](https://github.com/KeeperHub/keeperhub/pull/2423) | ether.fi weETH/eETH liquid restaking | open, CI green | protocol | [prs/2423.md](prs/2423.md) |
| [#2440](https://github.com/KeeperHub/keeperhub/pull/2440) | ABI-derived output field paths | open, CI green | workflow editor | [prs/2440.md](prs/2440.md) |
| [#2441](https://github.com/KeeperHub/keeperhub/pull/2441) | Sky/USDS L2 contract split | open, CI green | protocol | [prs/2441.md](prs/2441.md) |
| [#2469](https://github.com/KeeperHub/keeperhub/pull/2469) | trace matcher wired into event tracker | open, CI green | event tracker | [prs/2469.md](prs/2469.md) |

Merged: #2393, #2422, #2468. Open and CI green: #2421, #2423, #2440, #2441, #2469.

---

## Feature area 1: protocol integrations

More protocols an agent can read and act on, each verified against the real contract.

### #2422 Coinbase cbETH read integration (merged)
Adds cbETH to the protocol registry. Verified live on mainnet:
```
kh read 0xBe9895146f7AF43049ca1c1AE358B0541Ea49704 "exchangeRate()" --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
  -> 1139686501937561335        # 1.13968 ETH per cbETH
kh read 0xBe9895146f7AF43049ca1c1AE358B0541Ea49704 "totalSupply()"  --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
  -> 393750601365061869784889
```

### #2421 Renzo ezETH liquid restaking (open, CI green)
Adds Renzo ezETH. The RestakeManager pause gate output is **named**, so its advertised template
path is the one `structureAbiOutputs` produces and a workflow can read `paused` at runtime
instead of getting a bare value it cannot bind to. Write simulations require non-null revert
data, so a test proves the deployed bytecode parsed the calldata rather than passing on any
`CALL_EXCEPTION`. Verified live:
```
kh read 0x74a09653A083691711cF8215a6ab074BB4e99ef5 "paused()"   --chain 1 ...  -> 0 (not paused)
kh read 0xbf5495Efe5DB9ce00f80364C8B423567e58d2110 "decimals()" --chain 1 ...  -> 18
# name() decodes to "Renzo Restaked ETH"
```

### #2423 ether.fi weETH/eETH liquid restaking (open, CI green)
Adds weETH/eETH and the `approve-eeth` action, so the stake-then-wrap path is completable in
product (the way Lido exposes `approve-steth`). Addresses were cross-checked on mainnet:
`weETH.eETH()` returns the declared eETH address, and getRate / amountForShare / getEETHByWeETH
are byte-identical. Verified live:
```
kh read 0xCd5fE23C85820F7B72D0926FC9b05b43E359b7ee "getRate()" --chain 1 ...  -> 1103911155878575017  # 1.10391
```

### #2441 Sky/USDS L2 contract split (open, CI green)
Splits Sky/USDS into a mainnet contract with the full ABI and a read-only L2 contract, so L2
chains stop advertising ERC-4626 vault functions their deployments revert on. The dead
`get-susds-total-assets-l2` action was removed and the goldens regenerated. **The fix, shown
live:** `totalAssets()` is a vault method present only on mainnet; on Base and Arbitrum the
deployed sUSDS is a bridged token, so it reverts, while `balanceOf` still works.
```
kh read 0xa3931d71877C0E7a3148CB7Eb4463524FEc27fbD "totalAssets()" --chain 1     ...  -> 4381515516014605268821377264  (mainnet answers)
kh read 0x5875eEE11Cf8398102FdAd704C9E96607675467a "totalAssets()" --chain 8453  ...  -> execution reverted            (Base)
kh read 0xdDb46999F8891663a8F2828d25298f70416d7610 "totalAssets()" --chain 42161 ...  -> execution reverted            (Arbitrum)
kh read 0x5875eEE11Cf8398102FdAd704C9E96607675467a "balanceOf(address)" 0x33a4... --chain 8453 ...  -> 0  (the read the PR keeps)
```

---

## Feature area 2: event tracker and triggers

Keeping the right triggers firing under real conditions, including calls a plain log filter
misses.

### #2393 trace-call matcher (merged)
The foundation: a matcher that classifies reverted and internal calls so workflows can trigger
on them. #2469 wires it into the event tracker. Covered by the trace-matcher unit suite.

### #2469 trace matcher wired into the event tracker (open, CI green)
Workflows can now trigger on reverted and internal calls. `checkBlockStaleness`, `reconnect()`
and the drain guard test all three subscriber sets (log, state, trace); the post-reconnect
catch-up arms for log-or-trace (state owes no range). **Strongest proof:** a real ERC-20
transfer executed through KeeperHub runs as an internal call inside the relayer transaction, the
exact shape the matcher catches.
- Tx: https://sepolia.etherscan.io/tx/0x45494db90e017321e7d18d3ac480dde06f129a04fa73f3681f3eff285df9b1ce (status 0x1)
- The relayer calldata carries selector `0xa9059cbb` and the DAI address; the DAI contract emitted the `Transfer` event (topic0 `0xddf252ad...b3ef`).
- Live workflow: `kh workflow get qxsd20ivw39ny9ee3s2il` shows "Trace Trigger Demo", triggerType Trace, `traceSelector 0xa9059cbb`.

### #2468 block-staleness watchdog (merged)
The watchdog returned early when only the log-subscriber set was empty, so a state-only chain
that answered the heartbeat but stopped delivering blocks was never reconnected. It now checks
both sets, and `reconnect()` re-attaches the block listener for either. Pinned by regression
tests that fail against the unfixed code.

### #2440 ABI-derived output field paths (open, CI green)
Advertised output field paths now match what `structureAbiOutputs` returns at runtime, so a
template like `{{Node.balance}}` resolves instead of returning undefined. Shown live on a
Chainlink multi-output feed, where all five fields resolve:
```
kh read 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419 "latestRoundData()" --chain 1 ...
  result.roundId, result.answer (ETH/USD 2502.18), result.startedAt, result.updatedAt, result.answeredInRound
```
History, stated plainly: opened against #2424 on 09-14; a sibling PR (#2535) merged an
equivalent fix on staging on 09-18, so this was rebased to a **test-only** PR that pins the
label-join across the whole registry (400+ placements). Proven to bite: reverting staging's
positional join fails 4 of 7 cases with 507 mislabeled fields.

---

## Proof that it runs: transactions through KeeperHub

Every write was executed through the KeeperHub CLI on **Ethereum Sepolia**, signer
`0x33a41051f0a8ef15e6175dae4327e20f09b78ff4`, routed through KeeperHub's relayer
`0x809d8252aa4f9b8f7d9be7213855b289fe7d0444`, self-scoped so no value leaves the wallet. Each
re-verified on-chain, status `0x1`.

| PR | Transaction | Block |
| --- | --- | --- |
| primary | [0xc2cbd9ed...122ff2f](https://sepolia.etherscan.io/tx/0xc2cbd9ed1c35bc449875fc79dd9ac656a102e25b9d00bcd92ac81921e122ff2f) | 0xb2fab3 |
| #2469 (ERC-20 internal-call transfer) | [0x45494db9...5df9b1ce](https://sepolia.etherscan.io/tx/0x45494db90e017321e7d18d3ac480dde06f129a04fa73f3681f3eff285df9b1ce) | 0xb2fb07 |
| #2423 (approve round-trip) | [0xc1863137...5da6f73a](https://sepolia.etherscan.io/tx/0xc1863137e7c9bfcb6e1c565d2f26a6f69aaf9e87dfcc4e7b1b0bf4955da6f73a) | 0xb2fad0 |
| #2441 | [0xe8b52cd2...451f195e3f](https://sepolia.etherscan.io/tx/0xe8b52cd2080a1005c2c62fecc04bd5cf97120802035d9f342f263a451f195e3f) | 0xb2fab9 |
| #2440 | [0x561aa652...8720b604](https://sepolia.etherscan.io/tx/0x561aa6524d8ffd193db5744759395feac7121a6f77ac9331b5bd36178720b604) | 0xb2fae4 |
| #2421 | [0xb75b5d58...c4b6e614](https://sepolia.etherscan.io/tx/0xb75b5d5812915648600998cb276de1a0a2824016bce3fc284e2de75cc4b6e614) | 0xb2fae2 |
| #2468 | [0x941f590e...57c92958](https://sepolia.etherscan.io/tx/0x941f590ec7e877b54a2ab34c145acebac7bb846ee6ca35c8c1b7d57957c92958) | 0xb2fb15 |
| #2393 | [0x6bcf58d1...9d642a9](https://sepolia.etherscan.io/tx/0x6bcf58d1758b34a098a1a1dfc2f84a818f7cc8c7b788d6d1e0b77b57f9d642a9) | 0xb2fb16 |

The #2423 approve is a full write-then-read round-trip: after the approve, the allowance read
back through `kh read` returned exactly the approved amount. Details: [PROOFS.md](PROOFS.md).

---

## Verify everything yourself

No credentials needed. Reads are public `eth_call`; transaction checks are public
`eth_getTransactionReceipt`. Full command list: [VERIFY.md](VERIFY.md).

```bash
# any transaction (status should be 0x1)
curl -sS -X POST https://ethereum-sepolia-rpc.publicnode.com -H 'content-type: application/json' \
  --data '{"jsonrpc":"2.0","id":1,"method":"eth_getTransactionReceipt","params":["<TX_HASH>"]}'

# the #2441 fix live (mainnet answers, L2 reverts)
kh read 0xa3931d71877C0E7a3148CB7Eb4463524FEc27fbD "totalAssets()" --chain 1    --rpc-url https://ethereum-rpc.publicnode.com
kh read 0x5875eEE11Cf8398102FdAd704C9E96607675467a "totalAssets()" --chain 8453 --rpc-url https://base-rpc.publicnode.com

# CI on the open PRs
for pr in 2421 2423 2440 2441 2469; do gh pr checks $pr --repo KeeperHub/keeperhub; done
```

---

## How the review went

Each open PR carried real maintainer reviews, and each review item was addressed and verified,
not asserted. Where a reviewer asked for a control, it was run: for example, reverting a fix and
showing the pinned test then fails. Highlights:

- **#2468** the reviewer's "one more line" (reconnect re-attach for state subscribers) was added, the catch-up correctly kept log-scoped, the misleading comment corrected, and the description honestly downgraded from "live" to "latent".
- **#2441** the dead `totalAssets` L2 advertisement was removed and goldens regenerated; phantom skip entries deleted; the Lido skip reason narrowed to Base; each L2 action set pinned.
- **#2469** when #2468 landed on staging, this branch's overlap was merged and resolved to the three-set superset (log + state + trace), verified green.
- **#2440** rebased to test-only rather than duplicate a fix that had merged on staging, keeping only the registry-wide regression guard that staging lacks.

Per-PR review detail is in each [prs/](prs/) page.

---

## The demo video

**Watch:** https://github.com/zkasuran/keeperhub/releases/download/keeperhub-bounty-demo/DEMO-keeperhub-bounty.mp4

150 seconds, 1920x1080. Every scene reads a captured receipt, so any frame traces back to a real
command. Scene guide: [DEMO.md](DEMO.md). If your form needs an embeddable player rather than a
direct MP4, the same file uploads to YouTube or Vimeo and the link swaps in.

---

## Honesty note and AI assistance

Three PRs merged (#2393, #2422, #2468); five open and CI green, pending maintainer re-review
only. On-chain writes were executed on Ethereum Sepolia (testnet) and re-verified against public
RPC; protocol behaviour was verified against mainnet RPC. One follow-up flagged in review is out
of scope here: on the mixed log-plus-state reconnect path the catch-up can hand a stale
pre-outage state reading; it is byte-identical on staging and filed for its own change.

AI assistance (Kiro) was used in developing this work. The design, review, and verification are
the author's.
