# Builder deposit queue DoS attack and mitigation strategies

---

## 2026-05-08 (epbs)

The discussion centers on a potential DoS vulnerability where malicious builders can flood the deposit queue to prevent other builders from entering or topping up deposits. The core issue is that with current parameters, top builders can trivially spend around $37M to clog the entire queue even with optimized processing (512 deposits per slot), creating a fundamental tradeoff: if signature verification limits are low, DoS attacks are cheap; if limits are high, nodes still struggle to process deposits. The proposed queueing mechanism doesn't fundamentally solve this problem since attackers only face capital efficiency costs and can withdraw and re-enter.

Several mitigation strategies were debated, including increasing minimum deposits, extending withdrawal churn periods, adding exit fees, or improving signature verification handling. There was strong support for significantly increasing the builder withdrawal delay (originally 4096 epochs, compromised to 64 epochs) back to a much larger value, as this would make attacks extremely expensive and provide time for potential slashing via fork if needed. The team also discussed technical optimizations like moving signature verification to epoch transitions, using separate deposit contracts for builders, or implementing EIP-7688 (stable SSZ with progressive containers) to handle unbounded deposit lists.

The discussion concluded that more testing and benchmarking is needed before making major spec changes, particularly testing scenarios with 8,192 builder deposits ($200M). Immediate action items include creating PRs to increase the SSZ limit and builder withdrawal delay, while longer-term solutions like EIP-7688 implementation were considered for future devnets. The team agreed to schedule further discussion in the next ACDT meeting to avoid talking past each other on the fundamental issues.

**Participants:** barnabasbusa, jtraglia, m.kalinin, pk910, potuz, sauliusgrigaitis, tbenr, terencechain, tersec, wemeetagain

<details>
<summary>Raw messages</summary>

