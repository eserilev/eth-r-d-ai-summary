# ePBS Builder Deposits DoS Vector

---

## 2026-05-06 (epbs)

pk910 raised a potential DoS vulnerability in the planned ePBS (enshrined Proposer-Builder Separation) system. With gas limit increases to 100M+ and the ability to process up to 8192 builder deposits (193M gas) in a single slot, validators and builders could be forced to perform up to 8192 signature verifications in-slot during worst-case scenarios. This represents a significant increase in computational load compared to the current deposit system, which uses deferred signature verification through a rate-limited deposit queue.

The core question being discussed is whether all new builders should bypass the existing deposit queue or if only balance top-ups should be allowed immediate processing while new builder registrations continue through the rate-limited queue. The trade-off is between mitigating the potential DoS vector and ensuring builder availability at the fork boundary, as rate-limiting new registrations would prevent builders from being immediately available when ePBS activates.

This appears to be an ongoing design consideration with no resolution presented in the discussion, requiring further analysis of the security implications versus system availability requirements.

**Participants:** pk910

<details>
<summary>Raw messages</summary>

```
[2026-05-06T11:37:18.899000+00:00] pk910: Moving over a conversation from the Interop Repricings channel:

With ePBS and planned gas limit increases to 100M+, we now allow up to 8192 builder deposits (193M gas) in a single slot to be processed immediately. Unlike the old deposit flow (where sig verification was deferred/rate-limited via the deposit queue), this means validators/builders may need to perform up to 8192 signature verifications in-slot in the worst case.

This sounds like a lot and might be a dos vector,  so the question is whether new builders should really bypass the queue, or whether only balance top-ups should bypass it while new builder registrations remain rate-limited through the normal deposit queue.
This would come with the downside of not having builders available at the gloas fork boundary.
```

</details>

---

## 2026-05-07 (epbs)

The discussion centers around implementing a pending builder deposit queue to handle deposit processing performance issues. Potuz expressed strong opposition to adding a separate queue, citing concerns about introducing new consensus bugs, race conditions between builder and validator pubkeys, and the complexity of additional testing required during devnet stages. He advocated for simpler alternatives like capping deposit numbers or using forkchoice reorgs instead.

Jtraglia initially spec'd out a new pending deposit queue (PR #5219) but encountered complications with handling top-up deposits and race conditions. After finding bugs and realizing the complexity issues, he closed the PR and agreed that a separate queue was too complex. The group discussed using a shared queue where processing order determines winners, avoiding signature verification duplication, though this would complicate processing logic.

The conversation shifted toward benchmarking as a priority, with terencechain reporting significant optimization improvements (from 7 seconds to 50ms for processing 8192 deposits). The group decided to move forward without the queue implementation for now, focusing on benchmarking to determine if the deposit processing issue constitutes a real attack vector. Action items include creating assertoor tests for deposit validation scenarios and increasing the SSZ limit, with the understanding that the expensive attack ($20m mentioned) doesn't affect chain liveness.

**Participants:** barnabasbusa, jtraglia, m.kalinin, pk910, potuz, terencechain, tersec

<details>
<summary>Raw messages</summary>

