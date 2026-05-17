# Forkchoice Reorg Bug Testing Discussion

**Channel:** epbs | **Date:** 2026-04-17

## Summary

Potuz identified a subtle but critical bug in Prysm's proposing code where forkchoice reorgs can cause a mismatch between the consensus head and block preparation logic. Specifically, when the head is at slot N (full) but reorgs to (N, empty) due to attesters rejecting the payload, the forkchoice handles this correctly but the proposing code sends wrong payload attributes and prepares on top of the full block instead of the empty one. This represents a liveness risk that isn't caught by current forkchoice tests, which only verify the head is correct but don't test the proposing path consistency.

The discussion explored several potential testing approaches for these complex scenario-driven bugs. Sproul suggested integration tests within individual clients but noted there's no good cross-client solution yet. Terencechain shared that Prysm uses an interceptor between beacon node and validator client for end-to-end Gloas testing, allowing manipulation of messages without enshrining "evil" behavior in upstream code. Barnabasbusa offered their RPC snooper tool as another option for message manipulation between beacon node and validator client.

The main open question remains how to systematically test these forkchoice-driven reorg scenarios across clients, particularly with the added complexity of Gloas. Potuz expressed skepticism about fuzzers for this type of testing and offered to open issues describing specific test scenarios, though the implementation of cross-client testing infrastructure remains unresolved.

## Participants

- barnabasbusa
- potuz
- sproul
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-17T20:46:29.103000+00:00] potuz: I'm discussing some edge cases in private with <@592004585506340885> but also it occurs to me that there's a more general issue so I'd like to bring it up here. Tagging <@602753420033785856> and <@425572898787426305>
[2026-04-17T20:54:17.636000+00:00] potuz: We were just jointly reviewing live a PR on Prysm and found a subtle bug on the proposing code. The situation is like this. Our head is at slot `N` Full, and the chain advances on top of that block being full, but attesters did not like the payload, so we suffer a reorg to  (`N`,  empty). We handled this well in forkchoice and woud log such a reorg. However, we would send the wrong payload attributes and start preparing a payload on top of full. 

I am sure many here have already found many similar bugs. The essence is that forkchoice head says something, but the proposing code aims to build a different block. These kinds of bugs are not tested by forkchoice tests: those tests just check that our head is the right one. Here the problem is about a mismatch between head and the preparing block. How should we test and catch these bugs early on? these are a liveness risk on the chain. 

We could do some form of kurtosis scenario testing as discussed previously (I think <@199561711278227457> was also there). But the very specific combination pattern to trigger the above bug requires a majority of attesters voting for something incorrect in a way and then a missing block right at the exact moment. I feel that creating scenarios and actually implementing them in client images to run in Kurtosis quickly blows up in complexity. 

Is there any protocol that we could try to implement to test the proposing path? have there been discussions about this before recorded somewhere? my memory fails but I do remember discussing similar issues years ago, I think Gloas makes this situation much more delicate.
[2026-04-17T21:49:44.877000+00:00] sproul: yeah we have not implemented this yet, but I anticipate it being a pain. I think integration tests in clients would be one way to do it (you can create the precise timings and attestation patterns you speak of), but I don’t have a good cross-client solution
[2026-04-17T21:50:38.856000+00:00] potuz: I think we can aim to test scenarios that don't depend on timings at least
[2026-04-17T21:50:59.609000+00:00] potuz: But I honestly don't know how it's best to proceed
[2026-04-17T21:51:44.855000+00:00] potuz: I'm doing these with an "evil" branch of Prysm but haven't even started with Gloas and I'm overwhelmed just thinking on cases
[2026-04-17T21:53:29.837000+00:00] sproul: something fuzz-y could be good to generate cases beyond what we can think to codify
[2026-04-17T21:56:37.762000+00:00] potuz: I'm skeptical of fizzers for these kinds of things I have in mind which are forkchoice driven reorgs. Fuzzers are very good to test validity conditions. Thinking on scenarios is not really a problem. The main question I have is how to set them up. I want the actual client image to be in a situation like above, there's a reorg to (N-1, empty), it holds the Payload and it needs to propose, make sure it doesn't use the Payload of N-1
[2026-04-17T22:02:54.740000+00:00] terencechain: for prysm's gloas end to end test, i added an interceptor in between beacon node and validator client to mutate / delay/ drop messages that pass back and forth btw beacon node and validator client. The reason i added such is i dont want to enshrine evil node behavior into upstream code or add flag to do this. Interceptor allows me to check this into the main branch for test only purpose and still simulat the behavior i want. The interceptor can be used for caes like n payload is late, n+1 proposer built on ful anyway, n+1 attesters voted n, empty... etc
[2026-04-17T22:29:26.090000+00:00] barnabasbusa: we have rpc snooper that can sit between bn vc, and we can add any kinda manipulation to this snooper as well.
[2026-04-17T22:30:26.597000+00:00] barnabasbusa: https://github.com/ethpandaops/rpc-snooper
[2026-04-17T22:31:23.279000+00:00] barnabasbusa: would be happy to get some prs with use cases that you could find valuable I’m sure that way other clients could also use it, and we could create reproducible errors
[2026-04-17T23:31:03.738000+00:00] potuz: I can open issues with situations I'd like to test, but definitely not PRs 😂
```

</details>
