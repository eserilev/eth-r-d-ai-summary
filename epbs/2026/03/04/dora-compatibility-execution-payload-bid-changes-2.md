# Devnet genesis and Dora explorer issues

**Channel:** epbs | **Date:** 2026-03-04

## Summary

A new Ethereum devnet (epbs-devnet-0) has gone live but encountered immediate issues with the Dora blockchain explorer due to a schema bug. The team is currently unable to access the web-based explorer interface and must rely on SSH access to monitor the network status via CLI (`ssh devops@prysm-geth-1.srv.epbs-devnet-0.ethpandaops.io`).

A fix for the Dora explorer is being deployed through a new image build (GitHub Actions run #22674873564). The team is waiting to observe 100% network participation before proceeding to devnet1, with the network approaching a milestone called "gloas" in approximately 13 slots from the time of discussion.

Additional complications include a missing forkchoice dump endpoint in the current image, highlighting the team's heavy dependence on the Dora explorer for network monitoring and the challenges of reverting to command-line tools for debugging.

## Participants

- barnabasbusa
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T14:59:06.289000+00:00] barnabasbusa: its live
[2026-03-04T14:59:14.182000+00:00] barnabasbusa: we just had a dora schema bug
[2026-03-04T14:59:23.624000+00:00] terencechain: urls?
[2026-03-04T14:59:45.246000+00:00] barnabasbusa: `ssh devops@prysm-geth-1.srv.epbs-devnet-0.ethpandaops.io`
[2026-03-04T14:59:50.140000+00:00] barnabasbusa: gotta do the old fashioned way
[2026-03-04T14:59:55.165000+00:00] terencechain: no dora?
[2026-03-04T15:00:03.500000+00:00] barnabasbusa: <@363800010518822915>
[2026-03-04T15:00:03.895000+00:00] barnabasbusa: lmfao
[2026-03-04T15:00:08.878000+00:00] barnabasbusa: new image building
[2026-03-04T15:00:27.739000+00:00] barnabasbusa: https://github.com/ethpandaops/dora/actions/runs/22674873564
[2026-03-04T15:00:28.130000+00:00] terencechain: damn ok, sorry i cant wait
[2026-03-04T15:00:34.740000+00:00] barnabasbusa: once this passes the fix can be rolled out
[2026-03-04T15:00:45.782000+00:00] barnabasbusa: which is why i provided the ssh url 😂
[2026-03-04T15:00:55.251000+00:00] barnabasbusa: are we all blind without dora?
[2026-03-04T15:01:01.317000+00:00] barnabasbusa: everyone got too comfortable
[2026-03-04T15:01:04.610000+00:00] barnabasbusa: get back to the CLI
[2026-03-04T15:01:14.791000+00:00] terencechain: i just want to see 100% so we can move on to devnet1
[2026-03-04T15:01:33.998000+00:00] barnabasbusa: we are about to hit gloas
[2026-03-04T15:01:40.024000+00:00] barnabasbusa: 13 more slots
[2026-03-04T15:01:47.431000+00:00] barnabasbusa: so might see 100% till now
[2026-03-04T15:02:15.593000+00:00] potuz: shit we don't have the forkchoice dump endpoint working in that image
[2026-03-04T15:02:18.864000+00:00] barnabasbusa: https://klipy.com/gifs/im-blind-cheech-marin
[2026-03-04T15:02:22.998000+00:00] barnabasbusa: everyone without dora
```

</details>
