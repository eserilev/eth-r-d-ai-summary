# Builder Deposit DoS Attack and Queue Solutions

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on a DoS vulnerability where malicious builders can flood the deposit queue with signature verifications, preventing legitimate builders from entering or processing top-ups/withdrawals. Potuz argues that the proposed queueing mechanism doesn't fundamentally solve the problem because there's an inherent tradeoff: if signature verification limits per slot are low, it's cheap for attackers to DoS others; if limits are high, nodes still can't process all deposits. The core issue is that even with $37M USD, top builders can trivially clog the entire queue.

Several mitigation strategies were debated, including increasing minimum deposits, extending withdrawal churn periods (originally 4096 epochs, reduced to 64 epochs due to pushback about capital costs), adding exit fees, or moving signature validation to the EVM with gas costs. The team recognized that attack costs should match the benefits to attackers, with suggestions ranging from making attacks more expensive to improving node handling capabilities. Some proposed more radical solutions like disabling builder exits entirely or using separate deposit contracts for builders.

The discussion concluded without consensus, with participants agreeing they need to benchmark the actual impact before making spec changes. Barnabasbusa noted that even 1500 builder deposits caused chain struggles on testing hardware, while the theoretical maximum is 8,192 signatures per slot. The team planned to continue the discussion in Monday's ACDT meeting and potentially conduct a voice call to brainstorm further solutions.

## Participants

- barnabasbusa
- jtraglia
- m.kalinin
- pk910
- potuz
- sauliusgrigaitis
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
[2026-05-08T13:11:42.864000+00:00] jtraglia: No more bets lol.
[2026-05-08T13:12:24.176000+00:00] jtraglia: But I'll buy you a beer after Glamsterdam regardless 🙂
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
[2026-05-08T13:17:28.730000+00:00] jtraglia: Another nice benefit of the queue'd approach is that deposits are only made to builders after they are finalized. This will greatly simplify sites like beaconchain.
[2026-05-08T13:18:38.623000+00:00] m.kalinin: we would need to restrict builder deposits, while we want small builders to be in that set -- there is a conflict here
[2026-05-08T13:19:04.150000+00:00] potuz: I think we are all talking past each other: the queue does not solve the underlying issue. We probably should schedule Monday's ACDT to discuss most of it
[2026-05-08T13:19:56.244000+00:00] m.kalinin: this is me on Monday's ACDT 😄
[2026-05-08T13:20:22.869000+00:00] barnabasbusa: we can probably hop in a call now to brainstorm some more ideas
[2026-05-08T13:21:04.779000+00:00] jtraglia: Link?
[2026-05-08T13:21:21.265000+00:00] jtraglia: Maybe a voice chat thing here on discord?
[2026-05-08T13:21:39.171000+00:00] m.kalinin: we have <#814925424726900766>
[2026-05-08T13:22:31.010000+00:00] m.kalinin: <@755590043632140352> will you join?
[2026-05-08T13:22:55.150000+00:00] potuz: I'm out of electricity
[2026-05-08T13:23:05.585000+00:00] m.kalinin: why are you here?
[2026-05-08T13:23:08.661000+00:00] potuz: Can join in a couple of hours if they return it
[2026-05-08T13:23:22.097000+00:00] potuz: Cell but that won't work for a meeting
[2026-05-08T13:23:26.367000+00:00] pk910: I'll gonna do a PoC for the queued builder deposits on EL side.  I still think it's easier and makes most sense to charge sig validation fees there.
But don'T feel like I can defend this idea right now without further exploration work
[2026-05-08T13:23:52.110000+00:00] barnabasbusa: for voice it should work fine
[2026-05-08T13:24:13.836000+00:00] potuz: Ok should I go to the lounge?
[2026-05-08T13:24:24.657000+00:00] barnabasbusa: ye
[2026-05-08T13:24:31.989000+00:00] potuz: Just a sec
[2026-05-08T13:24:32.445000+00:00] barnabasbusa: no need to show yourself in 4k lol
```

</details>
