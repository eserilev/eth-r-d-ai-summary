# P2P testing and scoring challenges

**Channel:** epbs | **Date:** 2026-05-12

## Summary

The discussion centers on the challenges of testing P2P (peer-to-peer) scoring mechanisms in Ethereum clients. barnabasbusa suggests that P2P behavior should be easily testable, particularly for basic functionality like whether requesting data "A" from a peer actually returns "A" - citing Teku as an example of a client that appears to be failing even this simple case.

potuz explains that P2P scoring has been notoriously difficult to test since Ethereum's genesis, creating a fundamental testing paradox. Clients that appear to work well on testnets might simply be accepting all data without proper validation, trusting that peers won't be malicious. However, if all clients adopt this permissive approach and a real attack occurs on mainnet, the entire network becomes vulnerable. The team acknowledges they don't broadly test malicious P2P behavior, and even basic "happy case" testing is problematic because disabling downscoring mechanisms (which would be necessary for simple testing) would break the client's security model while still allowing it to pass tests.

The conversation highlights a significant gap in Ethereum's testing infrastructure, where the very security mechanisms that protect the network in production make it difficult to create reliable tests for P2P functionality.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
```

</details>
