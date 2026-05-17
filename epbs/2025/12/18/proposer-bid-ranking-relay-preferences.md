# Proposer Preferences Consensus Specs Review

**Channel:** epbs | **Date:** 2025-12-18

## Summary

jtraglia requested a review of a proposer preferences consensus specs change from potuz. Potuz identified an issue with the `MAXIMUM_GOSSIP_CLOCK_DISPARITY` allowance reference for proposal slots, noting that it doesn't matter since the proposal slot is deterministic based on the slot and state rather than being clock-dependent.

jtraglia acknowledged the feedback and implemented a fix in a follow-up commit. Potuz approved the rest of the PR, indicating the changes look good to merge with the clock disparity issue resolved.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-18T02:15:41.158000+00:00] jtraglia: <@755590043632140352> regarding proposer preferences, could you review this change?
https://github.com/ethereum/consensus-specs/pull/4777/commits/079686cf6bab4354cf7f0177ef473ed721f2e71e
[2025-12-18T15:20:30.702000+00:00] potuz: Apologies can login to github now, but   `MAXIMUM_GOSSIP_CLOCK_DISPARITY` allowance) -- i.e.` for the proposal slot doesn't matter since it's deterministic on the slot and the state, there's no clock for this
[2025-12-18T15:21:33.279000+00:00] potuz: the rest of the PR LGTM
[2025-12-18T15:34:36.629000+00:00] jtraglia: Thanks for the review. You're right. Fixed that here: https://github.com/ethereum/consensus-specs/pull/4777/commits/3978a2a632a2f8c72c55f8488c3629a25dec68d0
```

</details>
