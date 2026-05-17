# Solution proposals - second queue implementation

---

## 2026-05-08 (epbs)

The discussion centers on managing deposits in Ethereum's builder infrastructure, specifically addressing the challenge of handling large numbers of deposits (up to 8192) that could cause performance issues due to signature verification requirements. The core debate is between three approaches: (1) implementing a second queue with deferred signature verification, (2) lowering SSZ limits and making execution layer clients aware of constraints, or (3) simply raising SSZ limits and accepting the computational cost.

Multiple participants discuss the tradeoffs of different deposit limits per block, with 16 being considered too restrictive (easily allowing queue hogging for just 1024 ETH over 64 epochs) while 512 may be acceptable but computationally expensive for signature verification. A key technical challenge emerges around race conditions when using separate queues for builders versus validators with the same public key, leading to potential front-running attacks. The proposed solution involves processing deposits based on withdrawal credentials and moving misplaced deposits to the appropriate queue, though this introduces complexity around queue ordering invariants.

An alternative approach of limiting all deposit types at the execution layer (capping at 512 deposits per block) is suggested as potentially simpler than implementing dual queues, as it would address both builder deposits and top-up deposit issues while leveraging mempool prioritization. The discussion reveals ongoing tension between capital efficiency (lower deposit requirements to avoid pricing out smaller builders) and system security/performance constraints.

