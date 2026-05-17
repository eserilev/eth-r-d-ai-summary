# Attestation thresholds and builder safety mechanisms

**Channel:** epbs | **Date:** 2026-05-16

## Summary

The discussion centers on determining safe attestation thresholds for builders to avoid same-slot unbundling attacks. Participants debated whether builders need >50%, 66%, or other percentages of attestations before safely publishing blocks. The conversation explored how attestation deadlines (proposed at 2 seconds) could provide builders with safe windows for publication, with the key insight that waiting until after the attestation deadline plus seeing some attestations would be safer than current practices.

The group converged on **40% attestations with no equivocations seen** as sufficient to guarantee safety against a 20% attacker, with potuz providing the mathematical backing (40% + 40% proposer boost vs maximum 60% attacker chain when honest chain has 80%). However, there was agreement that the specification should simply state "release if you see a block containing your bid" and leave specific threshold implementations to individual builders rather than mandating exact percentages.

An open question remains about builders seeking 100% certainty against same-slot unbundling, with nero_eth asking for clear guidance on whether "40% attestations + after attestation deadline" provides the desired safety guarantees. The discussion also touched on the distinction between same-slot unbundling versus next-slot reorgs, with some clarification needed on attack scenarios.

## Participants

- jtraglia
- nero_eth
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-16T05:29:09.828000+00:00] nero_eth: Why 40, or 66? To not be unbundled in the same slot you need more attestations than a competing equivocation. Wouldn't that mean >50% of votes? Or any majority of votes while >50% votes (independently if they voted for the proposers block or the parent)?
[2026-05-16T05:32:02.809000+00:00] nero_eth: If we put the attestation deadline to 2s, and attesters attest as early as they see the block, then, for the EL repricing, we can assume we have the full time from attestation deadline to the payload deadline for propagation. The earlier we allow builders to safely publish (safe as in "not risking same-slot unbundling") the better.
[2026-05-16T12:51:36.461000+00:00] jtraglia: Regarding 66, I might be confusing thresholds for forkchoice with finalization 😅
[2026-05-16T13:02:50.203000+00:00] nero_eth: Tbf, just saying 50% is simplifying things too and is neglecting that attesters could equivocate too.
I think 70%, similar to the floor of FCR, or 90% (with 20% adversary) might be the right number here (using `Q > 1/2(1+ boost/committee) + beta`). At this point, the block can be fast-confirmed and should be safe (at least in the happy path when we finalize). My feeling is, 90% is overly conservative here, but unsure what one would recommend to builders.
cc <@520034910149410861> <@425572898787426305>
[2026-05-16T13:30:08.448000+00:00] nero_eth: Thinking about it more, if the builder waits for "a few" attestations and publishes after the attestation deadline, it is already safer than today. E.g. if no equivocating block was seen at the attestation deadline, it won't get boost and since the builder didn't publish yet, there's nothing to be unbundled.
So, publish after seeing the commitment + after the attestation deadline seems fine?!
[2026-05-16T13:56:06.405000+00:00] terencechain: Worth noting low-carb-crusader attack can not happen here, so the only downside is it reveals too early, then gets reorg and future builders learn about its tx. Sensitive txs wold have bounded by its slot anyway
I think ideally it still wants  to reach `BUILDER_PAYMENT_THRESHOLD_NUMERATOR` so it gets paid
[2026-05-16T15:46:31.988000+00:00] potuz: 40% and no equivocations seen, that's enough to guarantee safety against a 20% attacker
[2026-05-16T15:48:31.801000+00:00] potuz: But anyway the spec should not really mention any of this IMO, the spec should say "release if you see a block containing your bid" and leave all these other aspects up to implementations
[2026-05-16T15:50:31.323000+00:00] nero_eth: Agree.
It is 40% and no equivocations **after** the atteststion deadline?
[2026-05-16T15:51:13.086000+00:00] potuz: It's no early equivocation
[2026-05-16T15:52:15.165000+00:00] nero_eth: And early is defined as before attestation deadline?
I assume there's no proposer boost after the atteststion deadline.
[2026-05-16T15:55:23.321000+00:00] nero_eth: I'm just trying to figure out what to tell to builders as this question has come up and they want to protect against same slot unbulding with 100% certainty (e.g. accepting attacks by >20% attackers). 
Is it 40% attestations + after the atteststion deadline - then it's safe to publish?
[2026-05-16T16:07:36.543000+00:00] terencechain: same slot unbunding cant happen right, i thought we are talking about next slot reorg
[2026-05-16T16:13:33.391000+00:00] terencechain: ~~is it 30%? where do i mess up on math?~~
~~i think you want Q + PB > 1 - Q...   => Q > 30%~~
~~Left side is the honest side.. right side is the attacker side~~
[2026-05-16T16:22:22.583000+00:00] terencechain: Nvm.. I see the math now..
Q = 40%
PB = 40%
attacker = 20%
honest chain = 80%
attacker chain most could have = 60%
20% is the different btw honest and attacker chain here
```

</details>
