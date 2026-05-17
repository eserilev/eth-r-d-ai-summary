# Payload Equivocation Issues and Fork Choice

---

## 2026-03-27 (epbs)

Potuz reported that Subhasish discovered an issue related to payload equivocations not being properly tracked in the current fork choice implementation. The core problem is that attestations with `index=1` should not be rejected when a payload is invalid, since there may be an equivocation with a valid payload. Potuz suggests that a proper fix would involve adding an `envelope_root` to the signed envelope, where attesters would attest to this envelope root instead of the beacon block root as head, which would also resolve branches on equivocations.

Potuz noted that there are likely many edge cases when dealing with payload equivocations, and that designing a non-DoS-able pending payload queue is challenging because of these issues. He mentioned this should be considered for the next iteration of fork choice. The discussion references a PR written by someone else and indicates that Subhasish has found additional related issues beyond the initial discovery.

**Participants:** potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-27T11:18:18.286000+00:00] potuz: <@908359967038382121> has found another issue that stems from the fact that we do not track payload equivocations. Late to fix this properly but I suspect that we should have added an  envelope_root to the signed envelope and just keep things referencing that HTR. In particular, instead of setting index=1, attesters would attest to that envelope root instead of the beacon block root as head. It would also resolve branches on equivocations. Something to keep in mind for the next iteration of forkchoice.
[2026-03-27T11:26:48.775000+00:00] potuz: FWIW, the issue is that we should not `REJECT` attestations with `index=1` when the payload is invalid. There may be an equivocation with a valid payload. I'm pretty certain that there are a lot of edge cases when dealing with payload equivocations. Designing the pending payload queue in a non-dossable manner sucks because of this. cc <@363800010518822915> who wrote the PR.
[2026-03-27T14:05:00.199000+00:00] terencechain: @Subhasish has found another issue
```

</details>

---

## 2026-05-05 (epbs)

jtraglia raised a concern about payload attestation equivocation, where a malicious proposer could potentially include two `PayloadAttestation` aggregates in the same block with the same validator voting differently in both. While gossip rules prevent accepting multiple payload attestation messages from the same validator, there's currently nothing stopping proposers from including conflicting votes in a block since `process_payload_attestation` checks them independently.

potuz argued this isn't a significant problem, noting that equivocations can already create split views regardless of proposer actions, and since there's no slashing mechanism for PTC members, the behavior can't be punished anyway. He expressed reluctance to add new block invalidation conditions unless absolutely necessary, preferring that the last attestation in processing order simply takes precedence. jtraglia countered that allowing conflicting votes could cause implementation-dependent split views and represents buggy behavior that should invalidate the block, suggesting the proposer (not the PTC member) should be punished for including such conflicts.

The discussion concluded with jtraglia creating PR #5209 to implement a check preventing this behavior, while acknowledging he's willing to close it if others disagree. The participants agreed to seek additional opinions from the broader research community, leaving the issue unresolved pending further input.

**Participants:** jtraglia, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-05T17:36:10.433000+00:00] jtraglia: Payload attestation question. Currently, it appears that there's nothing which would prevent a malicious proposer from including two `PayloadAttestation` aggregates in the same block where the same validator's bit in set in both. Might be difficult to pull off, but theoretically possible. `process_payload_attestation` wouldn't prevent this because it checks them independently. Is this something we should prevent?
[2026-05-05T17:37:17.191000+00:00] jtraglia: Or should we just ignore 2nd votes...
[2026-05-05T17:38:15.872000+00:00] jtraglia: One could argue the validator should be slashed too. But I don't think that's currently possible.
[2026-05-05T17:39:01.456000+00:00] jtraglia: cc <@363800010518822915> <@755590043632140352>
[2026-05-05T18:00:44.527000+00:00] potuz: We thought about this and didn't think this was a problem to be worried about
[2026-05-05T18:01:39.632000+00:00] potuz: Francesco had some rules to consider only the last PTC attestation in the case of messages over the gossip
[2026-05-05T18:01:52.443000+00:00] potuz: but equivocations on the same block seem to be irrelevant
[2026-05-05T18:02:11.371000+00:00] jtraglia: In gossip, only the first payload attestation message from each validator is accepted.
[2026-05-05T18:02:30.271000+00:00] jtraglia: Why would you say irrelevant? To me it indicates buggy or malicious behavior.
[2026-05-05T18:02:43.525000+00:00] potuz: yes, but if the proposer includes a conflicting one we should take the equivocation
[2026-05-05T18:02:50.731000+00:00] potuz: I don't think this is important though
[2026-05-05T18:03:09.199000+00:00] jtraglia: What does "take the equivocation" mean here?
[2026-05-05T18:03:50.129000+00:00] potuz: yes, but explain me the attack, if you have a malicious PTC you can achieve anyway the same line of attacks, since we don't have slashings, then it's impossible to punish this behavior
[2026-05-05T18:04:26.512000+00:00] potuz: if the proposer includes a PTC attestation from a PTC member that differs with the one I have already processed and it's in my forkchoice, I should consider the proposer's one instead of mine
[2026-05-05T18:06:27.119000+00:00] jtraglia: I agree that it's impossible to punish the PTC member. But we could punish the proposer by considering their block to be invalid. The only way this could show up in a block is if a proposer includes it.
[2026-05-05T18:07:58.539000+00:00] jtraglia: We could add a fairly basic check for this in `process_operations`. Would you be open to seeing what that looks like? cc <@755590043632140352> (I should have responded to your message so it pings you).
[2026-05-05T18:19:09.948000+00:00] jtraglia: Yes, and I agree that's fine. Sort of weird but fine. What I'm saying is that the proposer shouldn't include two payload attestations from the same validator index with differing votes.
[2026-05-05T18:31:32.633000+00:00] potuz: I don't see why an equivocation in the block would be relevant in an  way, the last one is the one that remains, and it can't cause a split view cause they are processed in order.
[2026-05-05T18:32:13.919000+00:00] potuz: In general, conditions that invalidate a block are a bug to happen, I'd rather not add them unless we really should treat the block as invalid.
[2026-05-05T18:35:04.681000+00:00] jtraglia: I'd argue that allowing conflicting votes are a bug waiting to happen 😅  depending on how `on_payload_attestation_message` in clients are implemented. Couldn't this cause a split view depending on which vote you treat as valid?
[2026-05-05T18:35:25.920000+00:00] jtraglia: I think we need some outside opinions. cc <@&1417820113981145228>
[2026-05-05T18:48:08.526000+00:00] potuz: that split view is already there regardless of the proposer action if there's an equivocation, if the proposer does not include any PTC attestation you can have that split view
[2026-05-05T18:48:19.486000+00:00] potuz: on equivocations
[2026-05-05T19:20:19.975000+00:00] jtraglia: Made the following PR. I'm okay with closing it if others disagree with it.
https://github.com/ethereum/consensus-specs/pull/5209
```

</details>

