# PTC Payload Timely Check Implementation Changes

**Channel:** epbs | **Date:** 2026-02-06

## Summary

Potuz proposes modifying the `is_payload_timely` check in the PTC (Payload Timely Check) implementation to include both timeliness and availability conditions. This change would move the responsibility for reorgs from the PTC to the proposer, making the system more permissive by allowing proposers to reorg when data arrives late rather than forcing them to do so through PTC assertions.

The proposed implementation involves moving an assertion from outside to inside the last return clause, as demonstrated in a linked commit. Potuz suggests that with this change, honest proposer behavior should involve building on empty blocks when the PTC votes against data availability (even if timely) and the proposer only has partial data. However, for payload scenarios, proposers should build on full blocks if they received the payload timely, even when the PTC voted it wasn't timely but was available.

Terencechain agrees with this approach, noting it aligns with their original understanding of the intended behavior. The discussion leaves the asymmetric handling of payload scenarios as a topic for future discussion.

## Participants

- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-06T00:44:48.121000+00:00] potuz: Ok <@520034910149410861> I think I know what change would make you happy. I think if I change the `if is_payload_timely` check to both timely and available by the PTC then it would work. In this way the PTC will still not force the proposer to reorg and if the data came late it will allow the proposer to reorg
[2026-02-06T00:45:24.477000+00:00] potuz: Will that work for you? Just move the assertion from outside to inside the last return clause
[2026-02-06T00:54:28.057000+00:00] potuz: Like in [this commit](https://github.com/ethereum/consensus-specs/pull/4884/changes/d055ccef9ac9c8ea9fbd2e879e03a309909a1dcb).  This moves the responsibility to the proposer to reorg instead of the PTC so is more lax. However, with this change I think honest proposer behavior should be to choose to build on empty if the PTC has voted against data being available but timely and the proposer has it's share of the data but it's not a super node. 

I don't think the situation with the payload is symmetric. I believe the proposer should build on full if the ptc has voted that the payload wasn't timely but was available and the proposer saw the payload timely. But anyway this is for a different discussion, I believe this change is more in line with what you wanted
[2026-02-06T01:09:40.057000+00:00] terencechain: ya that makes sense to me, and is what i had originally thought we were doing
```

</details>
