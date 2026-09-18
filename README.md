# KeeperHub Bounty Submission

**Best KeeperHub Feature** bounty. Eight pull requests to
[KeeperHub/keeperhub](https://github.com/KeeperHub/keeperhub): three merged, five open
and passing CI. Every claim on this page links to something you can re-run or re-verify.

- **Source:** https://github.com/KeeperHub/keeperhub
- **Demo video (150s, every command is real CLI output):** https://github.com/zkasuran/keeperhub/releases/download/keeperhub-bounty-demo/DEMO-keeperhub-bounty.mp4
- **Primary transaction through KeeperHub (Sepolia, status 0x1):** https://sepolia.etherscan.io/tx/0xc2cbd9ed1c35bc449875fc79dd9ac656a102e25b9d00bcd92ac81921e122ff2f

## The 8 PRs at a glance

| PR | Title | State | Feature area | Detail |
| --- | --- | --- | --- | --- |
| [#2393](https://github.com/KeeperHub/keeperhub/pull/2393) | trace-call matcher for reverted/internal-call triggers | merged | event tracker | [prs/2393.md](prs/2393.md) |
| [#2422](https://github.com/KeeperHub/keeperhub/pull/2422) | Coinbase cbETH read integration | merged | protocol | [prs/2422.md](prs/2422.md) |
| [#2468](https://github.com/KeeperHub/keeperhub/pull/2468) | block-staleness watchdog checks both subscriber types | merged | event tracker | [prs/2468.md](prs/2468.md) |
| [#2421](https://github.com/KeeperHub/keeperhub/pull/2421) | Renzo ezETH liquid restaking | open, CI green | protocol | [prs/2421.md](prs/2421.md) |
| [#2423](https://github.com/KeeperHub/keeperhub/pull/2423) | ether.fi weETH/eETH liquid restaking | open, CI green | protocol | [prs/2423.md](prs/2423.md) |
| [#2440](https://github.com/KeeperHub/keeperhub/pull/2440) | ABI-derived output field paths | open, CI green | workflow editor | [prs/2440.md](prs/2440.md) |
| [#2441](https://github.com/KeeperHub/keeperhub/pull/2441) | Sky/USDS L2 contract split | open, CI green | protocol | [prs/2441.md](prs/2441.md) |
| [#2469](https://github.com/KeeperHub/keeperhub/pull/2469) | trace matcher wired into event tracker | open, CI green | event tracker | [prs/2469.md](prs/2469.md) |

## What it adds

**Protocol integrations** so agents and workflows can read and act on more protocols:
Coinbase cbETH (#2422), Renzo ezETH (#2421), ether.fi weETH/eETH (#2423), and a Sky/USDS
L2 contract split (#2441) so L2 chains stop advertising functions their deployments do not
implement.

**Event-tracker reliability and triggers:** the trace-call matcher (#2393) wired into the
event tracker (#2469) for reverted and internal-call triggers, ABI-derived output field
paths so advertised fields resolve at runtime (#2440), and a block-staleness watchdog that
reconnects a chain that answers the heartbeat but stops delivering blocks (#2468).

## Navigation

- **[prs/](prs/)** - one page per PR: what it does, the review items and how they were resolved, and its own KeeperHub-executed proof.
- **[PROOFS.md](PROOFS.md)** - every transaction executed through KeeperHub and every live read, with commands to reproduce and re-verify.
- **[DEMO.md](DEMO.md)** - the demo video, what each scene shows, and how it was built (real captured output).
- **[VERIFY.md](VERIFY.md)** - copy-paste commands a maintainer can run to re-verify every proof independently.
- **[SUBMISSION.md](SUBMISSION.md)** - the DoraHacks form answers (title, description, Q1, Q3, contact) in one place.

## Honesty note

Three PRs merged (#2393, #2422, #2468), five open and CI green. Every open PR had its
review items addressed and verified, including control reverts that prove the tests fail
against the unfixed code. On-chain writes were executed on Ethereum Sepolia (testnet) and
re-verified against public RPC; protocol behaviour was verified against mainnet RPC. AI
assistance (Kiro) was used; design, review and verification are the author's.
