# Validator blob sidecar broadcasting responsibility clarification

**Channel:** epbs | **Date:** 2025-12-03

## Summary

bharath.vedartham raised a clarification issue regarding validator responsibilities for blob sidecar broadcasting in PR #4772. The current specification states that validators don't need to broadcast blob sidecars since this is the builder's responsibility, but this creates ambiguity for self-building validators who act as their own builders.

The proposed change clarifies that when validators self-build blocks, they retain the responsibility to broadcast blob sidecars themselves, since they are effectively functioning as the builder in that scenario. This appears to be a minor specification update to resolve potential confusion about broadcasting responsibilities in different validator configurations.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-03T06:31:04.660000+00:00] bharath.vedartham: a minor PR here: https://github.com/ethereum/consensus-specs/pull/4772 
we mention that validators don't have to broadcast blob sidecars since it is the responsibility of the builder. but if a validator self-builds, they still have to broadcast the sidecars by themselves. the validator would be the builder. 
I felt like it was worth clearing that?
```

</details>
