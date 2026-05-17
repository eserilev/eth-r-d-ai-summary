# Kurtosis testing setup and devnet branch coordination

**Channel:** epbs | **Date:** 2026-05-12

## Summary

The discussion centers around testing coordination for Ethereum client development. etan_status announced the availability of a Kurtosis testing setup in consensus-specs PR #4630 and requested notification when other clients can add branches to stabilize the setup.

povilas8490 reported pushing fixes to the `glamsterdam-devnet-2` and `glamsterdam-devnet-3` branches, but noted an issue where the `grandine-geth-1` node failed to restart on the `glamsterdam-devnet-3` branch. barnabasbusa responded by rolling out a new image, likely addressing the restart issue.

The main action item appears to be ongoing coordination between client teams to integrate their branches with the Kurtosis testing infrastructure, while resolving the node restart problem on the devnet-3 branch.

## Participants

- barnabasbusa
- etan_status
- povilas8490

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-12T07:03:31.412000+00:00] etan_status: there's a kurtosis in https://github.com/ethereum/consensus-specs/pull/4630 – please lmk when can add a branch to it from another client, would love to get it stable
[2026-05-12T07:23:38.996000+00:00] povilas8490: <@412614104222531604>, I've pushed a fix to `glamsterdam-devnet-2` and `glamsterdam-devnet-3` branches yesterday, however, `grandine-geth-1` did not restart on `glamsterdam-devnet-3`.
[2026-05-12T13:59:54.074000+00:00] barnabasbusa: rolling out the new image
```

</details>
