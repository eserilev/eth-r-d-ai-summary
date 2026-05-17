# PTC votes equivocation and slashing

**Channel:** epbs | **Date:** 2025-12-19

## Summary

m.kalinin raised concerns about PTC (Payload Timeliness Committee) vote equivocation, noting that while such equivocations are theoretically slashable, there's currently no mechanism in the specification to process these equivocations in either the Fork Choice (FC) or State Transition (ST) functions. They questioned whether there are plans to implement equivocation processing and whether PTC member equivocation should be made slashable.

potuz expressed skepticism about adding new slashing conditions, arguing they should only be implemented when necessary for core chain security properties. They noted that equivocating PTC votes wouldn't provide significant advantages to malicious actors since it would only enable short-term griefing of the next proposer/attester without allowing transaction replay or MEV theft, making it unlikely that clients would fork specifically to enable such behavior.

The discussion leaves open the question of whether to implement PTC equivocation slashing mechanisms, with differing views on the necessity and security implications of such additions to the protocol.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-19T03:46:22.567000+00:00] m.kalinin: My another question is about PTC votes equivocation. They are slashable but we don't have a mechanism in the spec to process those equivocations neither in the FC nor in the ST. are there plans to work on equivocation processing?
[2025-12-19T12:52:45.426000+00:00] m.kalinin: What if PTC member equivocates? Should we attempt to make it slashable?
[2025-12-19T16:33:13.771000+00:00] potuz: I don't like new slashing conditions unless it's necessary for the core security properties of the chain. It doesn't make sense to fork clients to equivocate since you can't gain more than a short term grieving of the next proposer/attester. You can't even  replay transactions nor steal MEV
```

</details>
