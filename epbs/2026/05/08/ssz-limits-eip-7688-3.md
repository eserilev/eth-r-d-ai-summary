# SSZ Size Limit Increase PR

**Channel:** epbs | **Date:** 2026-05-08

## Summary

jtraglia opened PR #5224 to increase SSZ size limits in the Ethereum consensus specs. potuz raised concerns about the computational cost, noting that the proposed change would require approximately 30 additional hashes to compute the hash tree root (HTR), and suggested making the limit "large but not unreasonably large" to enable zero-cost HTR computation.

The discussion converged on setting the limit to 1 million (2^20), which jtraglia proposed as a compromise. potuz confirmed this would only require a maximum of 10 extra hashes, making it computationally reasonable. m.kalinin indicated this approach aligns well with the payload reveal threshold, suggesting broader consistency with existing protocol parameters.

## Participants

- jtraglia
- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T16:20:39.013000+00:00] jtraglia: Made this PR for this:
* https://github.com/ethereum/consensus-specs/pull/5224
[2026-05-08T16:21:25.573000+00:00] potuz: that's probably 30 something extra hashes to get the root 🙂
[2026-05-08T16:21:42.961000+00:00] potuz: can we make it large but not unreasonably large  so that we can HTR it with zero cost?
[2026-05-08T16:26:16.229000+00:00] jtraglia: Ha yeah. I thought about that. How about 1 million (2**20)?
[2026-05-08T16:28:53.974000+00:00] potuz: yep,that's only 10 extra hashes max
[2026-05-08T16:29:08.164000+00:00] m.kalinin: yeah, it seems to be aligned with the payload reveal threshold
```

</details>
