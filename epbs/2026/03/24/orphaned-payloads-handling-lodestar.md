# Orphaned payloads handling in lodestar

**Channel:** epbs | **Date:** 2026-03-24

## Summary

nflaig is reviewing Lodestar's approach to handling orphaned payloads and proposes different strategies for "by range" versus "by root" requests. For range requests, they suggest never serving orphaned payloads - only serving envelopes matching the current fork choice view for unfinalized data, and not serving orphaned finalized data since it's already pruned before archiving. For root requests, they propose serving payloads regardless of fork choice view for unfinalized data (since it's a single item lookup) but still not serving orphaned finalized data.

potuz confirms that their implementation should follow the same approach once they implement pruning. They also suggest this behavior should be standardized in the specification since nodes are penalized (downscored) for not following some of these practices. nflaig notes that the same principles would likely apply to data columns as well, though they haven't fully investigated that aspect yet.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
