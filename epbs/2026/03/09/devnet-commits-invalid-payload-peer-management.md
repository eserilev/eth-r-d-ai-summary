# Devnet Commits and Invalid Payload Peer Management

**Channel:** epbs | **Date:** 2026-03-09

## Summary

Potuz raised two technical issues during this discussion. First, they inquired about whether commits are automatically picked up on the devnet or only applied during pod restarts, as they wanted to verify which specific commit was running while investigating missing committee 1 attestations.

The main technical challenge discussed was how Prysm should handle peer management when peers are building on top of invalid payloads. Potuz explained that simply caching bad payloads by hash is problematic because it requires computing the hash locally, and even then, payload equivocations could occur with valid hashes. They suggested keeping the full payload around as a potential solution but expressed uncertainty about the best approach.

The discussion ended with an open question about how other Ethereum clients are handling this peer management problem for invalid payloads, with no responses or solutions provided in the messages shown.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-09T17:05:46.009000+00:00] potuz: <@199561711278227457> are commits picked automatically or only on restarts of the pods?  I want to know if we're running a particular commit on devnet, I do not know why we don't see committee 1 attestations
[2026-03-09T17:14:51.887000+00:00] potuz: we're having a bit of a pickle in Prysm, how do we unpeer from peers that are building on top of invalid payloads? we can't really cache a bad payload by hash unless we compute the hash ourselves
[2026-03-09T17:15:13.067000+00:00] potuz: But even in that case, there can be payload equivocations with the right hash
[2026-03-09T17:15:44.152000+00:00] potuz: so  I don't really know how to do this unless we keep the payload around or similar
[2026-03-09T17:15:47.321000+00:00] potuz: how are clients dealing with this?
```

</details>
