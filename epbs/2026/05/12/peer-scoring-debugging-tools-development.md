# Peer scoring debugging tools development

**Channel:** epbs | **Date:** 2026-05-12

## Summary

The discussion centers around developing debugging tools for peer scoring across Ethereum devnets. stefanbratanov suggested creating tools to investigate scoring mechanisms, noting that Teku already has an API that exposes peer scores. terencechain mentioned having created a basic UI prototype but hasn't had time to develop it further, while tomi0x noted the absence of Nimbus support.

When barnabasbusa expressed interest in collaborating and potentially building upon existing work, terencechain shared a GitHub link to their Prysm-specific implementation. However, they acknowledged that their current solution is limited to Prysm and emphasized the need for a more general, cross-implementation tool that could work across different Ethereum client implementations.

The conversation leaves open the question of developing a unified debugging tool that would work across multiple client implementations, with potential collaboration between barnabasbusa and terencechain on extending the existing prototype.

## Participants

- barnabasbusa
- stefanbratanov
- terencechain
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-12T15:52:33.429000+00:00] stefanbratanov: I think it would be useful to have some sort of debugging tool to investigating scoring etc across devnets, in Teku we have API exposing the scores https://consensys.github.io/teku/#tag/Teku/operation/getPeersScore
[2026-05-12T16:04:04.942000+00:00] terencechain: i vibe coded a jenky UI but havne't had time to put much work into it
[2026-05-12T16:05:49.451000+00:00] tomi0x: No Nimbus 😑
[2026-05-12T16:58:05.048000+00:00] barnabasbusa: care to share?
[2026-05-12T16:58:18.620000+00:00] barnabasbusa: I can probably vibe something built on top of it
[2026-05-12T17:05:33.917000+00:00] terencechain: https://github.com/OffchainLabs/prysm/compare/peer-scores-ui its only prysm specific though.. something more general and shareable btw diff implementations will be nice
```

</details>
