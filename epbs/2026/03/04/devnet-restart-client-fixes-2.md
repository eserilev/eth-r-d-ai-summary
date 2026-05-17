# Devnet Restart and Client Fixes

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The team is planning to restart devnet0 in a day or two once clients have implemented fixes for attestation committee index issues and payload by range support. The current devnet session has revealed two bugs in Prysm - one previously known but not merged into the devnet branch, and another being investigated by potuz.

The discussion reveals that Prysm incorrectly declared a Teku block as invalid when it should have been valid, which caused cascading issues in the small devnet environment due to peer downscoring. This highlights ongoing problems with clients processing unnecessary data based on outdated information, an issue that potuz expects will worsen in the Gloas upgrade.

For the restart, stefanbratanov notes that Teku will need several more days to fix serving finalized payloads as part of the payload by range support, while 0xunclebill is verifying their implementation which was added last minute. potuz plans to spend remaining time fixing the uncovered bug and documenting the issue, noting that Lighthouse and Lodestar may have similar problems.

## Participants

- 0xunclebill
- potuz
- stefanbratanov
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T19:10:14.915000+00:00] terencechain: im also not against restarting devnet0 in a day or two when clients have fixed attestation committee index and support payload by range
it'll be nice to find the next bugs
[2026-03-04T19:14:39.854000+00:00] potuz: This already highlighted 2 bugs on Prysm: one we knew about and had already fixed (for some reason wasn't in the devnet branch), the other I am tracking down, but I expect the usual problem of having to process useless stuff based on old crap will become much worse in Gloas
[2026-03-04T19:14:48.717000+00:00] potuz: we should have never even tried to process Teku's block
[2026-03-04T19:15:03.451000+00:00] potuz: I keep saying this every once in a while
[2026-03-04T19:16:00.739000+00:00] terencechain: whats blocking us from merging that
[2026-03-04T19:16:19.473000+00:00] potuz: <@476250636548308992>  🙂
[2026-03-04T19:17:01.999000+00:00] potuz: Anyway I'm glad this was triggered: we declared that block invalid. I think it must have been valid
[2026-03-04T19:17:40.453000+00:00] potuz: but that explains everything else, on a small chain, with too few nodes, even declaring invalid a useless block kills you cause you downscore peers
[2026-03-04T19:24:07.104000+00:00] potuz: Anyway, I'll spend my remaining time fixing the bug this uncovered and documenting the issue, there may be issues also in LH and Lodestar that kept sending info from this branch
[2026-03-04T19:34:20.402000+00:00] stefanbratanov: by supporting payload by range, do you mean also serving finalized payloads? It will be another few days probably for Teku to fix serving those
[2026-03-04T19:39:57.799000+00:00] 0xunclebill: we should just be able to serve what we have but im double checking now. i added it last minute and thought it was working
```

</details>
