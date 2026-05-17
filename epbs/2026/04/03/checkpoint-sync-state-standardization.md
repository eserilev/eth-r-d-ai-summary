# Checkpoint Sync Implementation Challenges

**Channel:** epbs | **Date:** 2026-04-03

## Summary

The discussion centers on implementation challenges for checkpoint sync when dealing with skipped slots. Lighthouse (LH) is experiencing difficulties advancing pending state from the previous epoch to the finalized epoch during checkpoint sync at skipped slots because they require the payload. Prysm handles this by requesting payloads for the slot range of the finalized checkpoint and blocks from slot+1, then deciding later what to apply using their existing range request mechanism after obtaining the state from the checkpoint server.

Lighthouse faces architectural constraints where they need a finalized state in the database to start the network layer, and currently require the starting state to be epoch-aligned. Sproul indicates they may need to loosen this constraint to handle skipped slots properly. The discussion reveals a broader issue where assuming that state advanced to an epoch is canonical can cause problems across multiple areas of implementation, with some uncertainty around tracking skipped slot attestations and their on-chain impact.

## Participants

- nflaig
- potuz
- sproul
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-03T10:53:25.170000+00:00] sproul: agree

for LH we are struggling to do checkpoint sync at skipped slots because we can no longer advance the pending state from the prev epoch into the finalized epoch (need the payload)

how does Prysm handle this?
[2026-04-03T10:54:08.475000+00:00] potuz: We request Payloads for the slot range of the finalized checkpoint and blocks from slot+1
[2026-04-03T10:54:16.837000+00:00] potuz: And decide later what to apply
[2026-04-03T11:29:21.437000+00:00] sproul: you fetch this from the checkpoint server via HTTP?
[2026-04-03T11:41:49.583000+00:00] nflaig: that seems to be still the case, I am not able to checkpoint sync, also very rough to get any peers
[2026-04-03T12:05:09.627000+00:00] potuz: No, we start requesting by range
[2026-04-03T12:05:57.371000+00:00] potuz: Just the same as now: you get the state from the server and we start requesting the range data starting from it
[2026-04-03T12:10:58.855000+00:00] sproul: Hmm ok, we don't really have a way of doing this easily. We need some finalized state in the DB in order to start the network layer. I'll see if we can loosen the constraint that the starting state must be epoch-aligned.
[2026-04-03T12:12:38.562000+00:00] potuz: Hmm I don't understand, the checkpoint state is epoch aligned, except the Payload if it's there, is this the problem? You assume that the state advanced to the epoch is canonical?
[2026-04-03T12:13:16.495000+00:00] potuz: That sameb problem is biting us in other areas
[2026-04-03T14:52:24.142000+00:00] terencechain: my bad, i forgot, and we dont track this as the skipped slots attestations never get counted on chain right? we could if we want to but it seems like a lot of complexity for a minimal gain
```

</details>
