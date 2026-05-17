# Second Queue Implementation and PR Discussion

**Channel:** epbs | **Date:** 2026-05-08

## Summary

This discussion centers around m.kalinin's proposal for implementing a second queue system for Ethereum deposits, presented in PR #5221 as an alternative to a previously closed PR. The proposed design uses two separate queues - a fast queue for builders and a slow queue for validators - with queue selection based on withdrawal credentials. When deposits are processed during dequeuing, items placed in the wrong queue would be moved to the correct queue (e.g., builder deposits that should be validator deposits get moved to the end of the validator queue).

potuz raised concerns about potential race conditions and front-running attacks where users could exploit timing differences between queues to bypass normal ordering, particularly if the queues have different churn rates. However, after reviewing the implementation details, potuz acknowledged that m.kalinin's approach prevents front-running by moving misplaced deposits to the end of the appropriate queue rather than processing them immediately. The solution was noted to work similarly to the original design, though potuz mentioned this differs from some stakeholders' preferences for immediate builder onboarding without churn management.

Several technical details remain under discussion, including whether to count builder deposits in validator queue limits, how to handle the invariant that deposit slots may no longer be monotonically increasing within queues, and optimization of the index counting mechanism. The conversation concludes with uncertainty about whether the non-increasing slot ordering presents a significant issue, though finalization monotonicity should ensure eventual processing of older deposits.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T05:02:27.114000+00:00] m.kalinin: i think we need a second queue, <@592004585506340885> you closed your PR too early 😄 
the second queue makes sense as the first one has the activation queue limitation, i don't think we want to have a different scan over the same queue as it's the complexity while the semantics similar to the first queue. I believe all the checks should be done upon dequeuing then there shouldn't be any conflict as far as i can think of
[2026-05-08T05:02:35.130000+00:00] m.kalinin: i'll try to open a PR
[2026-05-08T05:09:26.079000+00:00] m.kalinin: > Whenever there can be a race we need to validate signatures at the insertion point, which defeats the purpose entirely of the extra queue.
Why at the insertion point?
[2026-05-08T08:28:37.480000+00:00] m.kalinin: Alternative PR: https://github.com/ethereum/consensus-specs/pull/5221
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
```

</details>