**Participants:** 0xunclebill, barnabasbusa, jtraglia, m.kalinin, parithosh, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-08T00:56:57.326000+00:00] jtraglia: Surely ELs can just be smart enough to not create a payload with more than the SSZ limit.
[2026-05-08T00:57:38.921000+00:00] jtraglia: I feel like there are two options: (1) queue deposits and sig verifications + raise the SSZ limit or (2) lower the SSZ limit and make ELs aware of this.
[2026-05-08T00:57:59.268000+00:00] jtraglia: But you are saying: (3) just raise the SSZ limit and hope it's not an issue because it's expensive.
[2026-05-08T01:00:05.867000+00:00] jtraglia: Isn't 200m gas limit the _floor_? I thought we were shooting for like 400m gas in the near future.
[2026-05-08T01:01:26.338000+00:00] jtraglia: <@808969530608451584> what happens in your kurtosis devnet when there are 8192 new builder deposits? How long does it take to recover?
[2026-05-08T05:02:27.114000+00:00] m.kalinin: i think we need a second queue, <@592004585506340885> you closed your PR too early 😄 
the second queue makes sense as the first one has the activation queue limitation, i don't think we want to have a different scan over the same queue as it's the complexity while the semantics similar to the first queue. I believe all the checks should be done upon dequeuing then there shouldn't be any conflict as far as i can think of
[2026-05-08T05:02:35.130000+00:00] m.kalinin: i'll try to open a PR
[2026-05-08T05:09:26.079000+00:00] m.kalinin: > Whenever there can be a race we need to validate signatures at the insertion point, which defeats the purpose entirely of the extra queue.
Why at the insertion point?
[2026-05-08T07:41:56.910000+00:00] parithosh: wouldn't it be easier to enforce the limit on the EL block building logic instead of a second queue? if we have over x deposits in a block then consider it invalid?
[2026-05-08T07:52:32.728000+00:00] m.kalinin: it depends on the limit
[2026-05-08T07:52:44.878000+00:00] m.kalinin: if it's a 512 it might still be high. If it's 16 then it's probably fine from sig verification perspective, but quite restrictive from the deposit inclusion perspective
[2026-05-08T08:27:28.588000+00:00] barnabasbusa: with 16 we can easily see someone just hogging the queue all the time
[2026-05-08T08:27:42.523000+00:00] barnabasbusa: with 512 the hogging becomes a lot more expensive
[2026-05-08T08:28:25.356000+00:00] m.kalinin: are we fine with 512 sig verifications per block?
[2026-05-08T08:28:37.480000+00:00] m.kalinin: Alternative PR: https://github.com/ethereum/consensus-specs/pull/5221
[2026-05-08T08:29:13.865000+00:00] barnabasbusa: 16 is extremly limiting
[2026-05-08T08:29:26.301000+00:00] barnabasbusa: that means with 32 eth you can hog that queue non stop?
[2026-05-08T08:29:56.750000+00:00] barnabasbusa: you setup a dummy bot and keep depositing 16 new builders, then exit them in the following epoch then rinse and repeat
[2026-05-08T08:30:38.367000+00:00] barnabasbusa: also none of this fix the top up deposit issue
[2026-05-08T08:30:57.287000+00:00] m.kalinin: what is the issue?
[2026-05-08T08:30:59.604000+00:00] barnabasbusa: where we still gonna be shit out of luck if anyone does  8193x1ETH top up requests
[2026-05-08T08:31:12.520000+00:00] m.kalinin: ah, that's a different thing
[2026-05-08T08:31:17.440000+00:00] barnabasbusa: yeah
[2026-05-08T08:36:38.862000+00:00] barnabasbusa: but if we end up limiting all deposit types on the EL side, then that could solve the same issues
[2026-05-08T08:37:38.857000+00:00] barnabasbusa: if we say on the EL side we want at most 512 deposits bundled in a deposit, then we don't need a queue, don't need to worry about top up deposits reaching 8193+, or how much time it would take for signature verification to take place and the mempool could take care of the priority of such deposits
[2026-05-08T08:48:47.103000+00:00] m.kalinin: `MIN_BUILDER_WITHDRAWABILITY_DELAY=64`, so it's there going to be 64 epochs to wait till you can withdraw, if you don't want to wait then you have to have a budget for 64 epochs, which is `64*32*MAX_DEPOSIT_REQUESTS_PER_PAYLOAD` ETH, looks quite expensive. What is the benefit of such an attack?
[2026-05-08T08:49:27.237000+00:00] barnabasbusa: `64*16*1`ETH no?
[2026-05-08T08:49:33.689000+00:00] barnabasbusa: thats only 1024 ETH
[2026-05-08T08:49:53.792000+00:00] m.kalinin: 16 per block
[2026-05-08T08:50:05.475000+00:00] barnabasbusa: `MAX_PENDING_BUILDER_DEPOSITS_PER_EPOCH`
[2026-05-08T08:50:09.643000+00:00] barnabasbusa: this is the new flag you proposed
[2026-05-08T08:50:16.113000+00:00] barnabasbusa: per_epoch
[2026-05-08T08:50:51.141000+00:00] m.kalinin: this is a different thing, if we limit to 16 per payload, not to 512 without introducing a separate queue
[2026-05-08T09:41:09.450000+00:00] potuz: Pawel explained why this is not possible: you can triviallly dos the builder because it only finds out that the tx is not includable after full simulation, and this doesn't cost any money
[2026-05-08T09:43:06.499000+00:00] potuz: Have you read my review? How do you deal with the race without the signature verification?
[2026-05-08T09:44:30.649000+00:00] barnabasbusa: could we introduce a new tx type for deposits? Then limit them by that? Similar to how we filter blob txs due to their new tx type
[2026-05-08T10:47:21.133000+00:00] m.kalinin: if there is a validator with the pubkey exists then the builder deposit will top-up the validator, and vice versa. this happens upon dequeuing
[2026-05-08T10:47:49.440000+00:00] potuz: the problem is front running with invalid signatures!
[2026-05-08T10:48:05.145000+00:00] m.kalinin: how would you create a validator or a builder with invalid signature?
[2026-05-08T10:48:35.702000+00:00] potuz: you don't create them, but we can have the same pubkey in both queues at the same time, which leads to races on which one gets in first
[2026-05-08T10:48:46.754000+00:00] potuz: it can be handled but it complicates things
[2026-05-08T10:50:04.416000+00:00] m.kalinin: if you don't have the right privkey you can't front run
[2026-05-08T10:50:24.300000+00:00] potuz: you can get in the queue
[2026-05-08T10:50:59.382000+00:00] potuz: so the only way of dealing with this is deciding where to put things after dequeuing
[2026-05-08T10:51:02.299000+00:00] m.kalinin: right, and then if that front run with invalid sig is processed first then nothing happens and then the entity with the correct signature is eventually created
[2026-05-08T10:51:20.140000+00:00] m.kalinin: this is the approach
[2026-05-08T10:51:20.363000+00:00] potuz: so if you have different churns for example, then you can simply time your deposits to get into the faster lane
[2026-05-08T10:52:32.656000+00:00] m.kalinin: the decision on which queue to use depends on the withdrawal credentials. There is a fast queue (for builders) and slow queue for validators the one that we had before. If you put thing in the wrong queue then it is either moved to validator queue or builder is topped up as a part of validator deposit queue processing
[2026-05-08T10:52:45.660000+00:00] m.kalinin: have you looked at the PR?
[2026-05-08T10:55:39.196000+00:00] potuz: I haven't, I can guess what it does
[2026-05-08T10:56:17.758000+00:00] m.kalinin: it takes a bit different approach to the previous PR by Justin
[2026-05-08T11:02:11.323000+00:00] potuz: ok, left essentially the same comment as to Justin: there are inherent races in having separate queues, they all come with tradeoffs. In this case I bypassed the validator queue by sending builder deposits. If the churns are different (as they should be) then the situation becomes worse
[2026-05-08T11:02:35.792000+00:00] potuz: because then we will see this hikacking, invalidating the higher churn for builders (that we need for capital efficiency)
[2026-05-08T11:05:33.476000+00:00] m.kalinin: > This way I make sure that my validators are onboarded before other validators that are later in the queue.

