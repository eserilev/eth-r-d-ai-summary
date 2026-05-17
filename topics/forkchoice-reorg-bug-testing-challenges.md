# Forkchoice Reorg Bug and Testing Challenges

---

## 2026-04-17 (epbs)

Potuz discovered a subtle bug in Prysm's proposing code where forkchoice correctly handled a reorg from a full block at slot N to an empty block, but the client prepared payload attributes for the wrong block. The bug occurs when the chain head advances on a full block but attesters reject the payload, causing a reorg to an empty block - while forkchoice logs the reorg correctly, the proposing code mistakenly prepares payloads on top of the full block instead of the empty one. This represents a broader class of bugs where there's a mismatch between the forkchoice head and the block being prepared, which current forkchoice tests don't catch since they only verify the correct head selection.

The discussion focused on testing strategies for these complex scenarios that involve specific timing and attestation patterns. Several solutions were proposed: sproul suggested integration tests within individual clients but noted the lack of cross-client solutions, while terencechain described using an interceptor between beacon nodes and validator clients to mutate/delay/drop messages without adding "evil" behavior to upstream code. Barnabasbusa offered the existing rpc-snooper tool that can manipulate RPC communication between beacon nodes and validator clients, suggesting it could be extended for these test cases.

The conversation highlighted that these bugs pose liveness risks and become more complex with Gloas (likely referring to a protocol upgrade). While fuzzers were considered, potuz expressed skepticism about their effectiveness for forkchoice-driven reorg scenarios. The discussion concluded with potuz offering to open issues describing test scenarios, indicating this remains an open problem requiring further coordination on cross-client testing approaches.

**Participants:** barnabasbusa, potuz, sproul, terencechain

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-20 (epbs)

The discussion centers around m.kalinin's proposal to enhance fork choice (FC) tests by including a proposal step that would check `payloadAttributes` sent to the execution layer (EL) and verify the proposer head using `get_proposer_head`. This enhancement aims to address coherency issues between the proposal routine and fork choice where inconsistent data is sent to the EL, as described by Potuz in a previous discussion.

Tersec agrees with the concept but emphasizes that tests should remain runnable with only a consensus layer (CL) in isolation, suggesting that basic data validation doesn't require a real EL. However, he highlights a practical implementation challenge: Nimbus currently cannot use FCR (Fork Choice Rule) with Nethermind due to an existing issue, which would force test implementations to either fake data, provide problematic data, or skip test portions - preventing tests from reflecting actual CL behavior.

The conversation reveals tension between the desire for comprehensive FC testing and the complexity of real network integration. While m.kalinin suggests this Nethermind issue should be caught by Hive tests, the immediate problem remains that enhanced FC tests would need to work around EL compatibility issues, potentially undermining their effectiveness in testing real-world scenarios.

**Participants:** m.kalinin, tersec

<details>
<summary>Raw messages</summary>

```
[2026-04-20T03:54:24.488000+00:00] m.kalinin: What if we include proposal step into FC tests? With this the `payloadAttributes` a client sends to EL can be checked. `get_proposer_head` can be used to get the head that the proposal would use in each case. This would make FC aware of Engine API, but it's probably fine. Then we could add this step to our compliance test generator and create manual tests checking particular edge cases.

It's not necessarily should be a real proposal, but rather a check on the payload attributes and proposer head.
[2026-04-20T04:08:35.891000+00:00] tersec: as long as the tests can still be run with only a CL, in isolation
[2026-04-20T04:09:07.283000+00:00] tersec: i.e. once it becomes a "real" network test might as well use mechanisms designed to orchestrate that better (Hive, Kurtosis, etc)
[2026-04-20T04:13:01.363000+00:00] m.kalinin: I see the problem Potuz described as the following: proposal routine isn't coherent with the fork choice and calls EL with inconsistent data. Doing a basic check on the data doesn't require a real EL here
[2026-04-20T04:15:22.474000+00:00] tersec: yes, agree
[2026-04-20T04:18:20.326000+00:00] tersec: one general note is that right now, Nimbus (nor any other CL) actually can't use FCR with Nethermind: https://github.com/NethermindEth/nethermind/issues/11185
[2026-04-20T04:18:34.647000+00:00] m.kalinin: > I feel that creating scenarios and actually implementing them in client images to run in Kurtosis quickly blows up in complexity. 

This is one of the major problems with the FC tests as we have a very large variated of system states. Devnets help to address, but i don't see any better way than fuzzing or model based (any other alternative) + fuzzing approach to tackle this problem. At least now
[2026-04-20T04:19:02.807000+00:00] tersec: i.e. to some extent we'd fake things to pass any such EL-data test
[2026-04-20T04:19:10.308000+00:00] tersec: because there's no other choice
[2026-04-20T04:19:12.735000+00:00] m.kalinin: This should have a Hive test cc <@892053833121923094>
[2026-04-20T04:19:39.812000+00:00] tersec: and/or just skip those parts of the tests (more likely I guess?)
[2026-04-20T04:38:12.606000+00:00] m.kalinin: but the NM is an EL problem, not a CL one. And if there were a Hive test for this specific we would caught it much earlier
[2026-04-20T04:39:56.885000+00:00] tersec: doesn't matter we can't ship it
[2026-04-20T04:40:22.817000+00:00] tersec: and for test purposes, see above
[2026-04-20T04:42:36.016000+00:00] m.kalinin: I am not sure I get your point. Are you saying that we need a test env with real EL regardless?
[2026-04-20T04:45:40.068000+00:00] tersec: no
[2026-04-20T04:46:28.302000+00:00] tersec: I'm saying that the way Nimbus would have to test this is to one of (a) not provide the test the data it would provide to the EL; (b) intentionally provide ELs data which gets Nethermind wedged; or (c) skip that part of the test
[2026-04-20T04:46:40.499000+00:00] tersec: i.e. it wouldn't test what the CL does its usual flow
[2026-04-20T04:52:16.178000+00:00] m.kalinin: I see
```

</details>

---

## 2026-05-04 (epbs)

This discussion centers around the placement of implementation details within Ethereum's consensus specifications, specifically whether certain functionality should be added to the fork-choice spec or the validator spec. The conversation appears to be incomplete, with pk910's message being cut off mid-sentence.

Potuz proposes adding a helper function to the fork-choice implementation, similar to a `should_build_on_full` function from their DA (Data Availability) PR, but notes that this helper would only be called from the validator.md specification file. This creates a structural question about where the functionality should logically reside within the spec organization.

The discussion reveals a dependency issue, as Potuz mentions their DA PR (#5186) is currently waiting for another contributor's PR to be resolved before they can determine how to proceed. This suggests there are related changes in progress that may affect the final implementation approach.

**Participants:** fradamt, pk910, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-04T09:20:44.396000+00:00] fradamt: meaning in the fork-choice spec or in the validator spec?
[2026-05-04T11:20:34.654000+00:00] pk910: @pk910  i believe in case the
[2026-05-04T11:32:29.459000+00:00] potuz: I was thinking on adding the helper to forkchoice (something like should_build_on_full in my DA PR that can be updated for this) but then that helped is only called from the validator.md file. Just as my PR for DA that is still waiting for <@534934855113506836> PR to decide how to proceed. 
https://github.com/ethereum/consensus-specs/pull/5186
```

</details>

