# Verify everything yourself

Nothing here needs our credentials. Reads are public `eth_call`; transaction checks are
public `eth_getTransactionReceipt`. RPCs used: mainnet
`https://ethereum-rpc.publicnode.com`, Base `https://base-rpc.publicnode.com`, Arbitrum
`https://arbitrum-one-rpc.publicnode.com`, Sepolia
`https://ethereum-sepolia-rpc.publicnode.com`.

## Re-verify any transaction (status should be 0x1)

```bash
curl -sS -X POST https://ethereum-sepolia-rpc.publicnode.com \
  -H 'content-type: application/json' \
  --data '{"jsonrpc":"2.0","id":1,"method":"eth_getTransactionReceipt","params":["<TX_HASH>"]}'
```

Hashes are listed in [PROOFS.md](PROOFS.md).

## Re-run the live protocol reads (KeeperHub CLI)

```bash
# #2422 cbETH
kh read 0xBe9895146f7AF43049ca1c1AE358B0541Ea49704 "exchangeRate()" --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
# #2421 Renzo pause gate + ezETH
kh read 0x74a09653A083691711cF8215a6ab074BB4e99ef5 "paused()"   --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
kh read 0xbf5495Efe5DB9ce00f80364C8B423567e58d2110 "decimals()" --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
# #2423 ether.fi
kh read 0xCd5fE23C85820F7B72D0926FC9b05b43E359b7ee "getRate()"  --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
# #2440 Chainlink multi-output
kh read 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419 "latestRoundData()" --chain 1 --rpc-url https://ethereum-rpc.publicnode.com
```

## Re-run the #2441 L2 fix (mainnet answers, L2 reverts)

```bash
kh read 0xa3931d71877C0E7a3148CB7Eb4463524FEc27fbD "totalAssets()" --chain 1     --rpc-url https://ethereum-rpc.publicnode.com
kh read 0x5875eEE11Cf8398102FdAd704C9E96607675467a "totalAssets()" --chain 8453  --rpc-url https://base-rpc.publicnode.com
kh read 0xdDb46999F8891663a8F2828d25298f70416d7610 "totalAssets()" --chain 42161 --rpc-url https://arbitrum-one-rpc.publicnode.com
```

## Re-run the test suites (in a KeeperHub checkout)

```bash
# #2469 / #2393 trace behaviour
cd keeperhub-events/event-tracker && npx vitest run tests/unit/provider-manager-trace.test.ts
# #2468 watchdog
cd keeperhub-events/event-tracker && npx vitest run tests/unit/provider-manager.test.ts
# #2440 output-field guard (repo root)
npx vitest run tests/unit/protocol-output-fields.test.ts
```

## Re-check CI on the open PRs

```bash
for pr in 2421 2423 2440 2441 2469; do gh pr checks $pr --repo KeeperHub/keeperhub; done
```
