# ePBS Engine API and payload hash definitions

**Channel:** epbs | **Date:** 2026-03-19

## Summary

The discussion focuses on how ePBS (enshrined Proposer-Builder Separation) affects payload hash definitions in the Engine API and consensus layer specifications. m.kalinin clarifies that while the Engine API itself doesn't require changes (since the CL decides finalized and safe blocks), the definitions of finalized and safe block hashes on the consensus layer side do need updating. Under ePBS, these hashes will be defined as "the hash of the most recent payload in the chain of safe or final block" rather than simply "the hash of the payload of a safe or final block."

potuz confirms this understanding and notes that the issue is separate from a Prysm bug where non-existent payloads were being sent. He suspects that some clients may incorrectly be sending the child payload of the finalized block. As a result of the discussion, m.kalinin commits to opening a PR to update the consensus layer specifications with the corresponding changes to address these definition updates for ePBS.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-19T06:01:21.421000+00:00] m.kalinin: Does it require a change in Engine API? It is up to CL to decide what is finalizedBlock and safeBlock. Engine API requires them to be consistent with the head block, i.e. to belong to the same chain which I think shouldn't be affected by ePBS.

What will be affected is the finalized and the safe block hash definitiones on the CL side as of now both are defined as the hash of the payload of a safe or a final block. While ePBS changes this to: the hash of the most recent payload in the chain of safe or final block.

Does this make sense?
[2026-03-19T10:24:36.132000+00:00] potuz: Yes that's what changes. I think this is independent of the Prysm bug that Terence mentioned above. Prysm was sending inexistent payloads in some cares which was worse. But I suspect clients may be sending the child Payload of the finalized block and that's wrong
[2026-03-19T11:23:42.246000+00:00] m.kalinin: Then I will open a PR to the CL specs with the corresponding change
```

</details>
