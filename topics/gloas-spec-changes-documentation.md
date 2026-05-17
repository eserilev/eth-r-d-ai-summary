# Spec Changes and Documentation for Gloas

---

## 2026-04-06 (epbs)

potuz committed to writing a comprehensive document on Gloas specification changes and shared it at https://www.potuz.net/posts/gloas-finalized/, seeking feedback particularly on API aspects from team members who had previously expressed strong opinions. The document identifies a need for additional testing in a specific scenario where the system should "split/broken," and proposes modifying `store.checkpoint_states` to accept a pair of checkpoint and boolean for payload handling, though potuz acknowledges all implementation options are "rather ugly."

The discussion reveals tension between spec design and implementation practicalities. potuz suggests using the poststate of the attested blockroot for attestation verification rather than making "brutal" changes to accommodate new target states in the specification, arguing that optimizing the spec for the Python implementation is misguided since client implementers will use target states anyway. A broader concern emerges about `store_checkpoint_states` being an implementation leak that shouldn't exist in the spec, but removing this "unnecessary dictionary" would require significant specification changes.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-06T11:32:40.606000+00:00] potuz: I think we all understand the nuances, but setting it up in the spec is a little awkward, probably better to have a full writeup instead of dicussing here in the air. I'll write a full document on this issue, and have it ready in an hour or so
[2026-04-06T15:48:57.965000+00:00] potuz: Ok, sorry for the massive ping 
https://www.potuz.net/posts/gloas-finalized/

Need some opinion on the API aspects, cc <@586161934425128960> <@358120958726373381> <@602753420033785856>  you guys had strong opinions on this before. 

Also we need to add the test mentioned in the last section <@592004585506340885> since I am pretty certain that we should be split/broken in this scenario. 

Finally cc <@520034910149410861> cause you're kind of the owner here, I can open a PR on forkchoice but every option is rather ugly. The simplest would be to modify `store.checkpoint_states` to take a pair of a checkpoint and a boolean for the payload.
[2026-04-06T16:30:28.042000+00:00] potuz: In the spec the change to account for these new target states is rather brutal, I think it's easier to just use the poststate of the blockroot that is being attested to verify the attestations, client implementers will use the target state anyway
[2026-04-06T16:31:24.931000+00:00] potuz: The spec would be much slower, but designing the spec because of the python implementation is rather stupid I think
[2026-04-06T16:31:53.681000+00:00] potuz: the whole `store_checkpoint_states` should not exist at all, it leaks into implementations
[2026-04-06T16:34:24.155000+00:00] potuz: but even removing this unnecessary dictionary is a big change in the spec 🙁
```

</details>

---

## 2026-04-10 (epbs)

nflaig identified a compatibility issue in the fork choice specification where the code assumes all blocks have a `signed_execution_payload_bid.message` field, but this breaks when the parent block is from before the Gloas fork since pre-Gloas blocks don't have this field. They noted this appears to be the first time such fork compatibility has been relevant in the fork choice logic.

potuz confirmed this is a known issue that client implementations likely handle with special casing. However, they pointed out that the specification itself is fork-specific, and adding fork awareness to the helper functions would make the code ugly. The discussion leaves open how to properly address this specification gap between maintaining clean code and ensuring compatibility across fork boundaries.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-10T10:45:44.927000+00:00] nflaig: in the fork choice spec we have
```python
 parent_block = store.blocks[block.parent_root]
 bid = block.body.signed_execution_payload_bid.message
```
this breaks if parent is pre-gloas, does fork choice just not care about this? it seems like gloas is the first time this is relevant
[2026-04-10T12:25:58.711000+00:00] potuz: yeah this was noticed before and I assume all clients case this, the problem is that the spec is fork-specific, including fork awareness in the helpers is ugly.
```

</details>

---

## 2026-05-09 (epbs)

m.kalinin identified two modifications in the Gloas fork that differ from Phase 0: both `is_parent_strong` and `is_head_weak` functions have been switched to use `get_attestation_score` instead of `get_weight`, and `is_head_weak` now includes equivocation counting from the head slot committee. Since these changes don't appear to be ePBS-related, they questioned why they weren't applied to Phase 0.

potuz explained that these changes weren't backported because implementing equivocation counting is non-trivial and would cause all clients to fail fork choice tests and spectests. This suggests the changes require significant implementation work that would break existing test compatibility in Phase 0.

**Participants:** m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-09T06:20:28.392000+00:00] m.kalinin: I am looking at `is_parent_strong` and `is_head_weak` mods in Gloas:
1) Both functions are switched to `get_attestation_score` as opposed to `get_weight` in Phase 0
2) `is_head_weak` now counts in equivocations from the head slot committee

I don't find this changes ePBS related. Q: why aren't they applied to Phase 0? cc <@755590043632140352> <@602753420033785856>
[2026-05-09T10:14:41.325000+00:00] potuz: I think the reason these changes weren't back ported is that no one would pass FC tests. The equivocation counting is not trivial to implement and we wouldn't pass spectests at all.
```

</details>

