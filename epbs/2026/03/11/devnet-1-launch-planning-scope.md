# Devnet-1 launch planning and scope

**Channel:** epbs | **Date:** 2026-03-11

## Summary

The discussion centers around whether to launch epbs-devnet-1 as planned for March 18th or continue testing on the current devnet-0. Barnabasbusa proposed launching devnet-1 with the scope defined in the ethpandaops notes, requiring PR #4992 to be merged within days and a new consensus layer spec release by end of week. The main motivation is to prevent the changeset between devnet-0 and devnet-1 from becoming too large.

Potuz argued against launching a new devnet when clients like Teku and Nimbus can't even sync with devnet-0 yet, and when existing issues like withdrawals, exits, and PTC attestations packing remain unresolved. He advocated for continuing to test current failing structures on devnet-0 until they're fixed. Tersec supported this position, noting that maintaining multiple specs simultaneously creates unjustifiable costs for client teams.

The discussion ended without clear consensus, with jtraglia asking clients to choose between continuing devnet-0 work or proceeding with devnet-1. Key unresolved issues include client sync problems on the current devnet and the trade-off between iterating on existing problems versus managing changeset complexity across devnet versions.

## Participants

- barnabasbusa
- jtraglia
- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T15:47:28.146000+00:00] barnabasbusa: epbs-devnet-1 scope just dropped: https://notes.ethereum.org/@ethpandaops/epbs-devnet-1

We should aim to have https://github.com/ethereum/consensus-specs/pull/4992 merged in the next day or two, have new cl spec release by EOW and launch devnet 1 next wed on the 18th of March. Lets try to hit these deadlines!
[2026-03-11T15:50:44.094000+00:00] potuz: I don't see anything there, what are we testing in that devnet?
[2026-03-11T15:51:55.687000+00:00] potuz: can't we continue using this one until it doesn't give more? we can test existing structures on this one until we have them fixed, what' s the point of launching a new devnet when we can still use this one for unresolved issues, it's not like we have a hardfork. The only one would be the PTC attestations packing issue, which yes, we should launch a new devnet if this one breaks
[2026-03-11T15:52:47.512000+00:00] barnabasbusa: pretty sure there are a few consensus breaking changes in the new devnet
[2026-03-11T15:52:56.583000+00:00] barnabasbusa: that would make devnet 0 break
[2026-03-11T15:55:00.428000+00:00] potuz: there's the PTC lookbehind is the only one I can think of
[2026-03-11T15:55:15.025000+00:00] barnabasbusa: https://github.com/ethereum/consensus-specs/pull/4948
[2026-03-11T15:55:27.840000+00:00] barnabasbusa: oh Justin just told me that this would be forkchoice change not consensus change
[2026-03-11T15:55:35.049000+00:00] barnabasbusa: soz
[2026-03-11T15:55:44.453000+00:00] potuz: but again I think my original plan in https://discord.com/channels/595666850260713488/1480581540655399095/1480582319541715197 is much more cost-effective and we can iterate over this
[2026-03-11T15:56:02.194000+00:00] potuz: it does not make sense to launch a new devnet when Teku and Nimbus can't even sync yet
[2026-03-11T15:56:31.823000+00:00] potuz: lets continue testing on devnet-0 all the failing current structures (withdrawals, exits, etc). When we fix those lets try PTC attestations until we split
[2026-03-11T15:56:40.834000+00:00] potuz: and then we can just scrape this one off
[2026-03-11T15:58:16.485000+00:00] jtraglia: Can we do both? Keep devnet-0 & start working on devnet-1 for clients that are ready for that?
[2026-03-11T15:58:42.716000+00:00] barnabasbusa: the main reason I wanna actually push for devnet 1 is to make sure that the change set between devnet 0 and devnet 1 won't get too big
[2026-03-11T15:59:14.090000+00:00] barnabasbusa: we can keep acknowledge the limitations, and keep testing everyting on devnet 1.
[2026-03-11T16:00:23.508000+00:00] barnabasbusa: also ideally all these should be ironed out by next tuesday
[2026-03-11T16:00:25.540000+00:00] potuz: but no client is ready for devnet-0 yet, we fail in Kurtosis with the current branches
[2026-03-11T16:00:30.441000+00:00] barnabasbusa: so we can focus on the new changeset from wed on
[2026-03-11T16:01:16.235000+00:00] tersec: also in general supporting multiple specs at once is a hard-to-justify cost
[2026-03-11T16:01:23.456000+00:00] potuz: ok, whatever you guys say, I'll continue my testing on this devnet so please keep it up, this devnet is useful and I can' t even think of having separate branches with different specs to be testing on separate devnets
[2026-03-11T16:01:31.627000+00:00] potuz: jinx
[2026-03-11T16:09:43.857000+00:00] jtraglia: Okay so no to doing both. That's fine. What do clients prefer?
* Continue working on devnet-0.
* Bail on devnet-0 & start devnet-1 with new changes.
```

</details>
