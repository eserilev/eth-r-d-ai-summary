# Fork reorgs and local builders registration

**Channel:** epbs | **Date:** 2025-12-16

## Summary

This discussion explores two related technical concerns around Ethereum fork transitions. Potuz raises a question about whether fork reorgs are only problematic when the network is already experiencing finalization issues. 

Paul Harris follows up by asking about the practical implications of having local builders handle block production during fork transitions, specifically wondering if it would be problematic for local builders to propose "a few blocks at the fork" while external builders complete their registration process for the new fork rules. This suggests there may be a registration or transition period where the normal builder ecosystem needs time to adapt to fork changes.

Both questions remain open without responses in this snippet, indicating these are ongoing considerations for how to handle the builder registration process and potential reorg scenarios during network upgrades.

## Participants

- .paulharris
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-16T00:10:33.128000+00:00] potuz: So reorgs at the fork are only a problem if we're already not finalizing correct?
[2025-12-16T02:44:14.502000+00:00] .paulharris: so, at its simplest, is it bad if local builders had to do a few blocks at the fork? like if builders had to register at the fork and until they're registered some local builders need to propose...
```

</details>
