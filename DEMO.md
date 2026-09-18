# Demo video

**Watch:** https://github.com/zkasuran/keeperhub/releases/download/keeperhub-bounty-demo/DEMO-keeperhub-bounty.mp4
(release page: https://github.com/zkasuran/keeperhub/releases/tag/keeperhub-bounty-demo)

150 seconds, 1920x1080 H.264, -16.4 LUFS. Every command on screen is real output captured
from the KeeperHub CLI or the test runner. Nothing is typed by hand; each scene reads a
captured receipt.

## Scenes

1. Intro. The honest tally: 8 PRs, 3 merged, 5 open and CI green.
2. #2422 cbETH: live mainnet read of exchangeRate and totalSupply.
3. #2421 Renzo: paused() readable at runtime, ezETH decimals and name.
4. #2423 ether.fi: weETH getRate live on mainnet.
5. #2441: the fix shown live. sUSDS totalAssets answers on mainnet, reverts on Base and Arbitrum, balanceOf works on Base.
6. #2440: a Chainlink multi-output read where all five derived field paths resolve.
7. #2469: a real ERC-20 transfer through KeeperHub, receipt status 0x1 with the Transfer event the trace workflow matches.
8. #2468 and #2393: the watchdog and trace-matcher test suites passing.
9. Close.

## How it was built

The video is rendered from a project file whose terminal scenes read captured `.stdout`
receipts. The receipts and the commands that produced them are kept alongside the build, so
any frame traces back to a real command. If DoraHacks requires an embeddable player rather
than a direct MP4, upload the same file to YouTube or Vimeo and swap the link.
