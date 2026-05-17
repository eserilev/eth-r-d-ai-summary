# FCU Rollback and Late Payload Handling

**Channel:** epbs | **Date:** 2026-03-09

## Summary

Potuz raised a technical issue regarding FCU (forkchoiceUpdated) rollback functionality and late payload handling in Ethereum consensus clients. Currently, Prysm has complex logic to determine whether to send FCU when receiving new payloads that may warrant a reorg due to lateness. Potuz suggested that if FCU gains rollback capability, the implementation could be simplified by always sending the incoming payload but directing the FCU to the previous payload when HEAD returns empty, eliminating the need for different handling of late vs non-late payloads.

The discussion touched on how this relates to ePBS (enshrined Proposer-Builder Separation), where late blocks wouldn't typically include payloads, and the normal behavior would involve sending FCU to the parent payload expecting a payload ID back. Potuz noted this proposal likely won't make it into ACDT (presumably the current development timeline) and referenced an alternative approach in a GitHub PR. The discussion also included mentions of devnet-0 testing being initiated.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-09T15:01:48.527000+00:00] potuz: <@602753420033785856> <@360491619402776577> this will most probably not get into ACDT but I want to bring this up: today one of the trickiest paths on Prysm is the juggler's maneuvering to send or not send FCU when we receive a new payload that we may want to reorg because it's late. If we move into a situation in which FCU can actually rollback I would suggest to simply send the incoming payload but the FCU to the previous payload because HEAD returns empty. Thus not treating the late vs not-late situation differently. I think this is less of an issue on ePBS because if the block is late we don't expect to see the payload. Or even then the normal behavior would be to send FCU to the parent payload and expect a payload ID back.
[2026-03-09T15:02:27.167000+00:00] potuz: <@199561711278227457> Starting a thread on devnet-0 testing
[2026-03-09T15:34:08.281000+00:00] potuz: <@592004585506340885> this is the alternative https://github.com/ethereum/consensus-specs/pull/4992
[2026-03-09T15:34:53.304000+00:00] potuz: Left a self review
```

</details>
