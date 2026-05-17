# Proposer Boost Reorg Simplification

**Channel:** epbs | **Date:** 2026-04-27

## Summary

nc1234 proposed simplifying the proposer boost reorg mechanism following recent changes to the forkchoiceUpdated (fcU) call referenced in PR #5174. The suggestion received positive reception from multiple participants.

sproul provided context that the original restriction existed because the execution layer (EL) could be ahead of the consensus layer (CL), making fcU calls appear like reversions. They suggested a potential solution of stopping fcU calls during finalized sync and referenced a GitHub issue for additional background. Both sproul and jtraglia expressed support for the simplification.

potuz noted that this change has already been implemented, suggesting the proposed simplification may have already been adopted. No specific action items or open questions were explicitly identified in the discussion.

## Participants

- jtraglia
- nc1234
- potuz
- sproul

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-27T10:15:48.241000+00:00] nc1234: I want to see if there is any appetite to simplify the proposer boost reorg due to the recent change to fcu https://github.com/ethereum/consensus-specs/pull/5174 cc. <@592004585506340885> <@602753420033785856> <@520034910149410861> <@755590043632140352> <@425572898787426305>
[2026-04-27T10:42:17.459000+00:00] sproul: heck yeah, I dig it
[2026-04-27T10:42:38.683000+00:00] sproul: one of the original reasons that restriction existed was that the EL could be ahead of the CL, in which case the fcUs look like reversions
[2026-04-27T10:42:54.125000+00:00] sproul: but maybe we just stop sending fcU during finalised sync?
[2026-04-27T10:43:03.545000+00:00] sproul: (for reference: <https://github.com/ethereum/execution-apis/issues/313#issuecomment-1316648829>)
[2026-04-27T11:13:08.796000+00:00] jtraglia: Nice. Yes I like this too.
[2026-04-27T11:26:46.284000+00:00] potuz: We already did this btw
```

</details>
