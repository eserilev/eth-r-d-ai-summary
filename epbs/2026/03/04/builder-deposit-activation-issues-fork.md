# Builder deposits implementation status across clients

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion covers the implementation status of builder deposits across different Ethereum clients. pk910 reports that Prysm appears to be the only client not yet implementing builder deposits, while Lighthouse and Lodestar have both been successfully tested and can properly process registrations and maintain builders in state.

However, potuz raises a significant concern about potential client failures when there's a pending deposit queue at the time of the fork transition. While Prysm will handle deposit processing correctly, they believe this queue scenario could cause issues across all client implementations, suggesting a broader compatibility problem that may need attention before deployment.

## Participants

- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T08:10:25.057000+00:00] pk910: I think prysm is the only one not implementing it then 😄
We've already successfully tested builder deposits on lighthourse & lodestar.
Both pass the registration and have the builder in the state
[2026-03-04T09:20:11.754000+00:00] potuz: We will process deposits fine. But I'm pretty sure every client will be broken if you had a pending queue at the fork
```

</details>
