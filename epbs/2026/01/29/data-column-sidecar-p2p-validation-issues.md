# Data Column Sidecar P2P Validation Issues

**Channel:** epbs | **Date:** 2026-01-29

## Summary

nero_eth identified a critical validation issue with Data Column (DC) sidecars in peer-to-peer networks. When DC sidecars are received before the corresponding block, validation becomes impossible because both the inclusion proof and KZG proof require commitments that are contained in the block's sidecar. This dependency means that the validation process cannot proceed without first having access to the block data.

The issue extends beyond validation to data reconstruction functionality. Nodes cannot rely solely on sidecars for reconstruction because the `recover_matrix()` function requires the blob count, which is typically obtained from `len(sidecar.kzg_commitments)`. This creates a fundamental dependency on having complete sidecar data for proper operation.

raulvk and potuz shared relevant resources addressing these column sidecar concerns, including a consensus specs pull request (#4880) and potuz's analysis of gossip protocols with a specific section on data column sidecars. The discussion highlights ongoing technical challenges in the Data Availability Sampling implementation that require resolution.

## Participants

- nero_eth
- potuz
- raulvk

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T07:46:31.426000+00:00] nero_eth: Am I getting this right that DCs, when being received over p2p cannot be validated anymore if the block with the bid has not been seen yet - not only the inclusion proof doesn't work but also the kzg one would be impossible without the committments in the sidecar?

This also means that nodes cannot solely rely on the sidecars in reconstruction, since the blob count is needed in `recover_matrix(...)` , which we got from doing `len(sidecar.kzg_commitments)` .
[2026-01-29T14:01:39.925000+00:00] raulvk: related to the above discussion re: column sidecars
https://github.com/ethereum/consensus-specs/pull/4880
[2026-01-29T14:20:09.076000+00:00] potuz: I'm not sure if these are useful, Justin and Paul just corrected a couple of things. Anyway, here's a simpler one on gossip. The only section that may make some sense is the one on data column sidecar that contains the above discussion 
https://www.potuz.net/posts/gloas-annotated-pubsub/
```

</details>
