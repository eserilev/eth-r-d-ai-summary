# Prysm-Lighthouse Interop and Payload Reorg Issues

**Channel:** epbs | **Date:** 2026-02-28

## Summary

The discussion centers on interoperability issues between Prysm and Lighthouse clients during testing, specifically related to payload reorganization handling. Potuz identified that Prysm had a bug causing it to produce blocks that reorg payloads - while these blocks are technically valid and accepted by other Prysm nodes, Lighthouse was rejecting them. The key uncertainty was whether Lighthouse can properly process payload reorgs, with Potuz seeking clarification on this capability and whether similar issues occur with Lodestar.

After fixing the initial Prysm bug, the interop continued but then Lighthouse began experiencing its own reorg issues, making it unclear which client was at fault. Despite these payload-related problems, Potuz noted significant progress compared to previous interop attempts, specifically praising the test vectors work that eliminated root mismatches that had previously caused days or weeks of debugging with Teku.

The main open question remains determining Lighthouse's payload reorg processing capabilities and identifying the root cause of the subsequent Lighthouse reorg behavior after the Prysm fix was applied.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-28T20:43:00.149000+00:00] potuz: Hey <@358120958726373381> I have some Lighthouse logs from interop, can I share them with you to see what's wrong? Prysm has a bug on it's own and produced a block reorging a payload, the block should not be head, but it's a valid block. Lighthouse is rejecting it. I haven't really dug deep to really check if Prysm's block is actually valid (other prysm nodes do accept it with the payload reorg)  but I'll focus on fixing the previous bug before
[2026-02-28T20:45:46.591000+00:00] potuz: So my biggest question here is if you guys can actually process payload reorgs and if you've tried this with Lodestar yet, if you do then surely this is our bug and it narrows where I should be looking at
[2026-02-28T20:55:14.421000+00:00] potuz: Ok, I fixed Prysm's bug and we were fine for a while but Lighthouse started reorging itself, so not sure who's at fault here 🙂
[2026-02-28T21:17:48.501000+00:00] potuz: Prysm <---> Lighthouse Interop
[2026-02-28T21:29:09.027000+00:00] potuz: Kudos <@592004585506340885> and people that worked on testvectors: we didn't have a single root mismatch on our first interop this time (we wasted days/weeks with Teku because of this before). And can focus on actual client bugs
```

</details>
