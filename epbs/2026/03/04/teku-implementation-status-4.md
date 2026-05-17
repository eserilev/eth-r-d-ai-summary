# Teku Build and Database Management

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers on troubleshooting issues with a Teku Ethereum client build where the current version is not producing attestations properly. Stefan requested that Barnabas refresh the image from his branch to fix the issue. When they attempted to run the updated build, Teku encountered fork choice errors, specifically being unable to produce state for blocks during reorg situations at slots 161 and 321, with Stefan acknowledging that their fork choice implementation isn't fully proper yet and only works in most cases.

After the initial failure, they decided to perform a genesis sync with a fresh database rather than attempting checkpoint sync. However, this approach also failed with an "UnsupportedOperationException" indicating that lookup of finalized execution payload envelopes is not yet implemented. The team appears to be working with an incomplete implementation that has known limitations in both fork choice handling and execution payload envelope lookups, requiring further development work to resolve these fundamental issues.

## Participants

- barnabasbusa
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T15:53:37.182000+00:00] stefanbratanov: can you refresh the image still from my branch, otherwise Teku is pretty bad (not producing attestations)
[2026-03-04T15:53:44.402000+00:00] barnabasbusa: yeah im building it alrady
[2026-03-04T15:53:49.517000+00:00] barnabasbusa: do you need a nuked db?
[2026-03-04T15:53:50.917000+00:00] barnabasbusa: can you sync?
[2026-03-04T15:54:15.351000+00:00] stefanbratanov: nuke it better, can sync technically, but it's a gamble
[2026-03-04T15:54:28.079000+00:00] barnabasbusa: can you checkpoint sync?
[2026-03-04T15:54:28.807000+00:00] barnabasbusa: lol
[2026-03-04T15:54:38.697000+00:00] stefanbratanov: ok probably not actually haha
[2026-03-04T15:54:45.522000+00:00] stefanbratanov: ok don't nuke it, let's see
[2026-03-04T15:54:45.851000+00:00] barnabasbusa: we can try genesis sycn
[2026-03-04T15:54:55.880000+00:00] stefanbratanov: yeah that's possible i think
[2026-03-04T15:55:03.762000+00:00] barnabasbusa: bootnode is running teku too
[2026-03-04T15:57:59.376000+00:00] barnabasbusa: ` Fork choice process head failed
java.util.concurrent.CompletionException: java.lang.IllegalStateException: Unable to produce state for block at slot 161 (0xbe0ebd894aaf627422293356bae62d10d8c6a1bd2ebb6dc5bf1edc74f8132840)`
[2026-03-04T15:58:05.128000+00:00] barnabasbusa: teku is stuck here with the new image
[2026-03-04T15:58:58.165000+00:00] barnabasbusa: looks like teku is having a hard time reorgin that block
[2026-03-04T15:59:04.397000+00:00] barnabasbusa: so might need to yeet the db afterall
[2026-03-04T15:59:35.450000+00:00] stefanbratanov: gg, no idea, not sure how we handle reorgs since our fork choice is technically not implemented super properly yet, only in most cases
[2026-03-04T16:01:04.229000+00:00] barnabasbusa: doing genesis sync on them
[2026-03-04T16:01:10.508000+00:00] barnabasbusa: https://klipy.com/gifs/doi-mo
[2026-03-04T16:02:16.104000+00:00] barnabasbusa: `java.util.concurrent.CompletionException: java.lang.IllegalStateException: Unable to produce state for block at slot 321 (0xb319327d39615f9cb2edc2848da7f0d99c05499805dc608262f6c999d8224c1a)`
[2026-03-04T16:02:25.242000+00:00] barnabasbusa: got a bit further
[2026-03-04T16:03:40.312000+00:00] barnabasbusa: ```
2026-03-04 16:03:16.585 ERROR - Failed to process execution payload envelopes by range request
java.util.concurrent.CompletionException: java.lang.UnsupportedOperationException: Lookup of finalized execution payload envelopes is not implemented yet
at java.base/java.util.concurrent.CompletableFuture.encodeThrowable(Unknown Source)
```  😂
[2026-03-04T16:04:24.849000+00:00] stefanbratanov: oh yes haha, at least it's a nice error message
```

</details>
