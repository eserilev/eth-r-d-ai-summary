# Prysm Node Sync and Checkpoint Sync Implementation

**Channel:** epbs | **Date:** 2026-04-06

## Summary

0xunclebill successfully synced a Prysm node after initially encountering issues, attributing the success to checkpoint syncing during an epoch without skipped boundary slots. The discussion revealed technical complexity around checkpoint sync implementation, where fetching payload envelopes from the checkpoint server upfront is preferred over special handling during range sync. The team identified that enabling `GET eth/v1/beacon/payload_envelopes/{block_id}` on the checkpointz server would simplify the process.

Rate limiting emerged as a significant challenge during sync, with Prysm nodes quickly throttling requests in the devnet environment where only one or two nodes can serve data. The issue is compounded by the addition of envelope requests to the existing by-range requests, consuming rate limits faster than before. 0xunclebill implemented tighter self-rate limiting as a temporary solution and suggested relaxing rate limits for small devnets in the future.

The technical implementation uses a "batches" concept in range sync that groups ByRange requests for blocks, columns, and envelopes per epoch, with batch IDs tracking all requests and ensuring coupled importing. While potuz suggested an alternative approach of staggered slot requests for payloads and blocks, 0xunclebill indicated that pre-fetching envelopes from the checkpoint server remains the simpler solution compared to special-casing the first envelope in their current architecture.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-06T00:41:10.228000+00:00] 0xunclebill: Hmm I was able to sync a Prysm node yesterday
[2026-04-06T00:57:37.175000+00:00] 0xunclebill: I think I prolly got lucky and checkpoint synced during an epoch with no skipped boundary slot
[2026-04-06T05:21:29.860000+00:00] 0xunclebill: btw regarding checkpoint sync complexity, if we dont pre-emptively fetch the payload envelope from the checkpointz server we need special handling in range sync for importing the envelope at the checkpoint slot. its easier for us to just have checkpointz server enable `GET eth/v1/beacon/payload_envelopes/{block_id}`
[2026-04-06T07:33:35.141000+00:00] 0xunclebill: we get rate limited pretty quickly by prysm. issue is theres only really one or two nodes in the devnet that can serve us data... ill add some tighter self rate limiting rates for now
[2026-04-06T07:34:08.743000+00:00] 0xunclebill: would be nice to relax these rate limits for small devnets in the future i think

also we've added a new by range request (envelopes) so thats going to eat at rate limits quicker than before
[2026-04-06T07:42:05.118000+00:00] 0xunclebill: we synced!
[2026-04-06T09:32:58.954000+00:00] potuz: When you range sync anyway you're making different requests aren't you? You can always request slot, slot+N for Payloads and slot+1, slot+1+N for blocks and works everytime
[2026-04-06T10:11:19.609000+00:00] 0xunclebill: we have this concept of “batches” in our range sync impl which is a grouping of ByRange requests for blocks columns and envelopes for a given epoch. The batch ID allows us to keep track of all the different requests and we couple all the responses for a given batch id and import everything together
[2026-04-06T10:13:17.303000+00:00] 0xunclebill: The coupling logic also prevents us from importing columns without a block. or envelopes without columns and block. etc 

anyways we can special case the first envelope but it becomes much simpler if we just fetch the envelope from the checkpoint server before letting range sync take over
```

</details>
