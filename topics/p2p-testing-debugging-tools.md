# P2P Testing and Debugging Tools

---

## 2026-05-12 (epbs)

The discussion centers around the need for better P2P testing and debugging tools for Ethereum consensus clients. barnabasbusa emphasizes that basic P2P functionality should be easily testable, pointing out that simple "happy path" tests (like requesting data A and verifying the peer returns A) aren't currently working properly with Teku, suggesting fundamental issues that should be caught by straightforward testing.

stefanbratanov suggests implementing debugging tools for investigating peer scoring across devnets, noting that Teku already exposes peer scores via API. terencechain mentions having created a basic UI for this purpose but hasn't had time to develop it further. When asked to share, terencechain provides a Prysm-specific implementation (https://github.com/OffchainLabs/prysm/compare/peer-scores-ui) but acknowledges the need for a more general solution that works across different client implementations.

The discussion highlights a gap in cross-client P2P testing infrastructure, with an apparent need for both basic functional tests and more sophisticated debugging tools for peer scoring mechanisms across the Ethereum consensus layer ecosystem.

**Participants:** barnabasbusa, stefanbratanov, terencechain, tomi0x

<details>
<summary>Raw messages</summary>

```
[2026-05-12T14:39:51.522000+00:00] barnabasbusa: shuoldn't we have some kinda checks for this stuff btw ?
[2026-05-12T14:40:22.307000+00:00] barnabasbusa: I feel like it should be easily testable
[2026-05-12T14:42:47.840000+00:00] barnabasbusa: I mean it would still be valuable to test if happy cases working
[2026-05-12T14:42:57.201000+00:00] barnabasbusa: e.g 

I'm asking for A does peer give me A?
[2026-05-12T14:43:16.599000+00:00] barnabasbusa: right now it seems like teku is not even giving you A, so it should be a super easy test
[2026-05-12T15:52:33.429000+00:00] stefanbratanov: I think it would be useful to have some sort of debugging tool to investigating scoring etc across devnets, in Teku we have API exposing the scores https://consensys.github.io/teku/#tag/Teku/operation/getPeersScore
[2026-05-12T16:04:04.942000+00:00] terencechain: i vibe coded a jenky UI but havne't had time to put much work into it
[2026-05-12T16:05:49.451000+00:00] tomi0x: No Nimbus 😑
[2026-05-12T16:58:05.048000+00:00] barnabasbusa: care to share?
[2026-05-12T16:58:18.620000+00:00] barnabasbusa: I can probably vibe something built on top of it
[2026-05-12T17:05:33.917000+00:00] terencechain: https://github.com/OffchainLabs/prysm/compare/peer-scores-ui its only prysm specific though.. something more general and shareable btw diff implementations will be nice
```

</details>

