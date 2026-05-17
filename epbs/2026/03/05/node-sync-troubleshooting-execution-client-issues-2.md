# Slow Sync Performance and Network Issues

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around unexpectedly slow sync performance during what appears to be an Ethereum client sync operation. Potuz reports that the sync process is "painfully slow," progressing from slot 1237 to 1815 over several minutes, despite working with empty blocks where bandwidth shouldn't be a limiting factor. The main issue appears to be lengthy peer negotiation times for batches, followed by rapid imports of 300 slots at once, suggesting the bottleneck is in the peer discovery/negotiation phase rather than the actual data transfer.

The team considers potential causes including colocation issues and rate limiting from Lighthouse nodes. However, potuz notes that they previously synced immediately using only Lighthouse nodes, and mentions having their own node available to serve payloads and blocks. The root cause of the slow batch negotiation remains unidentified, leaving this as an open performance issue requiring further investigation.

## Participants

- 0xunclebill
- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T02:28:57.022000+00:00] barnabasbusa: Not really sure why it’s so slow
[2026-03-05T02:29:04.777000+00:00] potuz: doubt bandwidth is an issue with empty blocks
[2026-03-05T02:29:30.599000+00:00] potuz: it takes a long time to negotiate batches with peers, and then it immedately imports 300 slots
[2026-03-05T02:29:34.989000+00:00] potuz: something weird
[2026-03-05T02:29:50.155000+00:00] potuz: I can only think on colocation
[2026-03-05T02:30:03.725000+00:00] potuz: I'm at 1237 now
[2026-03-05T02:34:20.469000+00:00] potuz: I'm at 1687, painfully slow
[2026-03-05T02:34:40.159000+00:00] potuz: 1815 now
[2026-03-05T02:35:55.921000+00:00] 0xunclebill: could be lighthouse nodes rate limiting
[2026-03-05T02:36:32.575000+00:00] potuz: doubt it, here I syned immediately and only off of lighthouse
[2026-03-05T02:36:43.052000+00:00] potuz: now I have my own node that can serve those payloads/blocks
```

</details>
