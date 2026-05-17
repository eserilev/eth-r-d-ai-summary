# Fork Choice Implementation and Message Slot Logic

**Channel:** epbs | **Date:** 2026-02-02

## Summary

This discussion centers around a pull request (#4892) by potuz that aims to improve the readability of fork choice implementation by replacing what appears to be an "impossible branch" with an assertion. The specific change involves replacing `if message.slot <= block.slot: return False` with `assert message.slot >= block.slot`.

The key technical debate focuses on the edge case where `message.slot == block.slot`. Kevaundray suggests the new `>=` assertion is correct based on existing spec requirements, while nc1234 argues it should be `assert message.slot > block.slot` since equal slots should only be handled by the preceding `PAYLOAD_STATUS_PENDING` check. However, fradamt points out a critical concern: when `message.slot == block.slot` but the payload status is not pending, the function should return `False` rather than trigger an assertion failure.

The discussion reveals disagreement about whether the original conditional was truly an "impossible branch" or if it served a legitimate purpose in handling the equal-slot scenario. No clear consensus was reached, leaving open the question of whether the change properly handles all edge cases or introduces potential assertion failures in valid scenarios.

## Participants

- fradamt
- kevaundray
- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-02T20:12:07.568000+00:00] potuz: Another easy one, I am reviewing forkchoice to annotate it and I think this makes it more understandable if it's right, I was asking myself why in the hell did we add that branch. https://github.com/ethereum/consensus-specs/pull/4892
[2026-02-02T20:56:05.344000+00:00] potuz: <@427491045308235776> I'll ask here instead of chatting in the PR, I did not understand a word of your comment
[2026-02-02T20:59:26.273000+00:00] kevaundray: The comment says it was removing the impossible branch:

⁨⁨```python
if message.slot <= block.slot:
  return False
```⁩⁩

for 

⁨⁨```python
assert message.slot >= block.slot
```⁩⁩

I was saying that this changes the behaviour when ⁨⁨`message.slot == block.slot`⁩⁩, implying that the previous behaviour of returning ⁨`False`⁩ was a bug
[2026-02-02T21:01:21.951000+00:00] nc1234: So we should `assert message.slot > block.slot` instead
[2026-02-02T21:03:40.548000+00:00] kevaundray: I believe the new >= is correct due to

⁨⁨```python
assert block_slot <= attestation.data.slot 
```⁩⁩

from

 https://github.com/ethereum/consensus-specs/blob/7e33b9f9de37f02e711aa534dcc72e9880e551e2/specs/gloas/fork-choice.md?plain=1#L562

I was just skimming the code fwiw, haven't looked at the implications
[2026-02-02T21:07:15.703000+00:00] nc1234: Hmmm, if `message.slot == block.slot`, then it should be supporting PENDING only and not EMPTY or FULL. The `if node.payload_status == PAYLOAD_STATUS_PENDING` prior to this check already covers this scenario. So we should only consider `message.slot > block.slot` here.
[2026-02-02T22:47:03.173000+00:00] fradamt: If `message.slot == block.slot` and `node.payload_status != PAYLOAD_STATUS_PENDING`, we should be returning `False`, not failing an assert
```

</details>
