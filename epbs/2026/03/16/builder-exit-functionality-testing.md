# Builder Exit P2P Specification Updates

**Channel:** epbs | **Date:** 2026-03-16

## Summary

The discussion centers on whether the P2P specification needs updates to handle builder exits, which have high validator indices. Terencechain raised concerns about P2P compatibility when builders exit through the peer-to-peer network.

Nflaig clarified that the existing `process_voluntary_exit` function should be sufficient since the P2P spec relies on it for validation, with reject conditions based on `process_voluntary_exit` passing validation. However, they noted potential ambiguity in the IGNORE condition which specifically mentions "validators" rather than the broader term that would include builders.

The participants agreed that the P2P specification document should explicitly mention this change to avoid implementers missing the update, noting that similar documentation updates were also missed for the Electra upgrade. This represents an action item to update the P2P interface documentation to clarify builder exit handling.

## Participants

- nflaig
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-16T15:42:25.945000+00:00] terencechain: builder can exit through p2p right? in that case did we update p2p spec for exit coming from builder (high index) otherwise it wont work
[2026-03-16T15:46:31.750000+00:00] nflaig: `process_voluntary_exit` is updated which should be sufficient as p2p spec just states
```
_[REJECT]_ All of the conditions within process_voluntary_exit pass
  validation.
```
but I guess worth noting the other conditions
```
_[IGNORE]_ The voluntary exit is the first valid voluntary exit received for
  the validator with index signed_voluntary_exit.message.validator_index.
```
which only talks about validators
[2026-03-16T15:47:35.138000+00:00] terencechain: ah thanks..  but i think in https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/p2p-interface.md we need to mention there's a change
[2026-03-16T15:48:03.100000+00:00] terencechain: or else it's easy to miss
[2026-03-16T15:49:39.204000+00:00] nflaig: looks like we didn't do that for electra either but makes sense
```

</details>