Can you explain please how does that happen?
[2026-05-08T11:07:32.135000+00:00] potuz: I just wrote it in the PR:
Step 1. I send one validator deposit in slot N. 
Step 2. In slot N+1 there are 10K deposits for validators. 
Step 3., In slot N+2  I send 16 deposits for builders with the same pubkey

At dequeunig my 16 builders become validators earlier than the second 16 deposited in N+1
[2026-05-08T11:08:47.320000+00:00] potuz: they all top up to the same validator, but all those deposits anyway get in before the others
[2026-05-08T11:08:56.400000+00:00] m.kalinin: they will be queued after those 10K deposits
[2026-05-08T11:09:29.490000+00:00] potuz: no, they aren't, they are queued by withdrawal credentials aren' t they? or you' re doing an O(n) check on each call for the pubkey
[2026-05-08T11:10:13.066000+00:00] potuz: they are by withdrawal credential only
[2026-05-08T11:10:21.435000+00:00] m.kalinin: two checks, for validator and builder set. i hope it's O(1) check in the implementations
[2026-05-08T11:10:29.862000+00:00] m.kalinin: https://github.com/ethereum/consensus-specs/pull/5221/changes#diff-182a3a3013a99e845862ba9dfbdb522953ae271ac6f096bc394745fb31f4aa11R1056-R1058
[2026-05-08T11:10:41.858000+00:00] potuz: you process deposit requests purely based on withdrawal credentials
[2026-05-08T11:11:02.433000+00:00] potuz: so the set of deposits are set in different queues
[2026-05-08T11:11:13.180000+00:00] potuz: then when you process pending deposits you dequeue first validator deposits
[2026-05-08T11:11:16.653000+00:00] potuz: and then stop at 16
[2026-05-08T11:11:27.726000+00:00] potuz: then you process the builder deposits and you find out that these were validators
[2026-05-08T11:11:48.873000+00:00] m.kalinin: right, and then these deposits will be moved to the end of the validator queue
[2026-05-08T11:12:00.284000+00:00] potuz: oh I see, you add them back to the pending queue again
[2026-05-08T11:12:35.005000+00:00] potuz: hmmm, so now I can for free take space of both queues
[2026-05-08T11:12:44.230000+00:00] potuz: but at least I can't frontrun
[2026-05-08T11:12:45.629000+00:00] potuz: yeah
[2026-05-08T11:13:35.317000+00:00] m.kalinin: yes, i am not sure this is a problem. we can skip counting builder deposits in the validator queue and vice versa
[2026-05-08T11:13:37.280000+00:00] potuz: you probably want to increase the index only in the ` else:`  branch there then
[2026-05-08T11:16:58.664000+00:00] m.kalinin: we can also move the builder deposit from the validator queue instead of processing it to prevent any races
[2026-05-08T11:18:32.028000+00:00] potuz: ahh lol I just left that comment in the PR
[2026-05-08T11:19:43.287000+00:00] potuz: yeah this solution works, it' s very similar to what we had originally, the complain was because many people wanted to have builders immediately onboarded and to not have to manage churn, it seems that priorities have changed...
[2026-05-08T11:36:28.206000+00:00] potuz: I left a few more comments, another invariant that is a bit weird is that ` deposit.slot`  is no longer increasing in the queue
[2026-05-08T11:39:50.470000+00:00] m.kalinin: is this an issue? at least in validator deposit queue this invariant doesn't exist
[2026-05-08T11:41:11.423000+00:00] m.kalinin: finalization is monotonically non-decreasing, so if an older deposit is at the end of the queue then it will be processed eventually
[2026-05-08T11:41:18.190000+00:00] potuz: I am not sure if this is an issue
[2026-05-08T11:43:12.031000+00:00] potuz: As for the number of builders, I do not really know the answer, but the problem I can see is that it is very cheap for say Titan or the usual builders that will win the first blocks, to prevent others from entering the game
[2026-05-08T11:43:27.180000+00:00] potuz: it may be very profitable for them to fill the queue
[2026-05-08T11:44:33.784000+00:00] m.kalinin: if this is a problem then I would shuffle the queue at the fork transition
[2026-05-08T11:44:43.171000+00:00] potuz: lol
[2026-05-08T11:45:31.984000+00:00] potuz: life would be much easier if we had requested 32 ETH  to be honest
[2026-05-08T11:47:42.254000+00:00] m.kalinin: i believe you tried to argue for that hard, didn't you? what was the main opposition?
[2026-05-08T11:49:41.185000+00:00] 0xunclebill: i think the argument was that 32eth prices out the smaller builders
```

</details>

