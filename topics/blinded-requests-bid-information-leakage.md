# Blinded requests in bid and information leakage

---

## 2026-04-10 (epbs)

The discussion centers on whether execution requests should be included in full within builder bids or blinded, focusing on efficiency and information leakage concerns. The participants debate the tradeoffs of sending full requests in bids versus avoiding rebroadcasting, with tbenr noting potential data availability issues and potuz highlighting problems with invalid payloads when keeping them full in bids.

There are mixed views on information leakage risks. While nflaig references builder concerns about leaking payload information (similar to logs bloom issues mentioned in GitHub issue #120), bharath.vedartham argues that execution requests themselves don't contribute significantly to MEV leakage since the payload is the critical component. They note that currently under Fulu, full execution requests are sent with blinded payloads in BuilderBid, and builders haven't raised major objections to this approach.

The conversation concludes with potuz suggesting that not optimizing for pre-execution of requests could actually simplify caching mechanisms, particularly if the computational overhead of requests isn't substantial. The overall sentiment leans toward viewing this primarily as an efficiency consideration rather than a security issue, though the optimal approach remains an open question.

**Participants:** bharath.vedartham, nflaig, potuz, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-10T14:33:07.873000+00:00] tbenr: Just efficiency?
[2026-04-10T14:39:06.640000+00:00] potuz: you are thinking on keeping them full in the bid  and avoid rebroadcasting? I think this is ugly in the event of invalid payloads but also yes, sending them full in the bid is a problem.
[2026-04-10T14:40:55.269000+00:00] tbenr: Ye i was thinking that. It has free da issues at least
[2026-04-10T14:42:37.824000+00:00] nflaig: aren't there even complains from builders that having them in the bid leaks information about the payload?
[2026-04-10T14:44:22.134000+00:00] tbenr: i'm not an expert but the requests from EL to CL don't have much to leak
[2026-04-10T14:45:24.520000+00:00] nflaig: ah it was noted here https://github.com/ethereum/builder-specs/issues/120
> There also has been concerns (eg. #108) about leaking block information via the logs bloom.
but I am not sure if this means same as logs blooms or similar
[2026-04-10T14:49:10.542000+00:00] bharath.vedartham: today as per fulu, we are sending the full execution requests along with the blinded payload in the BuilderBid as per here
[2026-04-10T14:49:11.122000+00:00] bharath.vedartham: https://github.com/ethereum/builder-specs/blob/main/specs/electra/builder.md?plain=1#L36
[2026-04-10T14:49:47.112000+00:00] bharath.vedartham: In terms of MEV leaking, I believe the payload is the critical part and not the requests. otherwise I m pretty confident builders would have raised issues against the execution requests being fully in the bid
[2026-04-10T14:50:11.298000+00:00] tbenr: seems like an efficiency argument, mostly
[2026-04-10T14:50:59.640000+00:00] nflaig: that is just because we need them for stf, not because we want to include them per se
[2026-04-10T14:51:29.783000+00:00] nflaig: so gloas removes a lot of potential information leakage
[2026-04-10T14:51:57.597000+00:00] bharath.vedartham: yeah by just having the block hash in the bid, in terms of leakage i think its better than what we have today in the `BuilderBid` which is the full execution payload header
[2026-04-10T14:52:16.342000+00:00] bharath.vedartham: fair my point is that, i dont think having the execution requests in the bid should contribute to any info leak in the bid
[2026-04-10T14:56:19.888000+00:00] potuz: So if we don't optimize to preexecute  the requests, our cache actually becomes simpler. If the requests aren't that much of computation this actually simplifies things for us as well
```

</details>

