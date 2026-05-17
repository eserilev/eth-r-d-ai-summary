# ePBS Engine API finalized and safe block changes

---

## 2026-03-19 (epbs)

The discussion centers on how ePBS (enshrined Proposer-Builder Separation) affects the definition of finalized and safe block hashes in the consensus layer specifications. m.kalinin clarifies that while the Engine API itself doesn't need changes since the CL determines finalized and safe blocks, the actual definitions of these block hashes on the CL side must be updated. Currently, both finalized and safe block hashes are defined as the hash of the payload of the respective block, but ePBS changes this to the hash of the most recent payload in the chain of the safe or final block.

potuz agrees with this assessment and notes that this change is separate from a Prysm-specific bug where inexistent payloads were being sent in some cases. He suspects that clients may incorrectly be sending the child payload of the finalized block. As a result of the discussion, m.kalinin commits to opening a PR to update the CL specifications with the corresponding changes to reflect the new ePBS block hash definitions.

**Participants:** m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-03-19T06:01:21.421000+00:00] m.kalinin: Does it require a change in Engine API? It is up to CL to decide what is finalizedBlock and safeBlock. Engine API requires them to be consistent with the head block, i.e. to belong to the same chain which I think shouldn't be affected by ePBS.

What will be affected is the finalized and the safe block hash definitiones on the CL side as of now both are defined as the hash of the payload of a safe or a final block. While ePBS changes this to: the hash of the most recent payload in the chain of safe or final block.

Does this make sense?
[2026-03-19T10:24:36.132000+00:00] potuz: Yes that's what changes. I think this is independent of the Prysm bug that Terence mentioned above. Prysm was sending inexistent payloads in some cares which was worse. But I suspect clients may be sending the child Payload of the finalized block and that's wrong
[2026-03-19T11:23:42.246000+00:00] m.kalinin: Then I will open a PR to the CL specs with the corresponding change
```

</details>

