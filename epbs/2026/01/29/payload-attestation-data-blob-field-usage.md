# PayloadAttestationData blob_data_available Field Usage

**Channel:** epbs | **Date:** 2026-01-29

## Summary

nero_eth questioned why the `blob_data_available` field in `PayloadAttestationData` appears unused in the current specifications, noting that there seems to be agreement on a dual deadline design with dynamic PTC availability checks based on payload size. They initially thought this field might be dead code that could be removed, suggesting it could potentially be merged with the `payload_data_available` field into a single `payload_and_blob_data_available` field.

potuz clarified that the forkchoice implementation is not yet complete and argued that the separate `blob_data_available` field is needed because it's cheap to maintain and allows for independent thresholds between payload and blob data availability. They referenced an explainer document that provides more details on this design rationale.

The discussion leaves open the question of how exactly this field will be utilized in the final implementation, with the design apparently still evolving as the forkchoice logic is completed.

## Participants

- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T10:12:54.063000+00:00] nero_eth: Why is `blob_data_available` field in `PayloadAttestationData` unused in the specs? Seems like there's agreement on the dual deadline with dynamic PTC availability checks depending on the payload size, and this blob availability PTC check would be required to have a later deadline for blobs. Am I missing something?
[2026-01-29T10:14:58.373000+00:00] nero_eth: Nvm, I think the dual deadline design woudn't even require to have that field, and having the PTC vote at the later of the two deadlines is fine.
So, is it just dead code that can be removed?
[2026-01-29T10:46:06.732000+00:00] potuz: Forkchoice is not complete yet
[2026-01-29T10:47:32.748000+00:00] nero_eth: Is this field even needed or can this just be handled with the payload_data_available field. Maybe renaming it to `payload_and_blob_data_available`.
[2026-01-29T10:48:57.747000+00:00] potuz: It's needed IMO: it's cheap and it allows to have independent thresholds. I explained this situation in the explainer linked above
```

</details>
