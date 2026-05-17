# Variable PTC deadline PR discussion and implementation details

**Channel:** epbs | **Date:** 2026-01-21

## Summary

The team is discussing implementation details for a Variable PTC (Payload Timeliness Committee) deadline PR that needs to be merged before the v1.7.0-alpha.2 spec release. The current proposal uses linear interpolation between minimum (3.6s) and maximum (9.0s) deadlines based on payload size, but concerns were raised that this approach gives most payloads very short deadlines since real-world payload sizes follow a heavily right-skewed distribution where most blocks are much smaller than the maximum.

Several alternatives emerged during the discussion. Nero_eth proposed square root interpolation to give smaller payloads more time, while others suggested replacing `MAX_PAYLOAD_SIZE` with a more realistic reference based on `gas_limit//64` (representing the maximum calldata size under EIP-7623's pricing). The team ultimately agreed that linear interpolation is correct from a networking perspective for propagation time, and that using `gas_limit//64` as the reference size makes more sense than the theoretical maximum payload size. However, Francesco noted complications arise because even maximum-size payloads can still use significant gas for execution (75-94% depending on compression), meaning the maximum deadline must account for both propagation and execution time.

Key open questions include finalizing the exact formula parameters, resolving a bug where PTC attesters might incorrectly attest to execution rather than just payload timeliness, and broader architectural questions about whether the current calldata floor cost approach optimally balances execution and data constraints. The discussion suggests this feature is needed for the upcoming fork, but Francesco recommended letting the PR mature further given these unresolved technical considerations.

## Participants

- anderselowsson
- fradamt
- jtraglia
- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-21T02:50:32.085000+00:00] jtraglia: I would like to make the v1.7.0-alpha.2 spec release at the end of this week. We should try to get the following PRs merged before then:
* [Variable PTC deadline](https://github.com/ethereum/consensus-specs/pull/4843)
* [Remove MIN_BUILDER_WITHDRAWABILITY_DELAY](https://github.com/ethereum/consensus-specs/pull/4826) -- ideally an altered version of this with a shorter delay, rather than a full removal. cc <@755590043632140352> <@981249983196041218>
[2026-01-21T02:52:50.475000+00:00] potuz: I think Julian is not available so probably that one will require more input
[2026-01-21T02:57:28.823000+00:00] potuz: I'm fine with Francesco's PR but I think it's better to move the wrapping helper to validator.md to use just human language instead of the store. And just add the helper to get the timelines depending on the size. Another honest question: with real life numbers in which the max Payload is much larger than the average one. Wouldn't this result in most Payloads having a very short deadline? In principle there's nothing wrong here, just that we may be allocating the broadcast time to blocks that may never be produced and will force us to compute precisely the windows so that the normal blocks do travel in time to be attested by the PTC
[2026-01-21T03:09:50.078000+00:00] jtraglia: When possible, I think it's best to have explicit specs. No strong feelings on this. I'll leave it up to <@520034910149410861>. Re the question, we can adjust the `MIN_PAYLOAD_DUE_BPS` config to make the average what we want. Right now the min is 3.6 seconds and the max is 9.0 seconds, so on average the deadline might be around ~6 seconds. This feels reasonable to me.
[2026-01-21T03:10:31.080000+00:00] jtraglia: But we can/should tune this on real-world data 👍
[2026-01-21T03:14:11.837000+00:00] potuz: The point is that the average is not the mid point: most blocks are just a fraction from  the max possible. Anyway, a linear function makes sense. On the other issue: as it stands now it'll be impossible to have the data bit being false and the execution bit being true.  The question is if we want to address this on this PR or in a future one. It's a simple change to add it to this PR though.
[2026-01-21T06:19:51.956000+00:00] nero_eth: Agree here. We deal with a heavily right-skewed distribution, which should be reflected in the formula.
[2026-01-21T06:26:05.151000+00:00] nero_eth: Linear interpolation means a payload at 10% of max size only gets 10% of the extra time. Since most payloads are small, they're all near the minimum deadline.
An alternative might be doing square root interpolation:

```
interpolated_ms = min_ms + integer_squareroot(payload_size * (max_ms - min_ms) ** 2 // MAX_PAYLOAD_SIZE)
```
[2026-01-21T07:48:58.634000+00:00] nero_eth: I'm thinking if MAX_PAYLOAD_SIZE should even be part of that formula at first place. Today it's assumed to be some more-arbitrary boundary that wasn't questioned much. Instead we've made it a hard constrain in eip-7934 for security reasons but we're still far away from it.
E.g. with EIP-7623's calldata pricing and 60m gas, you get to 5.7 MiB. With 7976 having the aggressive 64/64 calldata repricing, and 300m gas, we get to 4.5 MiB in the worst case. 
A reference payload size of 1 MiB acting as a floor might be better, and then square root interpolation:

```
capped_size = min(payload_size, REFERENCE_PAYLOAD_SIZE)
interpolated_ms = min_ms + integer_squareroot(capped_size * delta_ms ** 2 // REFERENCE_PAYLOAD_SIZE)
```
[2026-01-21T07:54:26.809000+00:00] nero_eth: 
[2026-01-21T08:13:21.542000+00:00] nero_eth: i like the cyan curve:
https://github.com/fradamt/consensus-specs/pull/4
[2026-01-21T09:24:08.050000+00:00] fradamt: Yeah, MAX_PAYLOAD_SIZE is not ideal, it's too far from an actual worst case. I just wasn't sure what else to use given the CL has limited knowledge
[2026-01-21T09:25:55.969000+00:00] fradamt: I would definitely let this PR stew for a bit <@592004585506340885>, there's some questions to resolve not only on the PR itself but also on the general way we think about the (propagation + execution) time (tldr: 7623 makes it so that only a small portion of block's gas can be used for calldata, so you can have a max payload that still has a lot of execution. This has consequences on what we should be doing here)
[2026-01-21T11:02:40.958000+00:00] potuz: The thing is that linear interpolation is the right thing from a networking perspective. If we get the numbers right. The payload time to propagation is indeed almost  linear. I'm not sure what is the best approach here but given the numbers Kamil posted in the thread, we will need this feature at the fork so we need to figure out the best thing here. Another comment is that if we make these constants tight then the optimal approach would be to derive the formula for the max payload size in terms of the gas limit and then make this max a function of the gas limit. This is something that was discussed before in the CL for other reasons.
[2026-01-21T11:44:42.442000+00:00] potuz: Left another comment on the PR, apologies I hadn't realized before.  I think there's another bug with regards of the current helper: the  PTC attester will not attest to the payload timeliness but also the execution of it, which is not what we want. I don't think that helper should rely on the store to make these attestations.
[2026-01-21T12:51:20.985000+00:00] nero_eth: deriving it with from the gas limit works fine as of glamsterdam (assuming eip-7981 will be shipped too). We can then just do gas_limit // CALLDATA_FLOOR (likely 64), have that as our reference/cap, and keep it constant as of there, up to MAX_PAYLOAD SIZE.
[2026-01-21T12:53:49.206000+00:00] potuz: just in case you guys weren't aware, there was a push to have such a max being enforced even at the gossip level, when we realized that pushing the limit to 60M could create a network split on the CL before Fulu. <@449019668296892420> was pushing for this. If the formula is only dependent on the gas limit I think this is fine since the CL has access to it from the envelope without much work.
[2026-01-21T12:58:35.223000+00:00] fradamt: The issue is that we would have to change it anytime calldata gas costs change (which tbf might not be often)
[2026-01-21T12:59:35.517000+00:00] potuz: I think that's fine as long as those happen in a hard fork ( we can just coordinate a new constant per fork), if calldata gas cost changes intra fork as the gas limit then this becomes more complicated
[2026-01-21T13:03:43.867000+00:00] fradamt: That's probably not going to be the case
[2026-01-21T13:14:23.758000+00:00] fradamt: I would agree that linear interpolation seems like the correct thing to do ultimately, even if it feels odd that the average case payload would get near minimum propagation time
[2026-01-21T15:12:03.843000+00:00] nero_eth: Yeah agree, This seems like the best approach. And then we use something more realistic like `gas_limit//64` as the reference:
E.g. `capped_size = min(payload_size, gas_limit//64)` in `get_payload_due_ms()`?
[2026-01-21T16:04:53.929000+00:00] potuz: so `gas_limit//64`  is supposed to be the max size of the transaction slice?
[2026-01-21T16:09:36.270000+00:00] nero_eth: It provides an good enough upper-bound yeah. One can get higher even larger valid payloads but this rule of thump gets us very close.
[2026-01-21T16:16:47.074000+00:00] potuz: So tweaking the `MIN_PAYLOAD_DUE_BPS` is equivalent to handling the constant term of the linear (eg the payload size for a payload without any transactions and requests) and `gas_limit//64` is a good replacement for `MAX_PAYLOAD_SIZE`. We probably would still get tight deadlines for average blocks which presumably are nowhere close to `gas_limit//64` anyway?
[2026-01-21T16:43:12.764000+00:00] fradamt: Yeah. In principle the min payload thing should just account for pure latency, and everything on top of that should be accounting for bandwidth and scale linearly up to to the max size
[2026-01-21T17:01:35.083000+00:00] fradamt: We also need to set the max deadline to account for the fact that a max size block can still use 75% of the gas for execution (16 gas per calldata byte plus 48 gas for execution to get to the 64 calldata floor cost), so in principle the max deadline should be `min_payload_due + (slot_duration - min_payload_due) // 4`, so we leave 3/4 of the `(slot_duration - min_payload_due)` for execution
[2026-01-21T17:03:45.035000+00:00] fradamt: To be precise, that's if we can use compressed payload size for the interpolation. If not, we have to be more conservative because a max uncompressed size block can use 60/64 ~94% of its gas for execution (4 gas per 0 byte)
[2026-01-21T18:33:56.535000+00:00] potuz: <@917367578966523934> already has these linear approximations  in his work, we could probably adapt those, abstract some constants away as parameters for testing, and only leave `gas_limit` as the linear variable for the max deadline, and then just adjust the constant at testing time.
[2026-01-21T19:26:40.380000+00:00] anderselowsson: It's interesting that when it comes to execution and calldata, we can envision a multidimensional future where we do not treat them as fully orthogonal/separate. Ultimately, we are constrained by a "Metadimension" that limits the time for the block to propagate and then be executed. Today's aggregate gas measure captures this notion better, while the calldata floor cost with its fully orthogonal specification already favors the "balanced" block a bit too much. Metadimensions would give us a better definition of what a "balanced block" actually looks like.
[2026-01-21T19:51:41.821000+00:00] anderselowsson: So essentially, the calldata floor cost lessens the importance of the variable deadline, since as Francesco points out, even a block with plenty of calldata could still carry a lot of compute. But the calldata floor cost does so, in a way, by removing opportunities for scaling blocks that carry either more calldata or more execution than we currently can accept. We are under the calldata floor cost harmstrung by having to consider that a high amount of execution could also be accompanied by a high amount of calldata. If the distribution of blocks is such that they very rarely use up all the calldata while execution indeed often hits the limit, then the protocol must always consider a worst-case block (room for propagating big blocks) that we hardly ever utilize! By turning the calldata floor cost specification closer to a Metaresource specification that accounts for calldata propagation time, Ethereum would be able to scale quite a bit further.
```

</details>
