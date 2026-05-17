# Blinded envelope storage and reorgs handling

---

## 2026-04-13 (epbs)

The discussion centers on a storage challenge for blinded envelopes when handling blockchain reorganizations. Potuz identified that during reorgs, requests need to be extracted from envelopes, but storing both the blinded envelope and the full requests creates wasteful data duplication since both the envelope and block would contain the same request data.

Tbenr proposed several potential solutions, including storing blinded payloads with full requests and blinding requests only on finalization, or using a separate database column for requests that could be pruned after finalization or when child blocks are written. However, they noted complications with equivocating blocks and the complexity of serving RPC requests when using fully blinded storage, which would require querying both the execution layer and resolving beacon blocks to reconstruct the requests.

Both participants ultimately acknowledged that while data duplication is suboptimal, it may be the most practical approach for now, especially given that serving payloads by range becomes significantly more difficult without storing both the blinded and full versions of the data.

**Participants:** potuz, tbenr

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-15 (epbs)

0xunclebill raised a potential edge case in reorg handling where a node might not have any valid bids to accept after a reorganization, since it may have initially ignored bids that didn't match its previous view of the head. This scenario becomes more critical if local block building is eventually deprecated. Their proposed solution involves implementing a reprocess queue for non-canonical bids that can be evaluated against the new head view following a reorg.

Potuz disagreed that this edge case needs addressing, arguing that bids naturally arrive for both current and next slots, ensuring some bids will always match the current head view. Additionally, potuz noted that builders already send different bids for different potential heads, similar to their current practice of building on top of multiple possible heads.

The discussion appears to reflect differing views on whether this reorg edge case requires a technical solution, with no clear consensus reached on implementing the proposed reprocess queue mechanism.

**Participants:** 0xunclebill, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-15T01:15:16.200000+00:00] 0xunclebill: In the case of a reorg, I think there could be a situation where a the node doesnt have any bids to accept (since it had initially ignored bids that didnt initially meet its view of head). Probably not super important now, but if we want to eventually deprecate local block building we'll need to handle this edge case I think 

Our plan right now is to send non-canonical bids to a reprocess queue and in the case of re-org, process bids that now meet our new view of head
[2026-04-15T01:17:32.564000+00:00] potuz: I think this is not necessary to address, bids will come for next and current slot
[2026-04-15T01:18:16.061000+00:00] potuz: Thus some bids must match the current head view
[2026-04-15T01:18:52.437000+00:00] potuz: Also it's very likely builders themselves will be sending different bids for different heads
[2026-04-15T01:19:09.016000+00:00] potuz: As they currently build on top of different heads
```

</details>

