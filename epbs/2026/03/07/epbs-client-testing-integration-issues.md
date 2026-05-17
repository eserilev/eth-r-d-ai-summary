# EPBS devnet-0 payload reorg investigation and fixes

**Channel:** epbs | **Date:** 2026-03-07

## Summary

Potuz identified a payload reorg issue in EPBS devnet-0, specifically investigating why a block at slot 17366 proposed by Prysm should have reorged the previous payload but didn't. They initially requested status checks on the `epbs-devnet-0-sync` branch and attester behavior, but then quickly realized the likely cause was hardcoded logic that makes payloads extending the head automatically become the new head.

Nflaig responded that the latest `epbs-devnet-0` branch should have this issue fixed, along with other previously mentioned fixes. They recommended deploying the updated version to only one node initially as a cautious rollout approach. The investigation appears to have identified the root cause and a potential solution is ready for testing.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-07T00:53:48.511000+00:00] potuz: <@884836500037066862> can you check the status of the branch `epbs-devnet-0-sync` and tell me how attesters are attesting? does it have your PR? this block https://dora.epbs-devnet-0.ethpandaops.io/slot/17366  that Prysm proposes should have reorged the previous payload. I'm not sure why it doesn't. <@363800010518822915> same question for you, perhaps the proposer path is outdated? We should have logs for this one, will look tomorrow, leaving this note here for myself.
[2026-03-07T00:55:17.579000+00:00] potuz: Ah no, nvm, I think we hardcoded that payloads that extend head become our head. We'll look into this later.
[2026-03-07T02:17:29.084000+00:00] nflaig: latest `epbs-devnet-0` should have that fixed, also includes fixes mentioned earlier, I suggest to deploy that to one node only first
```

</details>
