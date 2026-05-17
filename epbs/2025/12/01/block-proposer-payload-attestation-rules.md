# Block proposer payload attestation construction rules

**Channel:** epbs | **Date:** 2025-12-01

## Summary

nc1234 asked about the block proposer payload attestation construction rules, specifically whether a proposer at slot n should include zero payload attestations when not building on an n-1 block (such as when slot n-1 has no beacon block). They referenced the specification requirement that "the slot of the parent block is exactly one slot before the proposing slot."

potuz confirmed this interpretation, stating that proposers should indeed include zero payload attestations in such cases. He explained that including payload attestations for previous blocks would be useless since they aren't penalized or rewarded anyway, providing clarity on the expected behavior when there are gaps in the block chain.

## Participants

- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-01T23:15:53.030000+00:00] nc1234: Regarding block proposer constructing payload attestation https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/validator.md#constructing-payload_attestations 
> The slot of the parent block is exactly one slot before the proposing slot.
Does this mean if proposer at slot n is not building on n - 1 block (could be n - 1 slot has no beacon block), proposer should include 0 payload attestation in its block body right?
[2025-12-01T23:32:38.954000+00:00] potuz: Yes. It's useless to include payload attestations for previous blocks and they aren't penalized/rewarded anyway
```

</details>
