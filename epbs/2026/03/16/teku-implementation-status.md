# Teku EPBS Development and Branch Updates

**Channel:** epbs | **Date:** 2026-03-16

## Summary

Stefan provided an update on Teku EPBS development, reporting improved stability but noting a networking issue discovered by Enrico where the client runs out of peers to publish to when approaching finalization. The development has moved to a new unprotected branch at `https://github.com/Consensys/teku/tree/epbs-devnet-0`, and Stefan indicated he's close to completing checkpoint sync functionality.

The team agreed to stop tracking the previous branch `StefanBratanov/teku:devnet-0-changes` in favor of the new official branch. Stefan committed to providing further updates during the upcoming ACDT meeting, with the networking issue near finalization remaining as an open technical concern to be resolved.

## Participants

- barnabasbusa
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-16T10:32:37.961000+00:00] barnabasbusa: <@641548687813902336> any progress?
[2026-03-16T10:42:31.644000+00:00] stefanbratanov: I made progress and now teku should be more stable, however Enrico noticed something wrong with our networking when we go close to finalization, so we run out of peers to publish to, you can use https://github.com/Consensys/teku/tree/epbs-devnet-0 by the way, decided to make this branch unprotected. I am close to having the checkpoint sync done! Will update on ACDT.
[2026-03-16T10:42:51.632000+00:00] barnabasbusa: Thanks!
[2026-03-16T10:43:29.775000+00:00] barnabasbusa: can I stop tracking `StefanBratanov/teku:devnet-0-changes` ?
[2026-03-16T10:43:59.091000+00:00] stefanbratanov: yeah please stop that
[2026-03-16T11:30:40.800000+00:00] barnabasbusa: GitHub - Consensys/teku at epbs-devnet-0
```

</details>
