# Orphaned payloads handling in lodestar

---

## 2026-03-24 (epbs)

nflaig outlined Lodestar's approach to handling orphaned payloads, proposing different strategies for "by range" versus "by root" requests. For range requests, they suggest never serving orphaned payloads - only serving envelopes matching the current fork choice view for unfinalized data, and not serving orphaned finalized data since it's pruned before archiving. For root requests, they propose serving payloads regardless of fork choice view for unfinalized data (since it's a single item lookup) but not serving orphaned finalized data.

potuz confirmed that their implementation should follow the same approach once they implement pruning. Both participants agreed this behavior should likely be formalized in the specification since nodes can be downscored for not following these patterns. The discussion also noted that the same principles would apply to data columns, though that area needs further investigation.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-03-24T12:54:06.751000+00:00] nflaig: I am reviewing how we handle orphaned payloads in lodestar, this is how I am currently thinking about

by range
- never serve orphaned payloads
- for unfinalized data, serve only envelopes that match our current fork choice view
- for finalized data, do not serve them either, already handled since we prune orphaned payloads before archiving

by root
- for unfinalized data, serve payloads irrespective of fork choice view since it's a single item lookup
- for finalized data, do not serve them, already handled by archiving (same as by range)

is this something that should be part of the spec or do we leave it up to implementations?
[2026-03-24T12:54:25.337000+00:00] nflaig: (same applies for data columns I suppose but haven't looked that much into it yet)
[2026-03-24T13:38:29.483000+00:00] potuz: We aren't there yet (not pruning still) but we should do exactly as described here. I guess this behavior should be part of the spec cause we downscore if you do not follow some of these.
```

</details>

