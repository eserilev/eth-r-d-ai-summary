# Gloas Finalized State and Spec Changes

**Channel:** epbs | **Date:** 2026-04-06

## Summary

The discussion centers on proposed changes to handle "Gloas finalized state" in Ethereum's consensus specification, with potuz seeking feedback on API aspects and implementation approaches. The main technical debate revolves around how to account for payload status in checkpoint states, with two primary options being considered: modifying the existing `store.checkpoint_states` to include payload information, or updating the `Checkpoint` data structure itself to include payload status.

Potuz argues against updating checkpoints directly, viewing it as an unnecessarily large change for what he considers an edge case, noting that client implementations already handle the required logic. He advocates for either modifying the checkpoint states storage or removing the `store_checkpoint_states` machinery entirely from the spec, arguing it only exists for Python implementation convenience rather than specification correctness. Wemeetagain counters that the change should be evaluated from a spec correctness perspective regardless of implementation complexity, while terencechain raises concerns about potential impacts on attestation structures.

The discussion reveals tension between maintaining spec correctness versus minimizing implementation changes, with no clear consensus reached. Key action items include adding tests for edge case scenarios and deciding on the fork choice implementation approach, with potuz indicating he can open a PR despite considering all options "rather ugly."

## Participants

- potuz
- terencechain
- wemeetagain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-06T15:48:57.965000+00:00] potuz: Ok, sorry for the massive ping 
https://www.potuz.net/posts/gloas-finalized/

Need some opinion on the API aspects, cc <@586161934425128960> <@358120958726373381> <@602753420033785856>  you guys had strong opinions on this before. 

Also we need to add the test mentioned in the last section <@592004585506340885> since I am pretty certain that we should be split/broken in this scenario. 

Finally cc <@520034910149410861> cause you're kind of the owner here, I can open a PR on forkchoice but every option is rather ugly. The simplest would be to modify `store.checkpoint_states` to take a pair of a checkpoint and a boolean for the payload.
[2026-04-06T16:30:28.042000+00:00] potuz: In the spec the change to account for these new target states is rather brutal, I think it's easier to just use the poststate of the blockroot that is being attested to verify the attestations, client implementers will use the target state anyway
[2026-04-06T16:31:24.931000+00:00] potuz: The spec would be much slower, but designing the spec because of the python implementation is rather stupid I think
[2026-04-06T16:31:53.681000+00:00] potuz: the whole `store_checkpoint_states` should not exist at all, it leaks into implementations
[2026-04-06T16:34:24.155000+00:00] potuz: but even removing this unnecessary dictionary is a big change in the spec 🙁
[2026-04-06T17:58:13.556000+00:00] wemeetagain: has anyone considered just updating `Checkpoint` to include payload status? Then we can keep the current checkpointing semantics and not try to patch at an implementation layer or accept degraded guarantees.
[2026-04-06T18:00:10.693000+00:00] wemeetagain: There's a lot of work in the "just". XD
[2026-04-06T18:11:27.222000+00:00] potuz: it's in the writeup above
[2026-04-06T18:12:18.310000+00:00] potuz: I immediately dismissed this by looking at how many times we use `Checkpoints` in Prysm, and it does sound like a very large change to be making over this edge case here
[2026-04-06T18:13:01.027000+00:00] potuz: the changes to deal with this in the spec may look large and ugly, but on code are zero: we just need a state that is compatible with the seed in the same epoch as the attestation: every client already does this
[2026-04-06T18:13:32.338000+00:00] potuz: so I am very skeptical if turning this into a very large change when it should be no work at all for clients
[2026-04-06T18:19:25.585000+00:00] terencechain: i think we'll also have to update the attestation as well which can get very ugly
[2026-04-06T18:37:21.335000+00:00] wemeetagain: Oh yeah.
> To preserve it directly on-chain, the spec would need to extend checkpoints themselves,
> 
Yeah IMO it should be considered from spec correctness perspective, independent of implementation pain
[2026-04-06T18:45:34.720000+00:00] potuz: attestations don't really need much of a change
[2026-04-06T18:46:58.869000+00:00] potuz: anyway, I think changing the checkpoints is crazy and utterly unnecessary
[2026-04-06T18:47:41.952000+00:00] potuz: I would rather remove entirely this machinery of the checkpoint states from the specs store, there's no real need for them to be there, they are there **only** to make the python state to be manageable, not from a specification perspective
[2026-04-06T18:47:58.505000+00:00] potuz: we could use get_ancestor everywhere in the spec and it would be formally correct
[2026-04-06T18:53:06.219000+00:00] terencechain: i see, you would just have attestation that point to old version of checkpoint.. and dont need to have a new `AttestationDataGloas`
[2026-04-06T18:55:32.809000+00:00] potuz: in principle we could remove the target checkpoint altogether from attestations at the cost of not being able to include votes from other branches, but even including the current checkpoint as-is, without any changes, we can still do the FFG counting on-chain to justify a new kind of checkpoint where it includes the payload information. I think this is just adding complexity just for intellectual masturbation
```

</details>
