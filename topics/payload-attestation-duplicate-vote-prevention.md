# Payload Attestation Duplicate Vote Prevention

---

## 2026-05-05 (epbs)

jtraglia raised a concern about payload attestation duplicate vote prevention, noting that malicious proposers could theoretically include two `PayloadAttestation` aggregates in the same block with the same validator's bit set in both, since `process_payload_attestation` checks them independently. He suggested either ignoring second votes or considering such blocks invalid to prevent buggy or malicious behavior.

Potuz disagreed with treating this as a significant issue, explaining that equivocations in blocks are irrelevant since they're processed in order (with the last one remaining) and can't cause split views. He argued that without slashing mechanisms for PTC members, it's impossible to punish the underlying behavior, and that adding block invalidation conditions should be avoided unless truly necessary. Potuz noted that split views from equivocations already exist regardless of proposer actions.

The discussion concluded with jtraglia creating PR #5209 to address the issue despite the disagreement, while acknowledging he's willing to close it if others disagree. The debate centers on whether preventing duplicate votes at the block level is worth the complexity, given that the underlying equivocation problem exists independently of proposer behavior.

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

