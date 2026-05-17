# Dora explorer attestation display issues

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion centers around display issues with attestations in the Dora blockchain explorer. While the explorer loads properly, potuz noticed that all attestations are showing zero attesters and there are no clickable attestation links available. 

pk910 investigated further and identified that the attestations appear to have empty aggregation bitmasks, though they suspect this may be a bug within Dora itself rather than an actual network issue. The conversation ends with pk910 committing to investigate the root cause of the display problem. Additionally, potuz observed some interesting attestation patterns and noted that Lodestar is missing occasional blocks, but decided against downloading blocks for further analysis at that time.

## Participants

- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T00:04:54.712000+00:00] pk910: wdym?  loads for me
[2026-03-06T00:05:12.406000+00:00] potuz: It loads, but all attestations are with zero attesters
[2026-03-06T00:05:29.109000+00:00] pk910: oh,  interesting
[2026-03-06T00:05:30.923000+00:00] potuz: Ah there's no attestation link
[2026-03-06T00:05:32.805000+00:00] potuz: 🙂
[2026-03-06T00:06:52.975000+00:00] potuz: Lodestar is missing a block here and there and the attestation pattern is interesting, but I don't want to download blocks this late 🙂
[2026-03-06T00:07:15.505000+00:00] pk910: It looks like these attestations have an empty aggregation bitmask 🤔 
but might be a dora bug..  let me dig into it 🙂
```

</details>
