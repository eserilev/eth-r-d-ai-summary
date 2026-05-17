# Removing blob KZG commitments from payload envelope

**Channel:** epbs | **Date:** 2026-01-26

## Summary

Potuz proposed removing blob KZG commitments from the payload envelope, arguing they serve no consensus role since the data column sidecars already contain them and can be used to verify the committed root in the bid. This would reduce bandwidth usage during block propagation.

Jtraglia raised concerns about data availability after the retention period, noting that without commitments in the envelope, proving blob association with a particular slot would require accessing sidecars or other blobs from that slot. The discussion revealed this change would make rollups and blob submitters dependent on saving sidecars to retain commitment data, which is more awkward than the current approach but not technically difficult. Both acknowledged that current nodes already purge both envelopes and sidecars after the retention period.

The conversation concluded with Potuz agreeing to submit a PR for discussion purposes rather than necessarily advocating for inclusion, and noting that commitments are typically available in the EVM for transactions anyway. No final decision was reached, with the PR intended to facilitate further discussion of the proposal.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-26T21:45:07.012000+00:00] potuz: I know you guys won't like this but can we remove the blob kzg commitments from the payload envelope? they play no role at all. cc <@520034910149410861>
[2026-01-26T21:46:48.768000+00:00] jtraglia: Not sure if I agree. What do you mean they play no role? Where else are they defined?
[2026-01-26T21:49:04.747000+00:00] potuz: The data column sidecars contain them
[2026-01-26T21:49:17.203000+00:00] potuz: those are enough to check that they hash to the committed root in the bid
[2026-01-26T21:49:23.179000+00:00] potuz: I'll open a PR for this
[2026-01-26T21:49:57.786000+00:00] potuz: I should have written the document to annotate the full specs long ago, now a lot of these decisions that became outdated are popping up
[2026-01-26T21:51:03.291000+00:00] jtraglia: Hmm indeed:
[2026-01-26T21:55:38.329000+00:00] jtraglia: I'm still not sure if removing them is a good idea. It feels a bit weird to not have the actual commitment saved somewhere that's not temporary. For example, I think it might make it more difficult to prove that some random blob was associated with a particular slot. To prove that the root matches, you'd need a data column sidecar or every other blob from that slot. The sidecars "expire" after the retention period & you might not care to save other people's blobs.
[2026-01-26T21:58:26.581000+00:00] potuz: That's fair, I am not sure either, but they are useless from a consensus perspective AFAICT, so broadcasting them on the envelope itself is waste of bandwidth. If we just pinky promise to save the sidecars instead of the columns we are fine, just as now we just pinky promise to save the envelopes instead of just the data needed to process blocks
[2026-01-26T21:59:43.069000+00:00] jtraglia: But then you're forced to save other people's data. I'd like to see what others think. Maybe this is fine.
[2026-01-26T22:00:07.703000+00:00] potuz: what do you mean? all columns have all commitments
[2026-01-26T22:00:36.806000+00:00] jtraglia: Yes, but columns contain parts of other blobs.
[2026-01-26T22:01:24.051000+00:00] jtraglia: A blob submitter would be required to save all of the commitments for any slot that they've submitted a blob to.
[2026-01-26T22:01:35.982000+00:00] jtraglia: Maybe not a huge deal, but different than how it is now.
[2026-01-26T22:05:09.844000+00:00] potuz: I honestly don't follow, the node saves all commitments now by saving the block, you correctly explain that if I remove them from the envelope they may be lost by not having them in the envelope that every node saves. However, as long as the node saves *any* sidecar for that slot, then the node will have all commitments, exactly the same data that is sent on the envelope
[2026-01-26T22:07:19.953000+00:00] jtraglia: Yes, this makes sense. I suppose I'm thinking of after the retention period, when the node will purge old sidecars.
[2026-01-26T22:08:25.353000+00:00] potuz: same as envelopes
[2026-01-26T22:08:29.146000+00:00] potuz: we purge everything
[2026-01-26T22:08:46.859000+00:00] potuz: Oh I see, coding this feature for rollups can be more awkward
[2026-01-26T22:09:19.678000+00:00] jtraglia: Yeah. Awkward but not _that_ difficult.
[2026-01-26T22:09:22.059000+00:00] potuz: yeah I guess nodes will want to just store once the commitments and then deduplicate, something we would want to do anyway if we ever get some time instead of coding for future forks
[2026-01-26T22:09:40.360000+00:00] potuz: add it to the long list of things that we could code in a different lifetime
[2026-01-26T22:10:00.507000+00:00] jtraglia: But yeah, submit a PR and we can discuss things there.
[2026-01-26T22:10:07.107000+00:00] potuz: will open the PR just to have something to link to in the explainer, not necessarily advocating for inclusion
[2026-01-26T22:26:25.512000+00:00] potuz: notice that in general the commitment is in the EVM anyway for the transaction
[2026-01-26T22:26:35.816000+00:00] potuz: I doubt anyone is using the CL to prove against those
[2026-01-26T22:31:17.918000+00:00] jtraglia: You're probably right.
```

</details>
