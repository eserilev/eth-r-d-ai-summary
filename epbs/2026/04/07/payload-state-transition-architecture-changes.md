# Payload State Transition and Architecture Changes

**Channel:** epbs | **Date:** 2026-04-07

## Summary

The discussion centers on architectural concerns with payload state transitions in Ethereum's beacon chain, specifically the problems caused by having two state transitions within the same slot. **tbenr** and **nflaig** advocate for eliminating payload states entirely by moving payload state transitions to occur as pre-state transitions before the next beacon block, which would maintain the crucial 1-1 relationship between beacon block roots and states. **nflaig** is actively working on removing payload state transitions completely, noting that `execution_requests` are particularly problematic and would likely need to be added to bids.

**potuz** expresses strong reservations about these approaches, having analyzed similar solutions two years ago. They highlight significant technical challenges including timing games, attester incentive problems, and complexities with engine API calls when payload validation fails. A critical concern involves dependent root validation for gossip objects: when blocks span multiple epochs (e.g., slot 31 to slot 65), the head state's processed payload can change active validator sets, making it potentially invalid to use head state for validating attestations even when dependent roots match. This breaks existing assumptions about dependent root guarantees for shufflings and committee assignments.

The team recognizes that if payload states must exist, they need a very tight, exhaustive list of where they're used to avoid complexity and bugs. Current consensus suggests payload states should only be relevant for pre-state selection of the next beacon block and beacon block gossip validation, while everything else (gossip validations, checkpoints, active balances) should ignore their existence. However, **potuz** warns that any drastic consensus changes to address what may be primarily an API-level issue could require completely re-analyzing the safety of consensus mechanisms.

## Participants

- nflaig
- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-07T10:28:21.290000+00:00] tbenr: my gut feeling is that the second state transition in the same slot causes issues. I'd rather do the payload state transition as a pre-state transition to be applied right before the next beacon block state transition. I need to make more sense of it but I have this feeling now.
This way a payload cannot change the state of the current slot.
[2026-04-07T10:30:28.167000+00:00] tbenr: we won't even have the concept of payload states in that world
[2026-04-07T10:37:53.572000+00:00] nflaig: I am working on exactly that since like 2 hours to competely remove the payload state transition, `execution_requests` are kinda of a pain to deal with and we would likely need to add them to the bid
[2026-04-07T10:46:26.187000+00:00] tbenr: Getting rid of payload states would be very beneficial imo. Maintaining the 1-1 relationship between beaconBlockRoot and the state is a big deal. Maybe potuz has analyzed that already
[2026-04-07T11:14:46.095000+00:00] potuz: The only way I know how to do this is to send the requests with the bid, fully cache them in the state, and execute them as part of state transition of the next beacon block if it builds on full. 

But even this (which was ditched early on because of all the bad things it brings) only patches the problem rather than solve it: you only know the actual post state of those requests after the next block arrives since the results may vary depending on how many missed slots there are.
[2026-04-07T11:56:24.716000+00:00] nflaig: isn't the problem that you then have them as part of the post block state but they might be invalid and you only figure that out in the next slot, for withdrawals the state caching is fine but I see some issues with execution requests
[2026-04-07T12:01:38.777000+00:00] potuz: This is one of the problems. Being a noop is relatively fine cause it's just a one slot free DA. It requires a new engine method to call on new blocks
[2026-04-07T12:02:25.658000+00:00] potuz: I agree that having just one transition would be preferable but I thought long for this two years ago and could not find anything acceptable
[2026-04-07T12:03:31.968000+00:00] nflaig: what about [payload as pre-state transition](https://gist.github.com/lodekeeper/6abbbc1f04bb1985f2f7bf165bfa0fa8), there might be gaps I am missing though
[2026-04-07T12:03:45.114000+00:00] potuz: You can avoid the engine call also
[2026-04-07T12:04:47.787000+00:00] tbenr: aren't there also implications on networking layer? I believe the StatusMessage's finalized root is now a checkpoint state (advanced)
[2026-04-07T12:07:13.874000+00:00] potuz: This runs into problems with timing games, incentives of attesters, etc. It ultimately becomes exactly what I propose to just return the post CL block from the point of view of the consumers with bad off protocol incentives
[2026-04-07T12:11:04.675000+00:00] potuz: No, I think it's the checkpoint root
[2026-04-07T12:11:37.620000+00:00] potuz: ```
finalized_root: store.finalized_checkpoint.root according to fork choice. (Note this defaults to Root(b'\x00' * 32) for the genesis finalized checkpoint).
```
[2026-04-07T13:37:50.498000+00:00] nflaig: I don't really understand how my proposal changes anything wrt to timing games and attester incentives, it also get rid of `payload_states` altogether keeps checkpoint sync semantics the same (also might affect how we store checkpoint during non finality, so claiming this is "just" on the api isn't clear to me yet)
[2026-04-07T13:48:10.666000+00:00] potuz: what is "just" in the API is the current "problem", it's not a part of consensus. We can solve it without any changes to consensus. 