```
[2026-05-08T12:08:09.552000+00:00] potuz: We need to be careful with malicious builders preventing others to entry and to top up/withdraw, so the churn to exit I believe should be higher and we should allow more deposits per epochs to be processed.
[2026-05-08T12:09:53.535000+00:00] potuz: Still I think top builders can dos us trivially, we' re saying we can' t handle 512 deposits per slot, but with 37M USD today a builder can still clog the entire queue then, **even if we did allow 512 deposits per slot** and we processed them at epoch transition as this PR suggest
[2026-05-08T12:10:05.901000+00:00] potuz: so I don' t think this queue actually solves the issue of signature verification
[2026-05-08T12:11:11.859000+00:00] potuz: with the current numbers I believe it makes matters worse in fact
[2026-05-08T12:14:31.321000+00:00] m.kalinin: i don't think introducing a queue on CL is more complicated than introducing a similar semantics to EL
[2026-05-08T12:16:19.124000+00:00] m.kalinin: i've pushed some changes:
1) val queue: builder deposit is now moved to the builder queue and doesn't hit val deposit limit per epoch
2) builder queue: sig verifications is the only thing that is rate limited, arbitrary number of top ups or val deposits can be processed; the only limitation is that that arbitrary number should happen before `MAX_BUILDER_DEPOSIT_SIGNATURES_PER_EPOCH` is hit
[2026-05-08T12:17:15.011000+00:00] potuz: The problem is that I don' t think it avoids the signature verification problem. There' s an inherent tradeoff.  Say we cap the number of signatures per slot to N. Then

- We should be able to process 32N signatures at epoch transition with the queue. 
- A builder' s cost to fill the queue is 32N ETH. 

These two are in contention. If N is small, it' s cheap for a builder to DOS others. If N is large, we still can' t process the deposits.
[2026-05-08T12:17:20.489000+00:00] m.kalinin: we have two problems: a number of deposit sig verifications per block and a number of deposits per block due to gas limit. To solve the former we need some queueing in EL
[2026-05-08T12:17:24.499000+00:00] potuz: So how does this queueing mechanism help us here?
[2026-05-08T12:18:38.956000+00:00] potuz: notice that the builder' s cost is only capital efficiency, not really paying that much
[2026-05-08T12:18:44.760000+00:00] potuz: they can simply withdraw and reenter
[2026-05-08T12:19:22.549000+00:00] m.kalinin: > it' s cheap for a builder to DOS others

It either DoS nodes or DoS other builders. i think this is the trade off
[2026-05-08T12:19:39.036000+00:00] potuz: yes, I agree
[2026-05-08T12:19:45.331000+00:00] potuz: and both are bad
[2026-05-08T12:20:03.319000+00:00] potuz: if we let only one builder bid then the chain can also stall
[2026-05-08T12:24:08.667000+00:00] potuz: I really think we need to fundamentally change the point of view here. The attack should be as expensive as the benefits to the attacker. This is the philosophy we should take. Either we make the attack more expensive, or we improve nodes to handle the deposits or we have something in the middle. 

Things that will make the attack more expensive:
- Increase min deposit. 
- Increase withdrwal churn. 
- Increase gas cost per deposit (seems impossible)

Things that will make us improve the handling:
- precompute the requests verification as we did pre-5094. 
- Add a queue as in these PRs (it allows to pipeline the signature handling for later in epoch transition, but it doesn' t fundamentally change how many we can handle). 

Other than this we need to understand the actual impact. It seems to me that we heal in exactly one slot and even zero slot if the next proposer does not reorg these blocks.
[2026-05-08T12:27:55.134000+00:00] m.kalinin: > Increase min deposit.
We will have to introduce activation_epoch and activate builders upon gaining enough stake

> Increase withdrwal churn.
How long builders can wait for exit?
[2026-05-08T12:31:00.048000+00:00] m.kalinin: min deposit and withdrwal churn -- can be modeled as a single attack budget constraint. attach_budget = min_deposit * sigs_per_epoch * withdrawal_delay. It means that instead of increasing min_deposit by `X` times we can multiply withdrawal_delay by `X` with the same outcome
[2026-05-08T12:38:06.805000+00:00] potuz: Yes, this was exactly the reason why I wanted the exit churn to be much much larger
[2026-05-08T12:38:15.023000+00:00] potuz: But people pushed back
[2026-05-08T12:38:29.497000+00:00] m.kalinin: what was the pushback reason?
[2026-05-08T12:41:12.826000+00:00] potuz: Capital cost of builders
[2026-05-08T12:43:52.490000+00:00] tersec: These proposed countermeasures also function by effectively increasing capital cost of builders
[2026-05-08T12:44:28.994000+00:00] m.kalinin: min_deposit implies the same
[2026-05-08T12:45:34.263000+00:00] jtraglia: Originally, the min withdrawal delay for builders was 4096 epochs. Some thought this was excessive and [advocated to remove this delay entirely](https://github.com/ethereum/consensus-specs/pull/4826) but the delay prevented some attacks, so we [compromised at reduced value](https://github.com/ethereum/consensus-specs/pull/4869), 64 epochs.
[2026-05-08T12:46:28.663000+00:00] potuz: I think we need to just make this number very large again and leave the spec as it's
[2026-05-08T12:46:32.130000+00:00] potuz: As is
[2026-05-08T12:46:38.249000+00:00] potuz: The attack cannot be kept
[2026-05-08T12:47:15.776000+00:00] potuz: And if a nation state actually decided to pull it up we have plenty of time to just fork and burn that shitton of ETH
[2026-05-08T12:47:56.785000+00:00] potuz: It wouldn't affect a single user transaction
[2026-05-08T12:48:29.162000+00:00] barnabasbusa: I think 4096 is even too small
[2026-05-08T12:49:10.899000+00:00] barnabasbusa: there is no real reason why a builder would want to quickly deposit/exit unless its an attack (unless I'm missing something)
[2026-05-08T12:49:55.534000+00:00] barnabasbusa: also ignore [my comment ](https://github.com/ethereum/consensus-specs/pull/4826#issuecomment-3785107986) lol, that was from before I got wise 😂
[2026-05-08T12:52:17.222000+00:00] barnabasbusa: Could we not use an alternative deposit queue for builders, but use the validator queue for the exits?
[2026-05-08T12:52:35.167000+00:00] barnabasbusa: or it doesn't make sense to have different queues like that?
[2026-05-08T12:53:09.317000+00:00] jtraglia: Hmm I'm not sure raising this is sufficient. Still feels like a bandaid fix.
[2026-05-08T12:53:24.892000+00:00] jtraglia: I'm still worried what will happen to the network if such an attack were performed for a short time.
[2026-05-08T12:54:29.270000+00:00] sauliusgrigaitis: I admit that it was too long to read, so I'm just proposing to add a fee for becoming a builder. This could be a part of the builder deposit.
[2026-05-08T12:55:16.034000+00:00] barnabasbusa: how much fee?
[2026-05-08T12:55:19.154000+00:00] m.kalinin: how big of a fee?
[2026-05-08T12:55:35.259000+00:00] sauliusgrigaitis: how much is the fish?
[2026-05-08T12:55:57.451000+00:00] m.kalinin: what fee could stop the DoS?
[2026-05-08T12:55:59.010000+00:00] barnabasbusa: A fee won't stop an attacker, just gonna make the attack expensive
[2026-05-08T12:56:15.882000+00:00] sauliusgrigaitis: haha, isn't that how PoS works?
[2026-05-08T12:56:46.622000+00:00] sauliusgrigaitis: I would say we can even increase a deposit. But if you exit you pay a fee.
[2026-05-08T12:56:49.385000+00:00] jtraglia: billions vs millions of USD
[2026-05-08T12:56:59.191000+00:00] sauliusgrigaitis: so technically it's free as long as don't exit
[2026-05-08T12:57:46.927000+00:00] sauliusgrigaitis: and you want to be competitive builder you need to have some money
[2026-05-08T12:57:47.396000+00:00] barnabasbusa: so you gonna lose 1ETH at least per builder?
[2026-05-08T12:58:01.748000+00:00] potuz: Should be just a quick dos on the CL and a very expensive one if we keep the churn large
[2026-05-08T12:58:39.634000+00:00] sauliusgrigaitis: For exact number we need to think a bit. But I don't see why we can't charge for exiting.
[2026-05-08T12:58:50.242000+00:00] sauliusgrigaitis: If you don't want to be charged you just don't exit.
[2026-05-08T12:59:34.180000+00:00] barnabasbusa: Deposit a builder -> 1ETH
Operaate a builder -> 1ETH+
Exit a builder -> -1ETH
[2026-05-08T13:00:20.171000+00:00] barnabasbusa: cost of running a builder would be 1ETH basically. And once you exit, you never get a refund of that 1ETH, so it would be very expensive to do the attack
[2026-05-08T13:00:24.164000+00:00] sauliusgrigaitis: I'm actually not sure why it's as low as 1ETH, my understanding is that if you want to be competitive builder you want to put some money in your setup.
[2026-05-08T13:00:54.637000+00:00] sauliusgrigaitis: I understading that the pitch line is that everyone can be builder, but how is that realistic?
[2026-05-08T13:04:12.620000+00:00] jtraglia: "Should be". Have we tested this? Say after 10 slots with 8192 builder deposits ($200m), testing shows the network will handle it or recover within a few slots. Preferably on low/mid tier hardware.
[2026-05-08T13:05:49.925000+00:00] potuz: Sure, set up the test, wanna bet a beer we recover nicely?
[2026-05-08T13:06:18.083000+00:00] potuz: It's much cheaper than that to today buy 10 slots of Ethereum
[2026-05-08T13:07:12.658000+00:00] potuz: We just need to make sure that they have an actual cost, at least take several days to get their money back
[2026-05-08T13:07:14.508000+00:00] pk910: alternative Idea:
What if we split the deposit contracts?  We could deploy a new builder deposit contract that is queued on EL side and only allows builder deposits / top ups.
That would leave the regular deposit flows untouched,  they still go through the queue before any sig check and we can even bump the ssz limit without problems.
The builder deposits are queued on EL side,  only N deposits get passed to the CL side per slot and immeaditly processed there.
I think that would simplify the double queue logic on CL side, and doesn't break any validation assumptions on the regular validator deposit flow.
[2026-05-08T13:07:42.892000+00:00] potuz: This doesn't fix the problem
[2026-05-08T13:07:52.737000+00:00] potuz: We can always cap. With the queue
[2026-05-08T13:08:07.725000+00:00] m.kalinin: everything less than 1 ETH is lost except you spent that on bidding
[2026-05-08T13:08:41.533000+00:00] barnabasbusa: maybe we can just disable builder exits?
[2026-05-08T13:08:58.683000+00:00] barnabasbusa: a builder can only deposit/top up
[2026-05-08T13:10:17.170000+00:00] pk910: with the separate builder deposit contract, we could push the sig validation to the EVM and charge regular gas for it
[2026-05-08T13:11:37.678000+00:00] m.kalinin: then top-ups will have to be signed with correct sig
[2026-05-08T13:12:29.220000+00:00] pk910: we could still allow 0 sigs bypassing the check
[2026-05-08T13:13:52.543000+00:00] potuz: Point is discussing this without even understanding the impact and deeply changing the spec is crazy. Let's bench this. We need to increase the Ssz limit to not be blocked by the EL and have invalid payloads and we need to increase the exit churn. But we should just bench what happens with 200M being deposited
[2026-05-08T13:14:20.009000+00:00] jtraglia: Totally agree. We need more information.
[2026-05-08T13:14:51.456000+00:00] m.kalinin: 8,192 to be specific; we cannot have more than that regardless of the gas limit
[2026-05-08T13:15:03.731000+00:00] barnabasbusa: I've done yesterday 1500 builder deposits, and already saw the chain struggle on my m1 pro mac
[2026-05-08T13:15:15.399000+00:00] barnabasbusa: lodestar/teku especially
[2026-05-08T13:15:16.501000+00:00] m.kalinin: 8,192 sig verifications, i think it can be benched locally
[2026-05-08T13:15:19.440000+00:00] potuz: This number should be increased I believe
[2026-05-08T13:15:20.394000+00:00] jtraglia: Some are suggesting we raise/remove that limit. So we could do more.
[2026-05-08T13:15:20.714000+00:00] pk910: without the sig check on the hot path, we could even safely increase the limit,  which is basically a no op
[2026-05-08T13:15:50.151000+00:00] potuz: The sig check in the hot path is irrelevant I think
[2026-05-08T13:16:12.929000+00:00] potuz: It is a nice optimization to move to epoch transition but if we don't cap it's still a dos
[2026-05-08T13:18:38.623000+00:00] m.kalinin: we would need to restrict builder deposits, while we want small builders to be in that set -- there is a conflict here
[2026-05-08T13:19:04.150000+00:00] potuz: I think we are all talking past each other: the queue does not solve the underlying issue. We probably should schedule Monday's ACDT to discuss most of it
[2026-05-08T13:23:26.367000+00:00] pk910: I'll gonna do a PoC for the queued builder deposits on EL side.  I still think it's easier and makes most sense to charge sig validation fees there.
But don'T feel like I can defend this idea right now without further exploration work
[2026-05-08T14:20:06.436000+00:00] potuz: If the issue is the loop itself I'm pretty sure we can get rid of this by forking blst
[2026-05-08T14:20:50.756000+00:00] potuz: I could try opening a PR for the go bindings on blst but I doubt they'll accept them
[2026-05-08T14:21:20.981000+00:00] jtraglia: 0% chance lol
[2026-05-08T14:21:42.492000+00:00] terencechain: Then we might have to fork? 
What was the verdict from the party lounge? 
Sorry I was driving
[2026-05-08T14:22:20.952000+00:00] potuz: The fix is simple though. Just add a goassembly wrapper that jumps directly to the assembly instead of using cgo to call it
[2026-05-08T14:22:53.067000+00:00] barnabasbusa: 
[2026-05-08T14:23:08.433000+00:00] potuz: Only problem is that blst does other things besides just EC manipulation so it may be dangerous.
[2026-05-08T14:24:12.304000+00:00] barnabasbusa: so I was wrong in the call where I said we could do 9000 deposits with 200m gas. 

We can do ~7200 builder deposits only with 200M gas 
We can still do over 8192 top up deposits with 200M gas tho
[2026-05-08T14:24:15.844000+00:00] potuz: We need to precompute this
[2026-05-08T14:24:44.398000+00:00] potuz: Topups are not a problem I believe
[2026-05-08T14:25:00.289000+00:00] barnabasbusa: topups will be a problem if they exceed the 8192, its a different kind of problem, its the ssz problem
[2026-05-08T14:25:02.382000+00:00] terencechain: Topups don't verify signature
[2026-05-08T14:25:35.979000+00:00] potuz: But the important point here is that we still need to optimize by precomputing this both in the processing and the proposing paths. Almost undoing 5094 lol
[2026-05-08T14:27:30.875000+00:00] barnabasbusa: state of the chain
[2026-05-08T14:27:48.265000+00:00] barnabasbusa: Anyways, as mentioned before, testing this right now when epbs implementation is still so fragile is worthless.
[2026-05-08T14:27:58.185000+00:00] barnabasbusa: We need to cycle back to this when we are doing the hardening
[2026-05-08T14:28:36.884000+00:00] barnabasbusa: however, 2 things are certain, we gotta increase the withdrawability delay of the builders, and increase the ssz size to maxint/or yeet it completely with stable ssz
[2026-05-08T14:28:49.589000+00:00] jtraglia: I'll make PRs for these.
[2026-05-08T14:29:11.356000+00:00] barnabasbusa: In the meantime client teams should work on optimizing large builder deposits
[2026-05-08T14:29:35.611000+00:00] tersec: stable SSZ is already enqueued basically, no? as in, the default action is that it happens when glamsterdam-devnets achieve some metric of stability
[2026-05-08T14:30:05.705000+00:00] tersec: tradeoff here is that the later one waits the more structurally constrained the remaining feasible changes will be
[2026-05-08T14:30:09.929000+00:00] jtraglia: Yes, a progressive list will handle this too. But in the meantime, we can set the limit to some large value.
[2026-05-08T14:30:50.080000+00:00] tersec: i.e. if it's early enough more interesting changes might remain testable. the later it gets the more it's going to be, tweak parameters, adds some kludge here, etc
[2026-05-08T14:30:57.784000+00:00] barnabasbusa: when can we have the progressivecontainer implemented by all CLs?
[2026-05-08T14:31:51.379000+00:00] tersec: Nimbus has a draft branch ready already, can't speak for other CLs obviously
[2026-05-08T14:32:09.267000+00:00] barnabasbusa: is it based on top of latest alpha 7?
[2026-05-08T14:32:16.860000+00:00] barnabasbusa: how is the consensus spec pr looking for it?
[2026-05-08T14:32:17.794000+00:00] tersec: yes
[2026-05-08T14:32:29.094000+00:00] tersec: (based on alpha.7)
[2026-05-08T14:32:31.491000+00:00] barnabasbusa: could we realistically schedule it in for the next devnet in 1/2 w?
[2026-05-08T14:33:27.863000+00:00] tersec: for Nimbus definitely for 2 weeks, plausibly for 1 week. for other CLs obviously I couldn't say
[2026-05-08T14:35:02.397000+00:00] tersec: https://github.com/status-im/nimbus-eth2/tree/eip-7688
[2026-05-08T14:36:18.092000+00:00] barnabasbusa: <@592004585506340885> instead of increasing it then removing it again with 7688, we should probably just aim to remove it with 7688?
[2026-05-08T14:37:44.840000+00:00] barnabasbusa: <@358120958726373381> <@363800010518822915> <@586161934425128960> <@504202741933932544> <@777935002263617576> would you be ok to schedule in 7688 in about 2w from now in the following devnet ?
[2026-05-08T14:38:37.052000+00:00] barnabasbusa: In the meantime I'm sure we gonna be finding a bunch of other epbs bugs so obviously those should also be ironed out in the next 2 weeks
[2026-05-08T14:48:42.357000+00:00] jtraglia: A PR to raise the min builder withdrawal delay, something I think we should do regardless:
* https://github.com/ethereum/consensus-specs/pull/5223
[2026-05-08T14:50:52.403000+00:00] jtraglia: It's such a simple change. I think we should do this independent of 7688.
[2026-05-08T14:51:21.072000+00:00] barnabasbusa: good time to push for 7688 😄
[2026-05-08T14:51:22.262000+00:00] jtraglia: But then again, it would require a new config value, which is annoying.
[2026-05-08T14:51:30.591000+00:00] jtraglia: Let me think about it more. There's no rush for this one.
[2026-05-08T15:22:15.789000+00:00] tbenr: my problem is that we haven't discussed how to deal with the schema becoming unbounded. If we assume that we will find a solution for that no matter what, i think teku can participate but ATM we can't be fully sure. There are a lot of moving parts for us in merging the interop branch into master.
[2026-05-08T15:29:50.754000+00:00] potuz: This really seemed like a serious issue to me, haven't heard back from anyone on this point
[2026-05-08T15:41:27.546000+00:00] terencechain: I'm not sure. I can ask the team. But my personal vote would be fix and harden the existing code first rather then increasing scope. There's already enough bugs to be fixed. We also should focus on p2p bids and builder api testing before then
[2026-05-08T15:50:41.227000+00:00] wemeetagain: we will just put sensible limits per topic / object on gossip and reqresp
[2026-05-08T15:50:56.753000+00:00] tbenr: My current position is that we could introduce an override (that can be specified on each affected type) which will set a practical upperbound. We can inject this at type level so the thing remains transparent for the machinery using it
[2026-05-08T15:52:52.352000+00:00] tbenr: we could even make it required if a progressive type is involved (so you are forced to declare something and not inherit unbounded size)
[2026-05-08T15:55:17.388000+00:00] sauliusgrigaitis: We don’t have that Ssz implementation, so definitely not in two weeks for us.
[2026-05-08T16:20:39.013000+00:00] jtraglia: Made this PR for this:
* https://github.com/ethereum/consensus-specs/pull/5224
[2026-05-08T16:21:25.573000+00:00] potuz: that's probably 30 something extra hashes to get the root 🙂
[2026-05-08T16:21:42.961000+00:00] potuz: can we make it large but not unreasonably large  so that we can HTR it with zero cost?
[2026-05-08T16:26:16.229000+00:00] jtraglia: Ha yeah. I thought about that. How about 1 million (2**20)?
[2026-05-08T16:28:32.781000+00:00] m.kalinin: <@755590043632140352> once again thinking on the builder frontrunning others. As you mentioned if you want to make a slot empty, you buy a block space and don't reveal a payload rather than DoSing with deposits. So is with this case, if you want to frontrun, you pay for a bid and frontrun. Even with FOCIL, you will be able to frontrun within the same block
[2026-05-08T16:28:53.974000+00:00] potuz: yep,that's only 10 extra hashes max
[2026-05-08T16:29:59.483000+00:00] potuz: yes, this seems correct to me
[2026-05-08T16:30:14.114000+00:00] potuz: dossing the chain with this attack seems silly to me
[2026-05-08T16:30:30.247000+00:00] potuz: but silly attackers with lots of money are not lacking 🙂
[2026-05-08T16:31:16.361000+00:00] m.kalinin: my argument is in favor of queueing as not queueing doesn't prevent frontrun of small builders
[2026-05-08T16:33:58.030000+00:00] potuz: I do not mind queueing (except the fact that it is a change in the spec rather late, it seems to be safe, but we will need to analyze and have new testvectors). But the point remains that capping at the epoch boundary seems silly as well, we would need to handle many more deposits than 16 IMO, and this pushes the happy path from "some per slot" to "several at epoch transition", making the happy case worse
[2026-05-08T16:36:44.578000+00:00] m.kalinin: Oh, I see. So, you want to prevent a *delay* that is introduced by the queue and can be abused by large builders. Like they can create 1,600 new builders and delay everyone else activation by 100 epochs?
[2026-05-08T16:37:30.548000+00:00] potuz: yes, this can be hugely profitable for builders
[2026-05-08T16:37:42.701000+00:00] potuz: specially if their cost is only interest rate over a few weeks
[2026-05-08T16:38:23.019000+00:00] potuz: but also I think it should be easy to handle for us the worst case
[2026-05-08T20:17:15.673000+00:00] jtraglia: Clients, what do we think about this? Is anyone already doing this?
https://github.com/ethereum/consensus-specs/pull/5227
[2026-05-08T21:28:02.229000+00:00] potuz: We don't do this yet. Depending on the outcome of the deposits thing we may be forced
```

</details>

