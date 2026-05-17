# PR 5094 Technical Discussion and Implementation

**Channel:** epbs | **Date:** 2026-04-10

## Summary

The discussion centers around PR 5094, which proposes including execution requests directly in beacon blocks rather than handling them separately, moving from a "dual state" approach to a single state transition. The key technical change involves having commitments as part of the finalized state, potentially enabling optimistic chain syncing without requiring the `ExecutionPayloadEnvelope`. However, this approach requires enforcing that requests match the commitment in bids during payload processing to prevent chain deadlocks, and includes gossip rules to reject non-matching request roots.

The main tradeoff identified is increased bandwidth usage since execution requests would be broadcast twice (once in the bid and once in the beacon block), but participants generally view this as acceptable given the significant reduction in implementation complexity. The change would eliminate the need for clients to manage separate empty/full states in their databases and state generation, which is seen as a substantial win. Concerns were raised about potential builder equivocation attacks and free data availability issues, though these appear similar to existing vulnerabilities.

Several implementation details remain to be resolved, including the exact placement of validation checks and the impact on various client caching strategies. Potuz indicated plans to implement and test the changes to better understand the practical implications, particularly around caching complexity that may vary between client implementations.

## Participants

- bharath.vedartham
- nflaig
- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-10T12:56:59.813000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/5094 (unless I used some evil spec sorcery it seems possible)
[2026-04-10T13:07:43.709000+00:00] potuz: can you tl;dr me instead of reviewing the code first? how do you sync 33 as asked above without the payload of 31? how do you verify the requests?
[2026-04-10T13:08:50.217000+00:00] potuz: do you commit in the bid?
[2026-04-10T13:09:12.922000+00:00] potuz: like here?
[2026-04-10T13:10:23.505000+00:00] nflaig: you have the commitment as part of the finalized state, I don't see why you even need the payload
[2026-04-10T13:10:47.341000+00:00] potuz: yeah that's why I asked if you commit in the bid
[2026-04-10T13:12:42.348000+00:00] nflaig: and I think with that change you can even optimistically sync the chain without requiring the `ExecutionPayloadEnvelope`
[2026-04-10T13:13:16.547000+00:00] potuz: you need the hash validation if we want to keep the same assurances as today
[2026-04-10T13:23:35.347000+00:00] potuz: left a comment, I think you need to enforce when processing the payload that the requests match the commitment in the bid, otherwise there's a chain deadlock, since no proposer can build on full.  The payload needs to be marked as invalid
[2026-04-10T13:25:34.481000+00:00] tbenr: I like the approach and I believe it is a big improvement.
[2026-04-10T13:26:28.474000+00:00] nflaig: agree, I have that check, but it might be in the wrong place
[2026-04-10T13:26:51.124000+00:00] potuz: I'll take a stab at implementing the changes, I *suspect* it'll make our caching harder as mentioned above, but not clear that it's a blocker for us
[2026-04-10T13:27:20.436000+00:00] potuz: oh I only checked in `process_execution_payload`
[2026-04-10T13:29:26.930000+00:00] potuz: oh yeah you have it on the forkchoice handler
[2026-04-10T13:29:51.168000+00:00] nflaig: you said that before, but for lodestar it removes a lot of complexity in our state cache, essentially what we have on mainnet now
[2026-04-10T13:30:33.519000+00:00] nflaig: so I am curious why that is for prysm, having to worry about 2 states per slot per root seem just more complex to me
[2026-04-10T13:31:04.252000+00:00] potuz: I think it's on the NSC, but I may be wrong, will find out when I code it, it doesn't look too bad
[2026-04-10T13:34:46.918000+00:00] potuz: so the only real dissadvantage is the cost of making the beacon block larger by the requests. Do we know how large these can be?
[2026-04-10T13:35:06.982000+00:00] potuz: these get now twice being broadcast in full
[2026-04-10T13:36:24.276000+00:00] nflaig: I was wondering if we can remove them from `ExecutionPayloadEnvelope`, I think this was just added so you can run the state transition without consulting the EL?
[2026-04-10T13:37:07.873000+00:00] potuz: the problem of not having them in the envelope is what I say above that forkchoice will enforce that you build on full
[2026-04-10T13:37:09.162000+00:00] nflaig: maybe pandas could help? the are most of the time empty it seems but their theoretical ssz size limit is high
[2026-04-10T13:37:22.909000+00:00] potuz: and attesters will not be able to take any incoming block as head
[2026-04-10T13:38:00.805000+00:00] potuz: so attesters need to have the requests available when the payload arrives to give builder safety
[2026-04-10T13:38:19.489000+00:00] nflaig: yeah you right i think, also optimistic sync needs it for sure
[2026-04-10T13:40:03.836000+00:00] nflaig: well we do sent them as part of the header right now which is very time sensitive, see https://github.com/ethereum/builder-specs/issues/120, but this is more theoretical, I don't think they are large in practice but also haven't looked at data
[2026-04-10T13:49:02.641000+00:00] potuz: So one worry I can see right now is that the builder could cause an ex-ante reorg by sending valid requests to the majority of the chain, invalid ones to a minority and the proposer being among the minority. But these sort of issues already exist today I believe on payload equivocation.  So we need to make sure that PTC votes on the valid requests being there.
[2026-04-10T13:56:05.980000+00:00] tbenr: I see gossip rules to reject non-matching requests root with the bid. should it be enough? committed but invalid requests are noop correct?
[2026-04-10T13:58:37.553000+00:00] potuz: Yeah in principle this prevents the payload from propagating over the chain, and if the proposer gets the invalid one first, it will attempt to reorg it whikle the people that got the valid one first will take it as valid, these sort of ex-ante reorgs are already in the current forkchoice so this does not make the situation worse I think. They aren't as bad as the current ex-ante reorg cause they won't steal a payload.
[2026-04-10T14:01:33.248000+00:00] potuz: <@795439202732867594> could you weigh in the impact of broadcasting the requests twice and adding them to the beacon block?
[2026-04-10T14:02:03.416000+00:00] tbenr: it seems the double send of the request is unavoidable. so we are exchanging bandwidth with single state transition (with all the good consequences of it). I believe it is significantly net-positive.
[2026-04-10T14:08:00.150000+00:00] tbenr: and maybe we could remove this duplication in the future, on the other side, once the double-state concept lends on clients, the complexity\bugs damage would be done 🙂
[2026-04-10T14:10:34.132000+00:00] potuz: yeah I think the removal of the handling of empty-full states from db/stategen is a big win
[2026-04-10T14:11:02.393000+00:00] potuz: we should allocate time to see if it affects forkchoice or cosensus in any way
[2026-04-10T14:15:15.804000+00:00] tbenr: I have the feeling that this is orthogonal to FC though. If a node has a state attached or not should be irrelevant
[2026-04-10T14:26:42.269000+00:00] potuz: yeah, but for example offhchain issues like the above if there are equivocations and attesters need to enforce the payload being full, etc.
[2026-04-10T14:32:19.492000+00:00] tbenr: Ye but the verifiability of the payload seems unaffected. 

