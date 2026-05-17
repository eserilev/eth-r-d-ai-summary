# EIP Review and Documentation

**Channel:** epbs | **Date:** 2025-12-15

## Summary

The discussion centers around review of a consensus specs pull request (ethereum/consensus-specs/pull/4788) that is identified as top priority for Gloas implementation. jtraglia initially sought feedback from the team and forwarded the discussion to another channel for broader input.

A key decision was made to abandon "option #1" due to complexity concerns, with jtraglia closing that PR despite being its main proponent. The conversation also addressed procedural matters around EIP reviews, specifically how to handle trivial PRs from "airdrop farmers" - with consensus that such simple fix attempts should be ignored and closed by maintainers.

The discussion concludes with plans to update the EIP with a "high level version of the new thinking" and add jtraglia as a co-author, indicating the team is moving forward with a simplified approach to enable implementation and iteration to begin.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T16:07:22.959000+00:00] jtraglia: <@&1417820113981145228> please share your thoughts on the following:
https://github.com/ethereum/consensus-specs/pull/4788
This is the top priority for Gloas. We need to decide on this to start implementing/iterating.
[2025-12-15T17:26:37.242000+00:00] jtraglia: Forwarded this to the <#692078615269212180> channel.
[2025-12-15T17:30:40.012000+00:00] jtraglia: I'm going to close the PR for option #1. I'm the main proponent for it & I agree that its complexity probably isn't worth it.
[2025-12-15T18:17:05.586000+00:00] potuz: A question of form, 1) I am ignoring these https://github.com/ethereum/EIPs/pull/10920#issuecomment-3654319881, is this right?
2) I think you should be added as author <@592004585506340885>
[2025-12-15T19:00:05.316000+00:00] jtraglia: 1) I don't think you should ignore eth-bot's review requests 😅
2) Not necessary, I'm just helping. But if you insist, I'm fine with that.
[2025-12-15T19:01:35.234000+00:00] potuz: By 1) I mean trivial changes like these, we do this in our repo to prevent airdrop farmers, but I have no vote on the EIP repo so not sure if it's correct behavior. I left comments saying that I'd close these types of PRs before, but the bot (and no one) closes them
[2025-12-15T19:07:59.136000+00:00] jtraglia: Ah I see. Yes please ignore PRs from airdrop farmers with simple fixes like that.
[2025-12-15T19:08:12.669000+00:00] jtraglia: A maintainer should probably close that PR.
[2025-12-15T19:08:59.165000+00:00] potuz: I'll update the EIP with the high level version of the new thinking, and add you then as an author
```

</details>
