# Block Gossip Validation for Missing Payload Handling

**Channel:** epbs | **Date:** 2026-02-13

## Summary

terencechain identified a potential gap in the Ethereum p2p specification regarding block gossip validation. Specifically, they noted that there doesn't appear to be a defined validation rule for handling blocks when the node hasn't seen the payload that the block builds on top of. In such cases, the typical implementation behavior would be to IGNORE the block, request the missing payload by root, and potentially queue the block for later processing.

jtraglia confirmed that this condition is indeed missing from the current specification. While they agreed that the described behavior (ignoring and requesting the missing payload) sounds reasonable, they expressed uncertainty about whether ignoring the block is the optimal approach, leaving this as an open question that may need further discussion or specification clarification.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-13T17:06:10.129000+00:00] terencechain: Hello, I cant find the following line in gossip check for `beacon_block`
What happens if i receive a block that i have not seen the paylaod the block built on top of
In that case, I will `IGNORE` and request the payload by root and may queue the block
Are we missing that line to put in the p2p spec or i am missing something else
[2026-02-13T17:16:39.187000+00:00] jtraglia: You're right that such a condition does not currently exist in the spec.
[2026-02-13T17:17:03.478000+00:00] jtraglia: That behavior sounds correct to me, but I'm not completely certain we want to ignore it.
```

</details>
