# Attestation data index specification clarification

**Channel:** epbs | **Date:** 2025-12-12

## Summary

A user (povilas8490) raised a question about the attestation data index specification, specifically regarding the rule that when `block.slot == current_slot`, validators should always set `data.index = 0`. They're concerned this means validators attesting to blocks produced in the same slot during propose duty will always use index 0, potentially giving blocks without payloads higher weight in fork choice since `data.index = 0` typically signals payload absence.

The question highlights a potential confusion in the specification where the same index value (0) is used for two different scenarios: same-slot attestations and signaling empty payload status. The user is seeking clarification on whether this creates an unintended bias in fork choice where nodes without payloads would receive greater weight than those with payloads.

This remains an open question awaiting response from other developers, as no replies were included in the discussion messages provided.

## Participants

- povilas8490

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T12:02:48.847000+00:00] povilas8490: Hello, I have trouble understanding the spec, please help me out:

```
*    If block.slot == current_slot (i.e., data.slot), then always set data.index = 0.
*    Otherwise, set data.index based on the payload status in the validator's fork-choice:
        Set data.index = 0 to signal that the payload is not present in the canonical chain (payload status is EMPTY in the fork-choice).
        Set data.index = 1 to signal that the payload is present in the canonical chain (payload status is FULL in the fork-choice).

```
(https://github.com/ethereum/consensus-specs/blob/v1.6.1/specs/gloas/validator.md#attestation)

Does that mean that when validators attest a block produced in the same slot during propose duty, attestation `data.index` will always be 0?

If so, does that mean in fork choice during head selection nodes without a payload usually will have bigger weight than nodes with payload?

What am I missing here?
```

</details>
