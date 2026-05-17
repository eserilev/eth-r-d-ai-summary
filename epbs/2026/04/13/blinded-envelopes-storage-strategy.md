# Storage strategy for blinded envelopes and requests

**Channel:** epbs | **Date:** 2026-04-13

## Summary

The discussion centers on a storage optimization problem for blinded envelopes and requests during blockchain reorganizations. The core issue is that requests need to be extracted from envelopes during reorgs, but storing both the blinded envelope and full requests creates wasteful data duplication since the block already contains the requests.

Several approaches were considered: storing blinded payloads with full requests and blinding requests only on finalization (which adds complexity), creating a separate database column for requests that gets pruned on finalization or when child blocks are written, and storing fully blinded versions. However, the separate column approach has complications with equivocating blocks, and fully blinded storage creates performance issues for RPC serving, requiring queries to both the execution layer and beacon block resolution to retrieve requests.

The participants ultimately lean toward accepting the data duplication for now, acknowledging that while it's not ideal storage-wise, it avoids the complexity and performance trade-offs of the alternative approaches, particularly for serving payloads by range which would be "horrible" without storing both versions.

## Participants

- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T17:55:47.799000+00:00] potuz: One problem I noticed on the PR is what would happen when there are reorgs. The requests need to be taken from the envelope. A natural thing would be to store the blinded envelope with requests, but this is very wasteful as the envelope and the block will store twice the requests. What are clients doing about this?
[2026-04-13T18:04:50.369000+00:00] tbenr: We are planning to store blinded payload with full requests. We could blind even the requests on finalization to save space but it will complicate things
[2026-04-13T18:42:45.750000+00:00] potuz: yeah, but this sucks badly, that was going to be our approach, but I already see myself complaining to Terence 🙂
[2026-04-13T19:06:55.266000+00:00] tbenr: A cleaner approach is to add a separate db column with requests only, and have the envelope stored as fully blinded. Than just prune the request colmun on finalization.
[2026-04-13T19:08:26.154000+00:00] tbenr: Or prune requests as soon as we write a child block containing those requests?
[2026-04-13T19:09:11.066000+00:00] tbenr: Well it won't work for equivocating blocks...
[2026-04-13T19:12:49.562000+00:00] tbenr: ~~Well if the key is the bid root, it should be fine~~
[2026-04-13T19:46:10.792000+00:00] tbenr: Storing a full blinded version will require both querying the el an also resolve the next beaconblock to get the requests  when serving rpc... I'd probably go for avcepting data duplication for now ...
[2026-04-13T20:10:06.333000+00:00] potuz: Yeah serving payloads by range is horrible if we don't save them both
```

</details>
