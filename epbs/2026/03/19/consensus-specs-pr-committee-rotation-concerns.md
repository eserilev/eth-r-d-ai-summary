# Consensus specs PR committee rotation concerns

**Channel:** epbs | **Date:** 2026-03-19

## Summary

nflaig raised concerns about PR #5020 in the consensus specs repository, arguing that the current approach to committee rotation is suboptimal. They criticized the proposal as being neither sufficient to allow clients to remove their external PTC (presumably Participation Tracking Committee) cache nor representing a minimal approach to reducing state size and updates.

The main technical issue highlighted is that rotating committees each slot just to handle epoch boundary cases results in unnecessary rotations - specifically doing it 31 times when most of those rotations serve no purpose. This suggests the current proposal introduces computational overhead without proportional benefits for the edge case it's designed to address.

## Participants

- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-19T12:35:48.213000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/5020 I don't really like the current spec PR as it's neither sufficient to allow clients to remove their external ptc cache while at the same time it's not the minimal approach we can use to reduce state size and updates, rotating committees each slot just to handle the epoch boundary case means we do it 31 times for no reason
```

</details>
