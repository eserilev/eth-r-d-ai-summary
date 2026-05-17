# BAL integration and glamsterdam-devnet timeline

**Channel:** epbs | **Date:** 2026-03-11

## Summary

The discussion centers around the timing for integrating BAL (Balance) into the glamsterdam-devnet-0 testnet. The team agrees that BAL integration should occur after standalone EPBS (Enshrined Proposer-Builder Separation) devnets have stabilized, specifically after devnet1 at minimum, rather than being added immediately.

There's consensus on targeting glamsterdam-devnet-0 launch for approximately 1-2 weeks before the interop event. Potuz suggests being more aggressive with a 2-week buffer to ensure a stable testing environment is available during interop, emphasizing the importance of having benchmarking tests ready that will need to integrate with BALs. However, the exact timeline remains uncertain as it depends on how quickly the EPBS implementation stabilizes.

The main open question is the precise timing of glamsterdam-devnet-0, with participants acknowledging uncertainty around when EPBS will be sufficiently stable to proceed with the next phase of testing.

## Participants

- barnabasbusa
- jtraglia
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T16:28:52.670000+00:00] terencechain: when do you guys want to add bal?
[2026-03-11T16:45:19.823000+00:00] jtraglia: This should happen after the standalone epbs devnets, in glamsterdam-devnet-0.
[2026-03-11T16:46:22.496000+00:00] jtraglia: Depends on how long you think it will take for epbs to stabilize. Hopefully before the interop.
[2026-03-11T16:48:43.441000+00:00] terencechain: that's fine with me, but ya my main question was when glamsterdam-devnet-0
I agree its good to have a stable epbs devnet first.. so will have to be after devnet1 (at least)
[2026-03-11T16:49:32.114000+00:00] jtraglia: Yes. Your guess is as good as mine 😅
[2026-03-11T18:23:44.650000+00:00] barnabasbusa: i’d aim for interop - 1 week for glam-dev-0
[2026-03-11T21:33:28.559000+00:00] potuz: Fairly doable. Could we be nasty and try -2 so that we get there with a somewhat stable testing environment? What I would love for Interop is to be in a place in which we can run benchmarking tests during that week
[2026-03-11T21:33:43.158000+00:00] potuz: those will necessarily have to be interlocked with BALs.
```

</details>
