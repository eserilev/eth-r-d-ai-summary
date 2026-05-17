# PTC Votes and Block State Validation

**Channel:** epbs | **Date:** 2025-12-18

## Summary

m.kalinin questioned why PTC (Proof-of-Custody Committee) votes need to be included in blocks if they don't modify state and are only being validated. potuz explained that including PTC votes in blocks allows the proposer to assert their view of the network state. Specifically, if there are sufficient votes indicating data is "missing," the proposer will be allowed to perform a reorganization, while enough "present" votes will canonically bind the proposer to build on top of the payload.

This mechanism addresses scenarios where attesters may not have received all data columns for the previous payload or all PTC votes, ensuring the proposer can make informed decisions about chain progression based on the committee's collective view of data availability.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-18T05:31:00.739000+00:00] m.kalinin: 2) Why do we need PTC votes in a block if they aren't modifying state anyhow but just being validated?
[2025-12-18T10:09:35.360000+00:00] potuz: The reason to put PTC votes in the block is so that the proposer can asset it's view. If there are enough votes say for missing then he will be allowed to reorg, if there are enough for present then he will be canonical binding on top of the Payload. Even if for example the attester hadn't received all columns for the previous payload for example and hadn't received all PTC votes
```

</details>
