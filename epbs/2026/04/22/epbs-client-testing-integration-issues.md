# ePBS short-term testing requirements and scenarios

**Channel:** epbs | **Date:** 2026-04-22

## Summary

Parithosh requested clarification on the short-term testing requirements for ePBS (enshrined Proposer-Builder Separation) from potuz. Potuz responded that they are working on a comprehensive list but identified the immediate priority tests focusing on reorged payloads scenarios.

The short-term testing scenarios include: range sync when a payload (not the block) was reorged, checkpoint sync when slot 0 was missed and the latest block 31 is either full or empty, and attempting to reorg a payload to verify that the block gets reorged as expected. These tests cover the most common cases that the chain should be able to handle for proper operation and quick restart capabilities.

Potuz indicated they will provide a separate list of additional scenarios for more comprehensive "hardness" testing beyond these initial short-term requirements.

## Participants

- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-22T10:04:21.477000+00:00] parithosh: What were the short term tests you wanted us to have ready for ePBS <@755590043632140352> ?
[2026-04-22T10:07:12.720000+00:00] potuz: Yeah working on the list but the short ones will be mostly around reorged Payloads:

Range sync when a payload was reorged (not the block) 

Checkpoint sync when slot 0 was missed and the latest block 31 is both full or empty. 

Attempt to reorg a payload and see that the block is reorged
[2026-04-22T10:08:24.136000+00:00] potuz: Those cover the most common cases that the chain should have right now to be able to work and quickly restart. The rest I'll send you a list of scenarios that we should test for hardness
```

</details>
