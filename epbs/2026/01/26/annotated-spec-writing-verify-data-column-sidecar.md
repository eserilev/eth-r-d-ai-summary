# Annotated spec writing and verify_data_column_sidecar modifications

**Channel:** epbs | **Date:** 2026-01-26

## Summary

Potuz announced they are writing an annotated spec due to multiple requests from implementers. They identified that the `verify_data_column_sidecar` function has been modified but noted it's unclear that this function needs to be called from the p2p spec list, referring to changes in an associated PR.

Potuz inquired about progressive SSZ structures, noting there aren't any in the current structures yet. They concluded that the current approach is acceptable either way, but suggested it would also be fine to wait until decisions are made on the open PRs that are currently under consideration.

The discussion leaves open the timing question of whether to proceed with the current modifications or wait for resolution of pending PRs, with no definitive decision reached.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-26T13:07:01.106000+00:00] potuz: note to self: I am writing an annotated spec cause I got this request multiple times from implementers. I noticed that [verify_data_column_sidecar](https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/p2p-interface.md#modified-verify_data_column_sidecar) is modified but it's not clear that this function needs to be called from the p2p spec list.
[2026-01-26T13:08:16.425000+00:00] potuz: what are the changes?
[2026-01-26T13:08:40.421000+00:00] potuz: ah the ones in the PR
[2026-01-26T13:09:10.375000+00:00] potuz: There are no progressive SSZ stuff in structures yet right?
[2026-01-26T13:09:55.602000+00:00] potuz: I think it's fine eitherway, but also is fine to wait until we make a decision on the PRs that are open
[2026-01-26T13:09:56.967000+00:00] potuz: in that list
```

</details>
