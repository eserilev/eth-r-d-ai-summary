# Builder staking requirements and BLS key verification

**Channel:** epbs | **Date:** 2025-11-24

## Summary

Etan_status raised concerns about a proposed change to remove staking requirements for builders while including BLS public keys. The main security issue is that such keys would lack proof of possession, making them unsafe to aggregate with other keys during signature verification - a critical problem since aggregation is typically used to accelerate verification processes.

As an alternative solution, they suggested requiring builders to use "any active non-slashed validator index" instead. This approach would provide both proof of possession (enabling safe aggregation) and accountability mechanisms if a payload isn't revealed, addressing the security concerns while maintaining the desired functionality.

## Participants

- etan_status

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-11-24T01:20:44.708000+00:00] etan_status: btw, someone mentioned a pr to me where the idea was to not require builder to be staked.

if we do that and put a blspubkey in there, care must be taken to verify that sig individually. as that key doesnt necessarily have proof of posession, aggregating it with other keys (to accelerate sig verification) may be nonsecure.

alternatively, one could require “any active nonslashed validator index”, those have proof of posession (ie can be aggregated safely) and provide accountability if a payload isnt revealed
```

</details>