```
[2026-05-07T15:15:37.057000+00:00] m.kalinin: <@755590043632140352> why you don't like pending builder deposits queue? (decided to move here)
[2026-05-07T15:17:05.354000+00:00] potuz: many reasons but mostly because it's a large change in devnet stages. We will need to deal with new bugs creeping out, for example races between builder pubkeys and validator pubkeys being inserted at the same time on the queues, different ordering for these operations, etc.
[2026-05-07T15:17:41.887000+00:00] potuz: capping the number of deposits, or even reorging with forkchoice the payloads that don't seems simpler
[2026-05-07T15:18:19.711000+00:00] potuz: if we get to have to add the queue, we'll need comprehensive testing, the deposit handling was so far the place where we had the vast majority of consensus bugs merged into the spec
[2026-05-07T15:19:08.367000+00:00] m.kalinin: yeah, i see
[2026-05-07T15:20:50.228000+00:00] potuz: the race situation is not trivial: I do not see an easy way out but to anyway verify the signature immediately so that it's likely that the queue does not help at all
[2026-05-07T15:21:59.528000+00:00] m.kalinin: the race situation can be handled on dequeuing
[2026-05-07T15:22:11.951000+00:00] potuz: may be, yes
[2026-05-07T15:22:48.711000+00:00] potuz: haven't thought much about it, it does sound like a non-trivial number of bugs can be created
[2026-05-07T15:26:02.554000+00:00] potuz: can we prioritize benchmarking the builder deposit issue? perhaps getting some benches from clients on how many deposits we can actually handle on block processing, it would be good, if the current limit can be handled, then we can probably deal  with this with simpler systems
[2026-05-07T15:30:31.003000+00:00] m.kalinin: we have 16 sigs limit at epoch boundary today, we could get back to 16 per block as it was before 6110. But I don't know if having 512 per block is reasonable
[2026-05-07T15:33:40.113000+00:00] tersec: see also https://discord.com/channels/595666850260713488/1501548412242493490/1501618480112210020 for more discussion of the builder deposit signatures
[2026-05-07T15:34:03.287000+00:00] tersec: but they're fairly intensive
[2026-05-07T20:23:53.586000+00:00] jtraglia: <@425572898787426305> <@755590043632140352> <@412614104222531604> and others... I spec'd out what a new pending deposit queue might look like for builders. I sort of like it. What do y'all think? https://github.com/ethereum/consensus-specs/pull/5219
[2026-05-07T20:25:59.917000+00:00] jtraglia: I considered re-using the same `state.pending_deposits` queue, but found it to complicate things. A new queue is easier.
[2026-05-07T20:25:59.924000+00:00] barnabasbusa: we need to make sure normal 0x00,0x01,0x02 deposits and top ups for 0x02 also handled by the same/similar queue mechanism
[2026-05-07T20:26:57.031000+00:00] jtraglia: Hmm yes, this reminds me that I need to handle top-up deposits better.
[2026-05-07T20:29:30.033000+00:00] jtraglia: Ugh I'm going to need to make some bigger changes...
[2026-05-07T20:45:47.104000+00:00] tersec: why again was it so terrible to require builders to have 32 ETH?
[2026-05-07T20:46:01.571000+00:00] tersec: the original 0x03 validator design
[2026-05-07T20:56:04.401000+00:00] potuz: Don't start that one again 😆 , you'll get leverage enough to remove ePBS or trustless payments altogether. Otoh I said above why I don't like the queue <@592004585506340885>, I'll review in a bit though
[2026-05-07T20:58:56.311000+00:00] jtraglia: Ah yeah, please no...
[2026-05-07T21:17:33.700000+00:00] potuz: left the review stopping at the first bug I found
[2026-05-07T21:18:34.247000+00:00] potuz: I think these races may be solvable, but just the fact that we have to start thinking about them and probably introduce another consensus breaking or UX breaking bug scares the hell out of me. I would rather stop this by capping, or simply take the attack and let CL clients be dossed until all signature verifications pass.
[2026-05-07T21:18:57.253000+00:00] potuz: The attack itself is incredibly expensive and it does not affect liveness of the chain
[2026-05-07T21:21:48.732000+00:00] jtraglia: I mean $20m is expensive but really not that much. I suppose you want to just reduce the ssz limit?
[2026-05-07T21:22:27.357000+00:00] jtraglia: Not surprised my PR has a bug. It was done pretty quickly. Will work on fixing it.
[2026-05-07T21:23:37.096000+00:00] jtraglia: Maybe there must just be a single queue.
[2026-05-07T21:24:20.756000+00:00] jtraglia: Even if we don't go this route, it's still useful to prototype. So we know why it's bad.
[2026-05-07T21:26:44.478000+00:00] jtraglia: But this is pretty annoying to deal with too. Not sure if there's a good solution here.
[2026-05-07T21:49:28.376000+00:00] jtraglia: I've decided to close the PR. Yeah another queue is too complex.
[2026-05-07T21:56:43.569000+00:00] potuz: We can probably deal with this with a shared queue as it was originally
[2026-05-07T21:57:24.234000+00:00] potuz: But then we'll have to deal with either 1) the builder's rage or 2) scan in the same queue independently
[2026-05-07T21:57:40.279000+00:00] potuz: That's the only solution I can think of
[2026-05-07T21:57:52.044000+00:00] jtraglia: Pretty sure these would still require a signature verification though.
[2026-05-07T21:58:00.132000+00:00] jtraglia: Which is what we were trying to avoid in the first place.
[2026-05-07T21:58:18.194000+00:00] potuz: Not if the queue is shared
[2026-05-07T21:58:33.362000+00:00] potuz: Whatever is processed before wins
[2026-05-07T21:59:07.957000+00:00] jtraglia: Oh yeah. Agreed. Sorry I thought you were saying something different. I said the same here.
[2026-05-07T21:59:48.985000+00:00] potuz: Yes that's possible but the processing becomes more complicated
[2026-05-07T22:00:31.607000+00:00] potuz: I say we move on, knowing this issue exists and try to bench if it's actually an attack or not
[2026-05-07T22:00:41.230000+00:00] potuz: We need to increase the Ssz limit though
[2026-05-07T22:01:21.210000+00:00] jtraglia: Wouldn't that make things worse?
[2026-05-07T22:01:56.056000+00:00] jtraglia: <@363800010518822915> said it took 7s to process 8192 deposits.
https://discord.com/channels/595666850260713488/1501968885434421339/1501985572410097726
[2026-05-07T22:02:34.096000+00:00] terencechain: that's before my optimization though, i already solved it in the latest commit.. 50ms now
[2026-05-07T22:02:40.525000+00:00] jtraglia: Hell yeah. Okay cool.
[2026-05-07T22:03:00.177000+00:00] jtraglia: What is the optimization? What if there's an invalid signature mixed in there?
[2026-05-07T22:13:36.259000+00:00] terencechain: still benchmarking this.. but i think the best strategy is something like divide and conquer but with a cutoff to linear
[2026-05-07T22:24:44.362000+00:00] jtraglia: <@808969530608451584> <@412614104222531604> for tomorrow, could we have an assertoor test which has 8192 deposits where 1 sig is invalid, 10 sigs are invalid, 100 sigs are invalid, 1000 sigs are invalid. It should be somewhat randomized too.
[2026-05-07T22:27:56.800000+00:00] pk910: what's the reason behind a test with invalid signatures?
we've just created a test to do valid builder deposits (all unique):
```yaml
# kurtosis config snippet
assertoor_params:
  image: ethpandaops/assertoor:master
  run_stability_check: false
  run_block_proposal_check: false
  tests:
    - file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/master/playbooks/gloas-dev/builder-deposit-spam.yaml"
      config:
        batchSize: 200
        pendingBatches: 5 # 5*200 = 1000 deposits per block
        totalDeposits: 0  # 0 = unlimited
