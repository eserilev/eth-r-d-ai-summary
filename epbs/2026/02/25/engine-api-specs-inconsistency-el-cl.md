# Engine API specs inconsistency between EL and CL branches

**Channel:** epbs | **Date:** 2026-02-25

## Summary

Stefan Bratanov raised a concern about inconsistencies between Engine API specifications and consensus specifications, specifically pointing to differences in the Amsterdam specs. He noted that both Execution Layer (EL) and Consensus Layer (CL) teams need to maintain separate branches to support BAL and ePBS devnets, which creates operational complexity.

Tersec responded that the Engine API specs aren't the primary cause of this branching requirement, though the response was incomplete. Stefan suggested that having the Engine API specs merged to the main branch would encourage EL teams to push changes to their main branches as well, and proposed adopting a feature system similar to the `_features` mechanism used in consensus-specs to better manage different protocol versions.

The discussion appears to be ongoing with no clear resolution reached, and the specific technical details of the inconsistencies or potential solutions remain to be elaborated.

## Participants

- stefanbratanov
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-25T07:30:16.317000+00:00] stefanbratanov: I find it inconsistent that engine API specs  https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md differ from consensus-specs in such that both EL and CL need to keep branches to support BAL and ePBS devnets, just highlighting it here
[2026-02-25T09:08:45.704000+00:00] tersec: the engine API specs aren't mostly why though
[2026-02-25T11:21:15.401000+00:00] stefanbratanov: In my opinion, it makes EL teams more likely to push changes to main branches when the engine API specs are merged to master, it would be nice to have something like _features that we have in the consensus-specs
```

</details>
