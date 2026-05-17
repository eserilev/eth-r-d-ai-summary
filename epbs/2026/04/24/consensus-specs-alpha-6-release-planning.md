# Consensus Specs Alpha 6 Release Planning

**Channel:** epbs | **Date:** 2026-04-24

## Summary

The team is considering releasing a new alpha.6 version of the Ethereum consensus specs that would include several new and fixed tests along with PR #5152, which contains a small consensus-breaking change. The proposed release would incorporate all changes made since the alpha.5 release.

However, there are concerns about timing the release appropriately. The team wants to wait until a genesis handling issue (#5149) is resolved before proceeding with the release. Additionally, there's uncertainty about whether to wait a few more days, as potential issues during upcoming interop testing might necessitate another release anyway.

The discussion leaves open the question of optimal timing, with the acknowledgment that multiple releases this week might be necessary depending on how issues unfold during interop testing.

## Participants

- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-24T21:49:03.098000+00:00] jtraglia: <@586161934425128960> suggested maybe making a new alpha.6 release which contains several new/fixed tests and [5152](https://github.com/ethereum/consensus-specs/pull/5152) which is a (small) consensus breaking change. [Here](https://github.com/ethereum/consensus-specs/compare/v1.7.0-alpha.5...master) is the full list of changes since the alpha.5 release. I think we should at least wait until [the genesis handling issue](https://github.com/ethereum/consensus-specs/issues/5149) is resolved (cc <@1019999229151821936>, <@602753420033785856>, <@504202741933932544>). Also, there's a chance we run into some issue which requires a release at the beginning of the interop. Not sure if we should wait a few more days or not. Worst case we make a few releases this week.
```

</details>
