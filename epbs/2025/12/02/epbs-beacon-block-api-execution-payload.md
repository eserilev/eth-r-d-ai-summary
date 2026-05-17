# ePBS blob_data_available boolean specification

**Channel:** epbs | **Date:** 2025-12-02

## Summary

The discussion centers on a missing specification for the `blob_data_available` boolean field in ePBS (enshrined Proposer-Builder Separation) `PayloadAttestationData`. Bharath initially questioned how this field should be set, suggesting it should be true when validators in the PTC committee have sampled all required columns, essentially when `is_data_available(signed_beacon_block)` returns true. He raised PR #4769 to address this specification gap.

Stefan clarified that the omission was intentional, noting that this field relates to dual deadline functionality where blobs can take longer to arrive than the payload, and that defaulting to false seems logical pending further testing and analysis. Potuz confirmed the intentional nature of the omission, explaining it occurred during the rebase on top of Fulu to avoid conflicts with concurrent changes.

While Potuz supports merging the current PR, he emphasized that the specification will need updates when dual timelines are formally implemented. The expected setup involves separate deadlines for payload and data availability, where `blob_data_available` would be set to true only after passing validation within the designated timeline. The team agreed to defer the complete specification until the dual deadline mechanism is fully designed.

## Participants

- bharath.vedartham
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-02T07:19:57.596000+00:00] bharath.vedartham: the ePBS spec doesn't clarify how the `blob_data_available` boolean(https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/beacon-chain.md?plain=1#L157)  is set in `PayloadAttestationData` ? I assume it would be set to true, if the validator part of the PTC commitee has sampled all the columns required by it? Basically set to true if `is_data_available(signed_beacon_block)` (https://github.com/ethereum/consensus-specs/blob/master/specs/fulu/fork-choice.md?plain=1#L22) returns true?
[2025-12-02T08:26:21.839000+00:00] bharath.vedartham: raised a PR: https://github.com/ethereum/consensus-specs/pull/4769, happy to discuss it and close the PR if not specifying setting `blob_data_avaiable` was intentional
[2025-12-02T13:39:02.848000+00:00] stefanbratanov: Remember I asked that question and <@363800010518822915> replied "I don't think that part is spec yet but it's for dual deadline so blobs can take longer to arrive. Pending testing and further analysis" so just default it to false seems the logical one to me
[2025-12-02T14:03:07.221000+00:00] bharath.vedartham: alright so it was intentional to not specify it 👍
[2025-12-02T17:02:27.023000+00:00] bharath.vedartham: hey <@755590043632140352> do you have any thoughts on https://github.com/ethereum/consensus-specs/pull/4769 before i close it?  i think we intentionally chose not to specify how `blob_data_available` was set?
[2025-12-02T17:40:59.127000+00:00] potuz: yeah sorry I saw that in the e-mail and didn't reply yet. I am not sure, I don't think it should be closed? I think that's right, just that we added the extra boolean when we rebased on top of Fulu and we didn't specify how to do that cause at that time it was merging with <@520034910149410861>'s changes and I didn't want to interfere. I think it's fine to merge that PR, the only caveat to which I would want to delay merging it (or we should open the discussion later) is when we actually do include the timelines. As far as I understand the expected setup is that we will have two different deadlines, one for the payload and one for the data availability. We want to set the payload to true only when it's been seen before the first deadline and the data to true only if it's passed validation (we may want to always use the same deadline for DA than to submit the PTC attestaiton, or have formally two different one and set them equal). I think we need to include this language eventually and that's probably why we never merged anything in that area
```

</details>
