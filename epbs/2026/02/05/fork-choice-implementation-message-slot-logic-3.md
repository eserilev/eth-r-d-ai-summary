# Fork Choice PR Payload Data Availability Behavior

**Channel:** epbs | **Date:** 2026-02-05

## Summary

This discussion centers on a fork choice PR that changes payload data availability behavior, particularly how the Payload Timeliness Committee (PTC) influences proposer and attester decisions. The key concern raised by fradamt is that the new `should_extend_payload` function includes a check `if not is_payload_data_available(store, root): return False`, which gives the PTC power to force payload reorgs regardless of the next proposer's preference, whereas previously proposers could extend payloads as long as they were actually available even if the PTC didn't vote for them.

Potuz clarifies that the intent is not to give PTC absolute power, but rather to address scenarios where proposers have partial data and need guidance from the PTC about full data availability. The proposed behavior is: (1) if a node has sufficient data to be certain of availability (through keys, column subscriptions, etc.), `is_payload_data_available` should return `True`, and (2) otherwise it should rely on PTC for the DA check. Potuz argues this stricter approach for data availability (compared to payload timeliness) is necessary to prevent attack scenarios where data is timed to arrive at slot end, causing unnecessary reorgs and exacerbating the free option problem.

The discussion reveals disagreement about making this behavior implementation-dependent, particularly for attesters where Francesco argues the behavior shouldn't be left as implementation choice. There's also debate about why blob availability should be treated more strictly than payload timeliness. The conversation ends without clear resolution, with Potuz suggesting alternative approaches like recording local timing data or removing the boolean from PTC attestation, though noting these would be more complex.

## Participants

- fradamt
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-05T15:05:51.852000+00:00] fradamt: The [fork-choice PR](https://github.com/ethereum/consensus-specs/pull/4884/changes) changes the behavior quite a bit as far as I can tell, in particular now the PTC has the power to force a payload to be reorged regardless of what the next proposer decides to extend, because of this in `should_extend_payload`:

```python
    if not is_payload_data_available(store, root):
        return False
```

Whereas previously a proposer could always choose to extend a payload even if it was late/the PTC didn't vote for it, as long as it was actually available. I am not sure we want this behavior.
[2026-02-05T15:14:28.039000+00:00] fradamt: Also it's not clear to me why we would require this about blob availability but not about the payload? If anything it is more important for the payload to arrive timely, so that the next slot's builder can start working on it, and the next slot's proposer can start verifying it
[2026-02-05T15:25:01.731000+00:00] potuz: The PTC doesn't have this right, that's why there is an implementation dependent part: if the proposer does have the data then it can build on top of present.
[2026-02-05T15:25:38.971000+00:00] potuz: The problem is for the proposer that has only its part of the data but doesn't know if the full data is available. If the PTC says it's not, then it should build on top of empty
[2026-02-05T15:25:58.029000+00:00] potuz: the Current status makes it build on present and it may be reorged
[2026-02-05T15:28:50.604000+00:00] potuz: We should change the attester's path though
[2026-02-05T15:32:26.591000+00:00] potuz: <@520034910149410861> I think even for attesters this behavior should be as in the stated PR: if you are an attester that don't have all the data and you see the PTC saying that it's not there, you should vote against the payload
[2026-02-05T15:37:16.736000+00:00] potuz: I guess your argument would be that anyway the block won't be attested if the data wasn't there because attesters wiithout the data will be a majority. But my worry is about data arriving after the deadline to do an ex-ante reorg.
[2026-02-05T17:29:05.123000+00:00] terencechain: i think <@520034910149410861>  may have missed this sentence: `Implemnetations MAY return `True` if the node have recovered all the blob data.`
maybe you can just use the helper `is_data_available(root)` and put that in `is_payload_data_available` like:

```
def is_payload_data_available(store: Store, root: Root) -> bool:
   if is_data_available(root) -> return True
```
[2026-02-05T17:39:16.612000+00:00] potuz: No I think Francesco's honestly worried about this new requirement, but I think that this should be the right behavior to prevent the data becoming available late
[2026-02-05T17:42:51.035000+00:00] fradamt: I don't understand what's the rationale for being more strict with blobs than with the payload. Attesters will never vote for extending a payload if `is_payload_data_available` returns `False` (other than if implementations decide to just care about the local DA check, but that may not be the case as it is written here), but they will vote for it if `is_payload_timely` is `False` as long as the proposer has extended it? Why does that make sense?
[2026-02-05T17:43:21.542000+00:00] fradamt: Also don't understand making this an implementation choice, the function is used not just by the proposer (where making it a choice would be reasonable) but by attesters. I don't think we should leave attestation behavior as an implementation choice
[2026-02-05T17:46:22.924000+00:00] terencechain: but if we add `is_data_available(root)` to `is_payload_data_available` and return `true`, it's no longer an implementation choice right
[2026-02-05T17:53:54.593000+00:00] potuz: > other than if implementations decide to just care about the local DA check, but that may not be the case as it is written here)
It is not about **just** the local DA check. I can make it more explicit if you want, but I think it should be really left as an implementation detail instead of specifying it in Forkchoice cause it becomes more complicated. But the statement is that:

1. If the validating node has enough keys or is subscribing to all columns or is a supernode or for whatever reason has enough data to be 100% certain that the data is available, this function should return `True`. 
2. Otherwise this function should rely on PTC for DA check. 

I think we need to be more strict with DA than with the payload timeliness because the payload not being timely cannot cause ante reorg here: we do allow the proposer to reorg untimely payloads, and the payload is fully validated by the attester regardless of timeliness, whether DA is not validated by attesters. Attesters that have gotten the DA check late, will currently reorg a beacon block that builds on empty simply because it has not gotten the data  and the proposer had no indication that the data was available because the PTC itself was signaling that the data was not available.
[2026-02-05T17:55:29.347000+00:00] potuz: I don't mean to have it as an *optional* behavior, but rather a mandatory one that is not possible to currently specify here as far as I can tell since  `is_data_available` is an implementation detail
[2026-02-05T19:16:27.559000+00:00] potuz: The attack scenario is to time the data to be available exactly at the end of the slot, and thus it will cause the next proposer to be reorged. Also it makes the free option problem worse by making the PTC deadline irrelevant, no matter how much we shorten it, the builder gets to have the full slot to reveal
[2026-02-05T19:21:09.287000+00:00] potuz: I guess we can deal with this locally by recording the time at which the data is available and not rely on PTC, that would be more like the Payload treatment and at least won't suffer from this attack. In this case we can even remove the boolean from the PTC attestation
[2026-02-05T19:21:28.412000+00:00] potuz: But it's strictly more complex
[2026-02-05T22:21:15.250000+00:00] fradamt: > The attack scenario is to time the data to be available exactly at the end of the slot, and thus it will cause the next proposer to be reorged.

It wouldn't, the way it was written before was: if the proposer does *not* extend a payload that's not voted by a majority of the PTC, everyone attests to the proposal (when there's no majority, it's always up to the proposer).
[2026-02-05T23:04:34.136000+00:00] potuz: Here the majority of the PTC is voting that the payload was timely
[2026-02-05T23:04:57.152000+00:00] potuz: It's just voting that it wasn't available
[2026-02-05T23:05:12.941000+00:00] potuz: Before this PR there's zero mention of the second part
```

</details>