There are solutions with consensus changes, and yes, removing entirely the payload transition and tying it to the next beacon block is a way. The incentives problems are with regard to collusions builder/proposers, but most importantly an issue arises if the call to new Payload is valid and the processing of the requests is invalid. The payload needs to be rolled back in this case. This can be dealt with in a few ways, anyway any succesful block will have to reorg the payload, but it requires careful changes to the engine calls, since proposers that find out that the requests are invalid they need to send FCU with the parent block hash, thus the problem is that the invalidity of the payload itself is not checked with the payload but rather with the next beacon block which makes syncing also harder. I believe any of these drastic changes over this issue of what state is being returned on the beacon API are unwarranted and come with going back to the drawing board to analyze the safety of the whole consensus changes
[2026-04-07T13:48:17.532000+00:00] potuz: and this is what I strongly opppose
[2026-04-07T13:59:43.200000+00:00] tbenr: So, correct me if I'm wrong. The mental model is that we should ignore the existence of payload states almost everywhere: gossip validations, everything around checkpoints, active balances, etc. The only place where they are used is pre-state selection of the next beacon block. Everything else remains as it is today: only post-beaconblock states matter.
[2026-04-07T14:15:39.084000+00:00] potuz: I think this is true. However, the situation that worries me is the following: The chain has a block in slot 31 and the next block is in slot 65 based on 31 full During slot 65 you get an attestation for 65 has head, 31 as target. We use a dependent root check to decide which state to use to validate the attestation: the dependent root for the head state coincides with the dependent root fot he target state advanced to 64 and thus we use the head state for validation. The problem is that the head state has the payload processed in that epoch transition and will have a different set of active validators. 

This is a real problem in implementations. As far as I can tell we use dependent root to decide if we can use the head state to validate two different things:

- Is the proposer correct?
- Is the attester/ptc attester etc in the right committee?

The first one is fine because of the lookahead. The second one is not because it requires the active balance and that can change from head *even if the dependent root is the same*.

The problem is here that the dependent root  was a concept that was chosen to be the latest blockroot **because** it guarantees that if you have that root on chain you have the right epoch transition and have guarantees shufflings, etc. But now that dependent root is useless if the payload can change the epoch transition. 

So this requires changes about how we handle dependent roots I believe. cc <@602753420033785856> <@688748669268132001> you guys had already thought about this too. A full epoch's worth of missing blocks would definitely cause trouble here, I am uncertain about just 31-->33 though, I think that one is just fine.
[2026-04-07T14:17:28.526000+00:00] potuz: So I think in this situation it would be invalid to use head state to validate gossip objects, and that really sucks
[2026-04-07T14:26:52.575000+00:00] potuz: It may still be fine with regards to justified balances though cause that is just keeping the right justified state. But it worries me the beacon committees
[2026-04-07T14:27:37.642000+00:00] tbenr: > The only place where they are used is pre-state selection of the next beacon block
actually beacon block gossip validation requires the payload state (which is the prestate of that block)
[2026-04-07T14:32:03.488000+00:00] tbenr: yes, these are the things that worry me. If those payload states become relevant in some scenarios, it becomes VERY complicated to reason about.
[2026-04-07T14:33:46.520000+00:00] tbenr: (and obviously bug prone)
[2026-04-07T14:37:35.351000+00:00] tbenr: so my suggestion is that we should land on a very tight list of places where we need to use payload states. And this list must be exhaustive. Seems like a practical useful angle to look things from.
[2026-04-07T15:01:37.538000+00:00] tbenr: I won't even consider the head state to be a payload state if my assumption is true. (even if it would be inconsistent with forkchoice head)
```

</details>
