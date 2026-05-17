# PTC votes for devnet implementation

**Channel:** epbs | **Date:** 2026-03-25

## Summary

The discussion centers on implementing PTC (Peer-to-Peer Transaction Channel) votes for devnet 1. Terence from the Prysm team reported that they have already been generating PTC votes for devnet 0, but the proposer is not including them in blocks (this functionality was commented out in the code).

The main technical decision point is whether to add the PTC window to the state, which would be a consensus-breaking change that all clients need to agree upon. Potuz confirmed that adding the PTC window is the only additional change planned for devnet 1, with all other modifications being non-hardfork changes. The team needs to reach consensus on the state implementation approach before proceeding with devnet 1.

## Participants

- 0xunclebill
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-25T02:37:29.958000+00:00] 0xunclebill: Are we doing PTC votes for devnet 1?
[2026-03-25T03:05:39.281000+00:00] terencechain: prysm has been doing PTC votes for devnet 0 except proposer not packing them (commented it out)
we need to agree on whether to add the ptc window in state or not because that's consensus breaking
[2026-03-25T13:36:57.813000+00:00] potuz: yes, PTC window was the only extra change we want in devnet 1, the rest are non-hardforks
```

</details>
