# P2P spec requirements for block pruning and serving

**Channel:** epbs | **Date:** 2026-03-07

## Summary

The discussion centers on block pruning practices and P2P specification requirements for serving blocks. Potuz raised concerns about not currently pruning blocks and questioned what the P2P spec requires for block serving. Terencechain clarified that the P2P spec requires blocks served "by range" to be canonical, while blocks served "by root" (being close to head) may not have the same canonical requirement.

Potuz confirmed this aligns with their understanding but noted they encountered a bug at epoch 142 (well behind finalized) when requesting by range, where peers were incorrectly returning non-canonical blocks. While they fixed the bug on their end, the issue highlighted that peers were serving blocks improperly according to the spec.

An additional concern was noted regarding compatibility between block lists and payload lists, suggesting potential inconsistencies in the data being served across different components of the protocol.

## Participants

- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-07T00:45:14.230000+00:00] potuz: We definitely don't prune them now, that's what triggered my worry that we ourselves be serving these BTW
[2026-03-07T00:45:40.038000+00:00] potuz: we don't and we should. What doed the p2p spec says for blocks?
[2026-03-07T01:56:45.036000+00:00] terencechain: p2p spec, by range should be canonical, by root, since is so close to the head, im not sure there's a requirement to be canonical
[2026-03-07T01:57:48.185000+00:00] potuz: Thanks this confirms my thinking, still the failure we had was at epoch 142 which was way behind finalized, we were requesting by range. We fixed that bug, but the peers were definitely returning incorrectly
[2026-03-07T01:58:17.302000+00:00] potuz: what's surprising is that the list of blocks is not compatible with the list of payloads 🙂
```

</details>
