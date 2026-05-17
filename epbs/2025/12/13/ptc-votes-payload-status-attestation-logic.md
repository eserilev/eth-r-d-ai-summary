# PTC votes and payload status attestation logic

**Channel:** epbs | **Date:** 2025-12-13

## Summary

This discussion examines the relationship between PTC (Payload Timeliness Committee) votes and payload status attestation logic in Ethereum's consensus mechanism. nc1234 explains that when an attester votes for a head block in the current slot, they don't need to indicate payload status since it can be derived from PTC votes within that block's body. However, when voting for a head block from previous slots (indicating a missed block), attesters must provide payload status since no beacon block means no PTC votes are available.

potuz challenges this interpretation, clarifying that PTC votes in a block body only refer to the previous payload's timeliness, not the current one. They emphasize that attesters must validate the payload they're voting on and attest to its validity, with the index set to zero when referring to future payloads that attesters couldn't have observed yet. The discussion leaves open questions about the fork choice implications of this logic.

## Participants

- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-13T00:09:41.715000+00:00] nc1234: This basically says if the head block you are voting for is this slot, then you as a attester does not need to indicate the payload status, because payload status can be obtained by PTC votes in the block body that you are voting for.
But if you are voting for a head block that is in previous slots, you are basically saying there is a missed block in the current slot, and payload status cannot be derived because no beacon block = no PTC votes. Now you as an attester need to step in to attest for the payload status. 
Don't know about fork choice
[2025-12-13T00:19:13.336000+00:00] potuz: The first paragraph here doesn't make much sense to me. The PTC vote in the body you're voting for is only for timelines of the previous Payload. You need to validate that payload and attest to it's validity. The index is set to zero because it's referring to a future payload that the attester couldn't have seen
```

</details>