```
[2026-05-07T22:39:22.374000+00:00] jtraglia: I was thinking some clients my try to optimize this with an aggregate signature check. But invalid signatures would force them to check them individually, which is slower.
[2026-05-07T22:41:45.983000+00:00] jtraglia: Given they are different messages (deposits), I guess that's not possible. Ignore me. I'm tired.
```

</details>

---

## 2026-05-08 (epbs)

The discussion centers on a vulnerability where builders can be easily subjected to denial-of-service attacks. As Pawel previously explained, attackers can DoS builders by submitting transactions that appear valid initially but are discovered to be non-includable only after the builder performs full simulation - a process that costs the builder computational resources while costing the attacker nothing.

Potuz raises a follow-up question about signature verification as a potential mitigation, asking how race conditions would be handled without implementing signature verification. This suggests there may be ongoing review of solutions to address both the DoS vulnerability and associated timing issues, though the specific details of the proposed signature verification approach and the race condition concerns are not elaborated in these messages.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-08T09:41:09.450000+00:00] potuz: Pawel explained why this is not possible: you can triviallly dos the builder because it only finds out that the tx is not includable after full simulation, and this doesn't cost any money
[2026-05-08T09:43:06.499000+00:00] potuz: Have you read my review? How do you deal with the race without the signature verification?
```

</details>

---

## 2026-05-12 (epbs)

721orbit reported testing issues with EPBS (Enshrined Proposer-Builder Separation) builder changes using rbuilder against glamsterdam-devnet-3. The problem occurs after the "gloas" activation when one of their consensus layer nodes (cl-3) gradually falls behind and eventually gets stuck on slot 147, stopping the import of self-built blocks. Prior to gloas activation, all three consensus layer nodes (cl-1, cl-2, cl-3) work correctly and follow the chain properly.

The issue appears to stem from rbuilder's bids not being selected by proposers, who then fall back to self-building blocks, possibly due to the bids being invalid or uncompetitive. 721orbit is using specific docker images including ethpandaops/reth:bal-devnet-6 for the execution layer and ethpandaops/prysm-beacon-chain:bharath-123-buildoor-apis for the consensus layer, with the builder deposit transaction made during epoch 4.

Potuz responded by noting that the issue appears to be related to 721orbit's own branch, suggesting the problem may be in their custom implementation rather than the base protocol. The discussion leaves open questions about the root cause of the bid selection failures and whether this represents a broader issue with the EPBS implementation.

**Participants:** 721orbit, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-12T18:50:41.655000+00:00] 721orbit: Im trying to test epbs related changes for rbuilder against glamsterdam-devnet-3 and have been running into the following issue since yesterday. Not really sure why it's being caused, but might be a bug in cl.
  
Basically my cl-3 always falls behind and then gets stuck on a single slot. It stops importing self built blocks. Pre gloas everything is working fine, cl-1, cl-2, cl-3 all peer and follow the chain. Once gloas activates and rbuilder starts sending out bids, cl-3 gradually falls behind (proposers fall back to self build because rbuilders bids aren't being selected, possibly because the bid are invalid/not competitive) and then gets stuck on slot 147 forever.

I make the builder deposit transaction somewhere in epoch 4

images i am using:
```
  - el_type: reth
    el_image: ethpandaops/reth:bal-devnet-6                                                                                 
    cl_type: prysm                                                                                                               
    cl_image: ethpandaops/prysm-beacon-chain:bharath-123-buildoor-apis                                                                      
    cl_extra_params:                                                                                                             
      - --prepare-all-payloads                                                                                                   
    vc_type: prysm                                                                                                               
    vc_image: ethpandaops/prysm-validator:glamsterdam-devnet-3 
    count: 2   
```
[2026-05-12T19:34:53.307000+00:00] potuz: <@1386598056320831509> that's your branch it seems
```

</details>

