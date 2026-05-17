# Checkpoint Structure Updates Discussion

---

## 2026-04-06 (epbs)

The discussion centers on whether to update Ethereum's `Checkpoint` structure to include payload status information to address an edge case, rather than implementing workarounds at the implementation layer. While wemeetagain suggests this approach for spec correctness, potuz strongly opposes it, arguing that it would require extensive changes across codebases (noting how frequently checkpoints are used in Prysm) for what should be a zero-work solution since clients already maintain compatible states with the required seed in the same epoch.

Potuz advocates for a simpler approach, suggesting that the current specification could use `get_ancestor` everywhere and that checkpoint states in the spec store exist primarily for Python implementation manageability rather than specification necessity. He argues that FFG counting can still be performed on-chain to justify new checkpoint types without modifying the existing checkpoint structure. Terencechain raises concerns about potential attestation updates becoming "very ugly," though potuz later clarifies that attestations wouldn't need significant changes.

The discussion reveals a tension between spec correctness and implementation complexity, with potuz characterizing the checkpoint structure changes as "crazy and utterly unnecessary" complexity added for "intellectual masturbation." No clear consensus is reached, leaving the question of whether to pursue checkpoint structure updates or alternative approaches unresolved.

**Participants:** potuz, terencechain, wemeetagain

<details>
<summary>Raw messages</summary>

```
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

