# Devnet Testing Strategy and Future Plans

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The team discussed a phased testing strategy for upcoming devnets, with potuz proposing to test withdrawals and deposits on the current devnet before moving to bids and external builders on devnet 1, followed by forkchoice tests, and finally adding the actual builder API. The approach follows a progression from basic structures to new structures to off-protocol paths.

barnabasbusa indicated that Teku needs to reach head before starting aggressive testing and suggested merging into the bal-devnet testing stream around epbs-devnet-4. They're targeting glamsterdam-devnet-0 by interop, which may require faster iteration cycles than the current 2-week devnet schedule. parithosh agreed they'll need to integrate with bal-devnet testing at some point.

potuz warned that the current devnet will likely crash when BLS to execution changes are implemented and outlined three prerequisites before allowing broader participation: hardening sync implementations to handle edge cases, implementing checkpoint sync, and setting up a reliable checkpoint server. The team expects significant stability issues as they progress through testing phases.

## Participants

- barnabasbusa
- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T13:05:34.069000+00:00] parithosh: https://giphy.com/gifs/6fMZvAOjzM9t6
[2026-03-05T13:05:58.384000+00:00] potuz: I agree, forkchoice I wouldn't even check until later than devnet 1, I think we could try to test withdrawals and deposits on this devnet and crash it, then move to bids and external builders on devnet 1, then forkchoice tests there, and finally add actual builder API in the end
[2026-03-05T13:06:24.344000+00:00] potuz: that's the natural way of testing I believe. This devnet structures, next devnet new structures, last devnet off-protocol paths
[2026-03-05T13:07:06.341000+00:00] barnabasbusa: Ideally I would like teku to get to head first
[2026-03-05T13:07:11.471000+00:00] parithosh: we'll at one of the devnets we need to merge it into the bal-devnet testing stream. but we can discuss when thats possible.
[2026-03-05T13:07:11.868000+00:00] barnabasbusa: before we start crashing lol
[2026-03-05T13:07:25.975000+00:00] barnabasbusa: probably around/after epbs-devnet-4
[2026-03-05T13:07:37.925000+00:00] barnabasbusa: I'd aim to get glamsterdam-devnet-0 by interop
[2026-03-05T13:07:50.762000+00:00] barnabasbusa: so we might need to do a bit quicker than 2w iteration cycles per devnet
[2026-03-05T13:08:58.229000+00:00] potuz: I think we will completely crash this one when we send BLS to exec changes. I propose we do not let <@412614104222531604> near any of these pods until we 
1) Harden sync implementations. There are lots of edge cases, I'll fix the one that was found now. 
2) Implement checkpoint sync
3) Set a checkpoint server that reliably serves the right checkpoint
```

</details>
