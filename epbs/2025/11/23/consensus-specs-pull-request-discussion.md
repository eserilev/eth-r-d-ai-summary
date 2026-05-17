# Devnet-0 consensus spec version targeting discussion

**Channel:** epbs | **Date:** 2025-11-23

## Summary

The discussion centers around which consensus specification version to target for devnet-0. blockkshane asked whether the team is still targeting `v1.6.0-beta.1` or if client teams are building to the current spec with the release version remaining flexible.

terencechain suggested moving to `v1.6.1` as the target, citing the potential need to add `execution_payment` in bid (referencing PR #4733), though expressed no strong preference and willingness to align with the team. potuz agreed with either approach, noting that the change should be straightforward to implement across all clients.

The discussion leaves the final version target as an open question, with general agreement that either `v1.6.0-beta.1` or `v1.6.1` would be acceptable for devnet-0, pending team alignment on the decision.

## Participants

- blockkshane
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-11-23T13:26:00.805000+00:00] blockkshane: hi, are we still targeting consensus spec `v1.6.0-beta.1` for `devnet-0`? or are client teams just building to the current spec and the release version for `devnet-0` is still a moving target?
[2025-11-23T19:51:35.118000+00:00] terencechain: we might want to add `execution_payment` in bid so I think v1.6.1 is a good target. I dont have a strong opinion, happy to align with anyone here
https://github.com/ethereum/consensus-specs/pull/4733
[2025-11-23T22:01:27.077000+00:00] potuz: I don't mind either way. It should be a simple change for all clients.
```

</details>
