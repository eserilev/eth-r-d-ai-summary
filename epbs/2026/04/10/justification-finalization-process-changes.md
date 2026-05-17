# Justification and Finalization Process Changes

**Channel:** epbs | **Date:** 2026-04-10

## Summary

Potuz initially proposed a minimal change to the justification and finalization process that would allow keeping the existing consensus mechanism without modifying the checkpoint structure. This approach would enable justifying full blocks and achieve finalization boundary alignment, which he planned to demonstrate through a pull request.

However, Potuz quickly identified a critical flaw in this proposal - the system would be unable to count attestations for unknown branches, which would break the attestation mechanism. This realization led him to abandon the approach, leaving the original problem of achieving finalization boundary alignment without consensus changes still unresolved.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-10T11:26:21.962000+00:00] potuz: I realized that there's a minimal change that allows to keep all of consensus without changing the checkpoint structure and it allows us to justify full and thus have finalization boundary aligned. It's just a minimal change to process justification and finalization. I'll have a PR ready today to show as an alternative
[2026-04-10T12:24:23.196000+00:00] potuz: Nevermind this, we wouldn't be able to count attestations for unknown branches 😦
```

</details>
