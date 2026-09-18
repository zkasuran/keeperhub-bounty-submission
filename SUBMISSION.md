# DoraHacks submission fields (one BUIDL, all 8 PRs)

Paste these into the DoraHacks BUIDL form. A click-to-copy version is at
`../submission/buidl-single.html`.

## Title

KeeperHub: 8 PRs of protocol integrations and event-tracker reliability, executed through the CLI

## Mandatory links

- Source: https://github.com/KeeperHub/keeperhub
- Demo video: https://github.com/zkasuran/keeperhub/releases/download/keeperhub-bounty-demo/DEMO-keeperhub-bounty.mp4
- Transaction through KeeperHub: https://sepolia.etherscan.io/tx/0xc2cbd9ed1c35bc449875fc79dd9ac656a102e25b9d00bcd92ac81921e122ff2f

## Q1 - Which project did you integrate with, and what does the integration do?

This Bounty submission integrates into KeeperHub itself, as eight open-source pull requests.
Four add protocol read/action support (Coinbase cbETH, Renzo ezETH, ether.fi weETH/eETH, and
a Sky/USDS L2 contract split) so agents and workflows can read and act on more protocols and
networks safely. Four harden the event tracker and triggers (the trace-call matcher and its
wiring into the event tracker for reverted and internal-call triggers, ABI-derived output
field paths so advertised fields resolve at runtime, and a block-staleness watchdog that
reconnects a chain that stops delivering blocks). Surfaces used: the CLI (kh read, kh execute
transfer, kh execute contract-call), agent-authored workflows, the open-source repo, and the
audit trail. Testnet or mainnet: both, protocol behaviour verified on mainnet and the
transactions through KeeperHub executed on Ethereum Sepolia.

## Q3 - What still breaks or is unfinished?

3 of 8 PRs are merged (#2393, #2422, #2468). The other 5 are open, CI green, and mergeable,
pending maintainer re-review only. Each review item was addressed and verified, including
control reverts that prove the tests fail against the unfixed code. #2440 was rebased to a
test-only PR after a sibling PR merged an equivalent fix on staging, so it now lands as the
registry-wide regression guard staging lacks. A known follow-up flagged in review, not in
scope: on the mixed log-plus-state reconnect path the catch-up can hand a stale pre-outage
state reading; byte-identical on staging, filed for its own change.

## Contact (edit before submitting)

Email: zkcryptoasuran@gmail.com  |  X: [@your_handle]  |  Discord: [your_discord_handle]
