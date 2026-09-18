# Proofs - transactions through KeeperHub and live reads

All on-chain writes executed on **Ethereum Sepolia** via the KeeperHub CLI, signer
`0x33a41051f0a8ef15e6175dae4327e20f09b78ff4`, routed through KeeperHub's relayer
`0x809d8252aa4f9b8f7d9be7213855b289fe7d0444`. Self-scoped, so no value leaves the wallet.
Every one re-verified on-chain (status `0x1`). Reads use public RPCs. See
[VERIFY.md](VERIFY.md) to re-run any of these.

## Transactions through KeeperHub

| PR | Tx (Sepolia, status 0x1) | Block |
| --- | --- | --- |
| primary | [0xc2cbd9ed...122ff2f](https://sepolia.etherscan.io/tx/0xc2cbd9ed1c35bc449875fc79dd9ac656a102e25b9d00bcd92ac81921e122ff2f) | 0xb2fab3 |
| #2469 (ERC-20 internal-call transfer) | [0x45494db9...5df9b1ce](https://sepolia.etherscan.io/tx/0x45494db90e017321e7d18d3ac480dde06f129a04fa73f3681f3eff285df9b1ce) | 0xb2fb07 |
| #2423 (approve round-trip) | [0xc1863137...5da6f73a](https://sepolia.etherscan.io/tx/0xc1863137e7c9bfcb6e1c565d2f26a6f69aaf9e87dfcc4e7b1b0bf4955da6f73a) | 0xb2fad0 |
| #2441 | [0xe8b52cd2...451f195e3f](https://sepolia.etherscan.io/tx/0xe8b52cd2080a1005c2c62fecc04bd5cf97120802035d9f342f263a451f195e3f) | 0xb2fab9 |
| #2440 | [0x561aa652...8720b604](https://sepolia.etherscan.io/tx/0x561aa6524d8ffd193db5744759395feac7121a6f77ac9331b5bd36178720b604) | 0xb2fae4 |
| #2421 | [0xb75b5d58...c4b6e614](https://sepolia.etherscan.io/tx/0xb75b5d5812915648600998cb276de1a0a2824016bce3fc284e2de75cc4b6e614) | 0xb2fae2 |
| #2468 | [0x941f590e...57c92958](https://sepolia.etherscan.io/tx/0x941f590ec7e877b54a2ab34c145acebac7bb846ee6ca35c8c1b7d57957c92958) | 0xb2fb15 |
| #2393 | [0x6bcf58d1...9d642a9](https://sepolia.etherscan.io/tx/0x6bcf58d1758b34a098a1a1dfc2f84a818f7cc8c7b788d6d1e0b77b57f9d642a9) | 0xb2fb16 |

## The #2469 internal-call transfer, in full

An ERC-20 DAI transfer executed through KeeperHub. The relayer wraps the call, so the
`0xa9059cbb` transfer happens as an internal call, the exact shape the trace matcher catches:

- relayer calldata contains the DAI address `0xFF34B3d4...` and selector `0xa9059cbb`
- the DAI contract emitted `Transfer` (topic0 `0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef`)
- receipt status `0x1`, block `0xb2fb07`

## The #2423 approve round-trip

`kh execute contract-call ... approve` on Sepolia WETH, then the allowance read back through
`kh read` returned `1000000000000000` exactly, proving the write landed through KeeperHub.

## The #2441 L2 revert, shown live

`totalAssets()` answers on mainnet sUSDS but reverts on Base and Arbitrum, while `balanceOf`
works on Base. Full commands in [prs/2441.md](prs/2441.md).

## Live protocol reads (mainnet)

| PR | Read | Result |
| --- | --- | --- |
| #2422 | cbETH exchangeRate() | 1139686501937561335 (1.13968) |
| #2422 | cbETH totalSupply() | 393750601365061869784889 |
| #2421 | Renzo RestakeManager paused() | 0 (false) |
| #2421 | ezETH decimals() | 18 |
| #2423 | weETH getRate() | 1103911155878575017 (1.10391) |
| #2440 | Chainlink latestRoundData() | 5-field tuple, all resolve (ETH/USD 2502.18) |
