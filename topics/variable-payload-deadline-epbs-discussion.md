# Variable Payload Deadline and ePBS Discussion

---

## 2026-03-23 (epbs)

nero_eth expressed strong opposition to the variable payload deadline proposal for ePBS, arguing that anchoring on uncompressed block sizes would not yield practical benefits. The main concerns include having to assume worst-case compression scenarios (where uncompressed equals compressed size) for benchmarking, which would over-allocate propagation time for most blocks and collapse the variable deadline back to effectively a fixed one. Additionally, the heavily right-skewed distribution of block sizes (with worst-case being 20x larger than average) would result in most blocks receiving very early deadlines, potentially negating ePBS's propagation time benefits.

While nero_eth acknowledged that BALs (Block Abstraction Lists) could help builders reach post-state faster through asynchronous validation, potuz countered that this would still create a monopoly period where only one builder could provide preconfirmations. Both agreed that if variable deadlines are implemented, they should use compressed rather than uncompressed sizes since that's what gets broadcast over the network. 

The discussion concluded with nero_eth suggesting to delay the variable deadline feature until after ePBS ships to observe market evolution, and potuz agreeing they need a protocol where compressed size is bounded to enable linear regression. An open question remains about establishing proper bounds for compressed block sizes and potentially standardizing on a specific Snappy compression variant.

**Participants:** nero_eth, potuz

<details>
<summary>Raw messages</summary>

```
[2026-03-23T13:54:34.472000+00:00] nero_eth: I can't be in the ePBS breakout as I'm at the BAL one but maybe someone can relay my thoughts:
* I'm against the variable payload deadline mainly because **anchoring on the uncompressed size will not yield good-enough results to actually make use of the little additional gas headroom we can squeeze out**: We'd need to assume the worst-case compression (uncompressed size == compressed size) for benchmarking, and since blocks usually compress very well, but worst-case we get no compression at all, we end up over-allocating propagation time for most blocks, collapsing the variable deadline back toward a fixed one and leaving little to no room to actually realize the ~6% headroom in practice.
* Another, independent "issue" is that we're dealing with a heavily right-skewed distribution when it comes to uncompressed block sizes. Today, worst-case is 20x larger than avg., which would lead to most blocks receiving very early deadlines, eating into the nice propagation time ePBS originally provided (and which might also be needed bc of BALs adding additional size to the payload).
* Talking with builders, the post-state monopoly argument doesn't hold - esp. since BALs will allow them to get to the post state even faster while validating the block async while already being able to build.
[2026-03-23T13:55:34.823000+00:00] nero_eth: If we still want to do it then:
* the boundaries for interpolation must be figured out (we should not use 3.6s as the lower border - that might be too tight). 
* we must study the block size distribution better: we're dealing with a heavily right-skewed distribution (both when looking at compressed as well as uncompressed). Worst-case of 10 MiB is 5x to much compared to what's realistic with today's gas limit and glamsterdam repricings. Better add the block gas limit to the formula and have the max be gas_limit / 64 (calldata cost per byte with 7976) - there are edge cases where 7702 auths could be used for even larger uncompressed blocks - not sure if this is overcomplicating things or if we want to be super precise here.
* agree with potuz that in general linear(!) interpolation is the right approach, but the lower and upper bound + assumption for worst-case size matters a lot.

My feeling is, since we can still to it later on (and it would be a simple change), let's delay it for now, see how the market evolves with ePBS being shipped, and reconsider it.
Also, we might want to reconsider agreeing on a specific version/variant of Snappy, which would help here.

For additional details, [I put the hackmd post I shared on last week's ACDC onto ethresearch](https://ethresear.ch/t/why-a-variable-payload-deadline-only-helps-by-6/24488)
[2026-03-23T13:56:31.653000+00:00] potuz: I don't think we'll discuss the issue of variable PTC deadline today? we're not close at all to even looking into that. But anyway we can also mention a link to this in the general part of the meeting.
[2026-03-23T13:57:17.441000+00:00] potuz: But I do want to know what do you mean by BAL helping here with the post-state, there's no way we'll have BAL in the bid right?
[2026-03-23T13:57:29.228000+00:00] potuz: BAL will have to be broadcast with the payload, not the block
[2026-03-23T14:08:32.784000+00:00] nero_eth: As soon as you get the ExecutionPayload, you only need a few ms to get to the post-state, no execution/validation needed to get there. Ofc, to know that the BAL is correct, you need to execute the block, but this can be done async.
[2026-03-23T14:09:36.871000+00:00] nero_eth: So, in the worst-case, instead of executing for 2-3s to arrive at the post-state, you would apply the BAL, start building the next block while validating the prev block + BAL async.
[2026-03-23T14:09:51.026000+00:00] potuz: still this then gives seconds of monopoly to the builder
[2026-03-23T14:10:07.154000+00:00] potuz: during those seconds that builder is the only one that can provide preconfs
[2026-03-23T14:10:11.015000+00:00] potuz: for example
[2026-03-23T14:10:37.960000+00:00] nero_eth: This doesn't matter as "competetive" building only starts right before the next slot. I think we might overindex on the usefulness of the post-state here.
[2026-03-23T14:11:36.496000+00:00] potuz: I think assuming that block building dynamic will remain the same as now is too optimistic
[2026-03-23T14:16:52.986000+00:00] nero_eth: Let's say you're right (this is the weakest argument I had against the variable deadline), the fact that we're anchoring on uncompressed vs compressed (which matters over the wire) removes any benefits as we'd have to assume worst-case compression.
[2026-03-23T15:47:50.555000+00:00] potuz: yes with this I agree, we need to do this over the compressed size for sure, that's what's being broadcast. So at the very least we need to have a protocol in which the compressed size is bound, even if it's not deterministic, we want to have it bounded so that we can do a linear regression over the bound
```

</details>

---

## 2026-03-30 (epbs)

barnabasbusa requested a decision on whether to proceed with the Variable PTC Deadline proposal, referencing a specific GitHub pull request (#4843 in the consensus-specs repository). They clarified that this decision request is separate from the epbs-devnet-1 implementation and concerns the general adoption of the feature.

No responses or further discussion were captured in these messages, leaving the decision request unresolved. The question of whether to implement Variable PTC Deadline remains open and awaiting input from other developers.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-03-30T20:18:35.722000+00:00] barnabasbusa: can we make a decision on whether we gonna do [Variable PTC Deadline](https://github.com/ethereum/consensus-specs/pull/4843) ?
[2026-03-30T20:19:24.968000+00:00] barnabasbusa: not talking about epbs-devnet-1, but in general.
```

</details>

