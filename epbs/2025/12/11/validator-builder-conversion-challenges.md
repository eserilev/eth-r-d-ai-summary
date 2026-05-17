# Validator to builder conversion technical challenges

**Channel:** epbs | **Date:** 2025-12-11

## Summary

jtraglia identified a technical challenge with converting 0x03 validators to builders, specifically that allowing duplicate public keys across `state.builders` and `state.validators` would break the current logic for distinguishing between the two types. The main issue arises during deposits and withdrawals - when processing a transaction for a given public key, the system wouldn't know whether to route it to the validator or builder instance.

Currently, the implementation prevents creating builders with the same public key as existing validators and vice versa. When potuz questioned why this restriction is necessary since validators and builders exist in different slices, jtraglia explained that the ambiguity during deposit/withdrawal operations makes it cleaner to maintain unique public keys across both types.

The discussion leaves open the question of whether this restriction should be maintained or if there's an alternative approach to handle the deposit/withdrawal routing problem that would allow validator-to-builder conversion.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-11T21:00:00.889000+00:00] jtraglia: I don't think it would be easy to convert from 0x03 validators to builders.
* The biggest issue is that there would be a duplicate pubkey in `state.builders` and `state.validators`. This breaks my current logic on telling them apart.
[2025-12-11T21:00:53.410000+00:00] jtraglia: Currently, I've written it so that you cannot make a builder with the same pubkey as a validator. And vice versa.
[2025-12-11T21:01:37.483000+00:00] potuz: Why is that needed? They are on different slices
[2025-12-11T21:02:27.535000+00:00] jtraglia: For deposits & withdrawals. You're given 1 ETH for `pubkeyA`. Is `pubkeyA` a validator or builder?
[2025-12-11T21:02:41.390000+00:00] jtraglia: If there's a duplicate, which do you deposit to?
[2025-12-11T21:03:34.938000+00:00] jtraglia: It's just cleaner if there's a rule there.
```

</details>