Can you recall me why requests need to be blinded in the bid?
[2026-04-10T14:33:07.873000+00:00] tbenr: Just efficiency?
[2026-04-10T14:39:06.640000+00:00] potuz: you are thinking on keeping them full in the bid  and avoid rebroadcasting? I think this is ugly in the event of invalid payloads but also yes, sending them full in the bid is a problem.
[2026-04-10T14:40:55.269000+00:00] tbenr: Ye i was thinking that. It has free da issues at least
[2026-04-10T14:42:37.824000+00:00] nflaig: aren't there even complains from builders that having them in the bid leaks information about the payload?
[2026-04-10T14:44:22.134000+00:00] tbenr: i'm not an expert but the requests from EL to CL don't have much to leak
[2026-04-10T14:45:24.520000+00:00] nflaig: ah it was noted here https://github.com/ethereum/builder-specs/issues/120
> There also has been concerns (eg. #108) about leaking block information via the logs bloom.
but I am not sure if this means same as logs blooms or similar
[2026-04-10T14:49:10.542000+00:00] bharath.vedartham: today as per fulu, we are sending the full execution requests along with the blinded payload in the BuilderBid as per here
[2026-04-10T14:49:11.122000+00:00] bharath.vedartham: https://github.com/ethereum/builder-specs/blob/main/specs/electra/builder.md?plain=1#L36
[2026-04-10T14:49:47.112000+00:00] bharath.vedartham: In terms of MEV leaking, I believe the payload is the critical part and not the requests. otherwise I m pretty confident builders would have raised issues against the execution requests being fully in the bid
[2026-04-10T14:50:11.298000+00:00] tbenr: seems like an efficiency argument, mostly
[2026-04-10T14:50:59.640000+00:00] nflaig: that is just because we need them for stf, not because we want to include them per se
[2026-04-10T14:51:29.783000+00:00] nflaig: so gloas removes a lot of potential information leakage
[2026-04-10T14:51:57.597000+00:00] bharath.vedartham: yeah by just having the block hash in the bid, in terms of leakage i think its better than what we have today in the `BuilderBid` which is the full execution payload header
[2026-04-10T14:52:16.342000+00:00] bharath.vedartham: fair my point is that, i dont think having the execution requests in the bid should contribute to any info leak in the bid
[2026-04-10T14:56:19.888000+00:00] potuz: So if we don't optimize to preexecute  the requests, our cache actually becomes simpler. If the requests aren't that much of computation this actually simplifies things for us as well
[2026-04-10T14:59:47.084000+00:00] tbenr: speaking of which.

current design doesn't have requests written onchain in beacon blocks, while the new design has.
If requests are not affecting payload validity by design, is the new design open to big free DA issues?
[2026-04-10T15:01:57.608000+00:00] potuz: Having them in the bid would give free DA but in the block how so?
[2026-04-10T15:01:58.450000+00:00] tbenr: oh this is similar to what we have today
[2026-04-10T15:02:25.387000+00:00] potuz: Today the builder can include whatever it wants
[2026-04-10T15:02:27.568000+00:00] potuz: Anyway
[2026-04-10T15:02:34.488000+00:00] tbenr: no no, talking about unblinded requests in beaconblock
[2026-04-10T15:02:55.467000+00:00] potuz: As long as the Payload is valid I don't see any change from today
[2026-04-10T15:03:05.891000+00:00] tbenr: ye exactly
```

</details>
