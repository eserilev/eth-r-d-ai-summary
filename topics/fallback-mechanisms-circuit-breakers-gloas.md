# Fallback mechanisms and circuit breakers in Gloas

---

## 2026-05-13 (epbs)

The discussion focused on fallback mechanisms and circuit breakers for Gloas, particularly how to handle builder failures. M.kalinin asked whether the current circuit breaker that switches to local block building would persist post-Gloas. Bharath explained that anonymous P2P builders could cause liveness failures by not revealing payloads, with no slashing mechanism available beyond forfeiting their payment to the proposer.

Potuz advocated for more granular fallback mechanisms rather than immediately switching to local building, suggesting that builders should be banned individually when they cause issues. He noted that most circuit breaker activations have been due to specific builder bugs rather than consensus layer problems, which could have been resolved by banning single builders instead of switching modes entirely. The team has discussed complex fallback strategies that would revert to different builder types (trusted builders only, P2P bids only, or local building) depending on the situation.

A key challenge identified is distinguishing between trusted builders and P2P builders when examining bids, which complicates the implementation of targeted fallback mechanisms. While P2P builders can potentially sybil attack by creating new public keys when banned, the consensus seems to favor implementing granular builder blacklisting as the first line of defense, with detailed fallback mechanisms documented in interop notes.

**Participants:** bharath.vedartham, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-13T17:17:58.156000+00:00] m.kalinin: Do I understsand correctly that the curcuit breaker that switches to local block building that we have today will exist post-Gloas?
[2026-05-13T17:47:57.269000+00:00] bharath.vedartham: We had some discussions on this in interop. Anonymous builders on p2p could choose to not reveal payloads and cause some liveness failure and we have no way to slash today except the fact that their payment will go to the proposer. 
There were ideas to fallback to trusted builders from trustless and then to self-building if that goes bad too. But I assume the simplest thing to do with gloas is to fallback to local block building if there are a certain number of missing payloads in a row
[2026-05-13T17:50:44.313000+00:00] m.kalinin: Yeah, i think the simplest way would be to preserve the same behavior as it is today, i.e. switch straight to local building
[2026-05-13T17:51:24.006000+00:00] potuz: we want to avoid this, we can circuit break to ban particular builders now
[2026-05-13T17:51:48.140000+00:00] potuz: eg every single time we hit the circuit breaker for builder bugs instead of CL bugs, it would have been a single block
[2026-05-13T17:53:30.502000+00:00] bharath.vedartham: P2P builders can sybil if you ban a particular builder pubkey, this is much easier with trusted builders where you could just disconnect from this.
[2026-05-13T17:53:32.076000+00:00] m.kalinin: i see, you just ban a builder and when there are no bids you can use you start local building?
[2026-05-13T17:55:37.373000+00:00] potuz: this is not about P2P builders though, we've had detailed discussions in interop about the fallback mechanism, I'm sure the notes are up in the website
[2026-05-13T17:55:57.054000+00:00] potuz: there are different fallback mechanisms
[2026-05-13T17:56:32.655000+00:00] potuz: it's a bit complex, but we want to revert to **only** trusted builders some times, and to **only** local building some others, or **only** p2p bids on others
[2026-05-13T17:56:48.990000+00:00] potuz: but the first line of defense is blacklisting a single builder
[2026-05-13T17:57:59.990000+00:00] bharath.vedartham: https://hackmd.io/@nixorokish/builder-api-summary#5-Circuit-Breakers here is the summary
[2026-05-13T18:01:15.723000+00:00] bharath.vedartham: yeah i think the main issue we found was that, it is hard to tell if a bid came from trusted builder or from p2p just by looking at the bid
```

</details>

