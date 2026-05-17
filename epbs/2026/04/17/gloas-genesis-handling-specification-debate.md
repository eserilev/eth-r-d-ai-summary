# Gloas Genesis Handling Specification Debate

**Channel:** epbs | **Date:** 2026-04-17

## Summary

The discussion centers around PR #5067 regarding whether to formally specify Gloas genesis handling in the Ethereum consensus specs. jtraglia argues that specification is necessary because clients need to handle genesis scenarios for devnets and Kurtosis testing, and there's a technical issue where having 0x00 in the genesis state doesn't fully resolve problems with bid processing - specifically when block at slot 1 has a bid with parent_hash = 0x00, making the genesis slot appear "full" due to an assert in bid processing.

potuz strongly opposes adding this to the specification, arguing that devnet handling doesn't require formal specification and that it would make the main Ethereum spec less readable without sufficient justification. He suggests they could simply accept that the first block is empty or generate the genesis state so that parentIsFull returns false. tbenr supports a similar approach of configuring the genesis state appropriately rather than adding specification complexity.

The discussion remains unresolved, with jtraglia indicating that no release will proceed until this specification question is decided. He mentions that another contributor (Sproul) considers this specification important and suggests waiting for their input to potentially break the deadlock between the competing viewpoints.

## Participants

- brechy
- jtraglia
- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-17T16:14:54.844000+00:00] jtraglia: <@&1417820113981145228> need more reviews on this PR:
https://github.com/ethereum/consensus-specs/pull/5067
Sorry to keep pinging everyone.
[2026-04-17T16:29:28.241000+00:00] potuz: Do we really want this?
[2026-04-17T16:35:17.423000+00:00] jtraglia: Yes. I now think it's important to clarify.
[2026-04-17T16:35:19.591000+00:00] jtraglia: 
[2026-04-17T16:35:55.117000+00:00] jtraglia: If clients are expected to handle gloas genesis, we need to specify this.
[2026-04-17T16:38:30.666000+00:00] jtraglia: There will not be a release until this situation is resolved; to spec this or not.
[2026-04-17T16:42:41.922000+00:00] potuz: Why would clients need to ever handle genesis in Gloas?
[2026-04-17T16:42:50.003000+00:00] jtraglia: Devnets
[2026-04-17T16:43:01.601000+00:00] jtraglia: Kurtosis
[2026-04-17T16:43:06.936000+00:00] potuz: We can do that, no need to specify for devnets
[2026-04-17T16:43:41.226000+00:00] potuz: But even if we do, why not just leave things as is and accept that the first block is empty
[2026-04-17T16:44:22.979000+00:00] jtraglia: https://github.com/ethereum/consensus-specs/issues/5043#issuecomment-4152212725
[2026-04-17T16:44:28.536000+00:00] potuz: None of this should be specified IMO it makes the actual Ethereum spec less readable for no reason
[2026-04-17T16:44:48.294000+00:00] jtraglia: > I actually don't think having 0x00 in the genesis state completely solves it. If the block at slot 1 has a bid with parent_hash = 0x00 as well, then we're back to the same situation where the genesis slot seems "full". I think this is fundamentally required because of this assert in bid processing.
[2026-04-17T16:45:13.486000+00:00] potuz: Anyway, I've already wasted more time than the issue warrants so I'm fine if people want to merge this or not
[2026-04-17T16:45:24.963000+00:00] tbenr: cant we just generate the state so that that parentIsFull returns false?
[2026-04-17T16:45:34.214000+00:00] potuz: My preference is for not
[2026-04-17T16:46:23.534000+00:00] jtraglia: This is sort of what <@808969530608451584> suggested here:
https://github.com/ethereum/consensus-specs/pull/5067#issuecomment-4193354606
[2026-04-17T16:47:35.031000+00:00] jtraglia: But this is also confusing because the genesis block's parent is not full.
[2026-04-17T16:49:25.879000+00:00] jtraglia: I understand/agree with both sides of this argument. I think we need to give <@602753420033785856> time to respond. He might be able to convince you of its value or be convinced that it's not truly necessary.
[2026-04-17T16:50:11.758000+00:00] jtraglia: But it's clear to me that Sproul thinks it's important to spec this.
[2026-04-17T18:19:23.384000+00:00] brechy: My preference is for not
```

</details>
