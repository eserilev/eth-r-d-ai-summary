# PTC Lookbehind and Payload Attestations Discussion

**Channel:** epbs | **Date:** 2026-03-17

## Summary

The discussion centers on PTC (Payload Timeliness Committee) lookbehind issues and whether payload attestations should remain in blocks. Potuz identified this as the primary topic for the next ACDT meeting and requested that participants bring informed opinions. Ralexstokes explored the possibility of removing payload attestations from blocks entirely, suggesting they could rely on fork choice documentation and cached committee data instead, potentially reducing spec complexity.

Potuz argued that PTCs in blocks serve important functions, particularly in scenarios involving PTC equivocations where proposers can enforce their view on payload reorg decisions, helping prevent ex-ante reorgs. He noted this also applies during split view situations. Ralexstokes questioned whether this was redundant given that proposer intent is already indicated by their chosen parent block, and pointed out that attackers could simply withhold attestations if they wanted to deviate.

The conversation concluded with Potuz clarifying two scenarios: honest proposers who see available payloads but face PTC votes claiming unavailability (creating honest reorg situations similar to late CL blocks), and more realistic cases where proposers lacking payloads want to ensure attesters follow their forced reorg path. The technical tradeoffs between spec complexity and these edge case protections remain unresolved.

## Participants

- potuz
- ralexstokes

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-17T20:25:18.685000+00:00] potuz: For next ACDT there's only one topic I can think of and it's the PTC lookbehind thing. Please bring someone with an opinion. The issue above of blockroots should be discussed in ACD or the joint section of ACDT then
[2026-03-17T21:04:11.621000+00:00] ralexstokes: i was just looking at this earlier today -- it may be a bit heretical but im still trying to see if there are any attack vectors if we just remove the payload attestations from the blocks entirely
[2026-03-17T21:04:31.484000+00:00] ralexstokes: then we can just say e.g. in the fork choice doc to use the right state when validating them
[2026-03-17T21:04:40.847000+00:00] ralexstokes: AFAIK every client will keep the committees cached anyway
[2026-03-17T21:05:40.728000+00:00] ralexstokes: so it seems like adding the PTCs to the state is more of a harmony/aesthetics thing in the spec and then  at that point i'd ask if we can cut some complexity
[2026-03-17T21:13:00.429000+00:00] potuz: - The PTC in the block helps in a few scenarios. One of them that I like is this:  if there have been equivocations by the PTC, the proposer enforces his own view on why he decided to reorg a payload or not. So it helps prevent some ex-ante reorgs in this way
[2026-03-17T21:13:54.699000+00:00] potuz: Something similar happens on split views
[2026-03-17T21:26:37.996000+00:00] ralexstokes: right and that's likely the justification, although going thru the fork choice to see if that's actually possible right now
[2026-03-17T21:27:19.661000+00:00] ralexstokes: hm although in the proposer case don't we already know via the parent they build on top of?
[2026-03-17T21:27:32.228000+00:00] ralexstokes: so then it seems a bit redundant
[2026-03-17T21:27:46.916000+00:00] ralexstokes: and if an attacker wanted to deviate from what would be on chain they can just withhold them from the block
[2026-03-17T22:52:16.962000+00:00] potuz: The proposer path is tricky, cause it's not really honest behavior if they "decide" to reorg the payload. That is, if the PTC voted the payload is not available but the proposer did see it on time, the proposer's forkchoice will pick the full state as head and it *should* build on full. However in this case the proposer could reorg and forkchoice of all attesters will follow it's choice. This is very much like an *honest reorg* today for the late CL block path. 

The more reallistic scenario however is that the proposer may not have the payload and wants to make sure that attesters follow it's forced path on reorging.
```

</details>
