# P2P Testing and Scoring Tools

---

## 2026-05-12 (epbs)

The discussion centers around the lack of comprehensive testing for P2P scoring mechanisms in Ethereum clients. Barnabasbusa suggests implementing checks for P2P behavior, noting that even basic "happy case" testing (like verifying if requesting data A returns A) would be valuable, especially since Teku appears to not be providing expected data to Prysm. However, potuz explains that P2P scoring has been notoriously difficult to test since genesis because clients might appear to work well on testnets by simply accepting all data without proper validation, but this approach would fail catastrophically during actual attacks on mainnet.

The conversation reveals that malicious P2P behavior testing is not done broadly across the ecosystem, creating a significant blind spot. Stefan suggests developing debugging tools for investigating scoring across devnets, mentioning that Teku has an API for exposing peer scores. Terence mentions having created a basic UI for monitoring peer scores but notes it's Prysm-specific, expressing interest in building something more general that works across different client implementations.

The discussion highlights an open need for better P2P testing infrastructure and cross-client debugging tools, with Terence sharing a GitHub link to his preliminary work and suggesting collaboration on a more universal solution.

**Participants:** barnabasbusa, potuz, stefanbratanov, terencechain, tomi0x

<details>
<summary>Raw messages</summary>

```
[2026-05-12T14:39:51.522000+00:00] barnabasbusa: shuoldn't we have some kinda checks for this stuff btw ?
[2026-05-12T14:40:22.307000+00:00] barnabasbusa: I feel like it should be easily testable
[2026-05-12T14:42:15.403000+00:00] potuz: testing p2p scoring has been pretty hard since genesis, and these are typically the hardest things to diagnose. Specially cause a client that is "doing well on tesnet" could be simply accepting anything and just trusting that peers won't be malicious. But then if we all do this and there's an actual attack on production we are fucked
[2026-05-12T14:42:43.736000+00:00] potuz: AFAIK we do not test malicious p2p behavior, or at least we do not do it broadly
[2026-05-12T14:42:47.840000+00:00] barnabasbusa: I mean it would still be valuable to test if happy cases working
[2026-05-12T14:42:57.201000+00:00] barnabasbusa: e.g 

I'm asking for A does peer give me A?
[2026-05-12T14:43:16.599000+00:00] barnabasbusa: right now it seems like teku is not even giving you A, so it should be a super easy test
[2026-05-12T14:43:25.856000+00:00] potuz: but that's the reason why happy case testing is not even possible: if I just disable any downscoring I'll be completely broken but I will pass any test in the happy case
[2026-05-12T14:44:20.017000+00:00] barnabasbusa: you think teku is downscoring prysm and thats why its giving you an empty payload?
[2026-05-12T15:52:33.429000+00:00] stefanbratanov: I think it would be useful to have some sort of debugging tool to investigating scoring etc across devnets, in Teku we have API exposing the scores https://consensys.github.io/teku/#tag/Teku/operation/getPeersScore
[2026-05-12T16:04:04.942000+00:00] terencechain: i vibe coded a jenky UI but havne't had time to put much work into it
[2026-05-12T16:05:49.451000+00:00] tomi0x: No Nimbus 😑
[2026-05-12T16:58:05.048000+00:00] barnabasbusa: care to share?
[2026-05-12T16:58:18.620000+00:00] barnabasbusa: I can probably vibe something built on top of it
[2026-05-12T17:05:33.917000+00:00] terencechain: https://github.com/OffchainLabs/prysm/compare/peer-scores-ui its only prysm specific though.. something more general and shareable btw diff implementations will be nice
```

</details>

