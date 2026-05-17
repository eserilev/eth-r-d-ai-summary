# Builder payload reveal timing and attestation thresholds

**Channel:** epbs | **Date:** 2026-05-15

## Summary

The discussion centers on when builders should reveal their payloads in the Ethereum consensus mechanism. jtraglia initially asked whether builders should wait for 66%+ attestations before revealing, or decide not to reveal if insufficient attestations are seen by `ATTESTATION_DUE_BPS_GLOAS`. However, potuz clarified that honest behavior should be to submit payloads as soon as they see the block, though builders may choose to be more strategic.

The conversation then focused on interpreting current specification language that mentions a 40% threshold and "payload withheld messages." potuz explained that the 40% number comes from proposer boost mechanics, not the 66% supermajority threshold jtraglia initially referenced. However, potuz identified that the specification text discussing "withheld messages" appears to be outdated content from early 2024 drafts that should no longer be relevant.

The main conclusion is that the current specification contains obsolete language about payload withholding that needs updating, while the core honest behavior remains submitting payloads immediately upon seeing a block. The 40% threshold relates to proposer boost rather than payload revelation timing, leaving some ambiguity about the exact revelation strategy builders should follow.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-15T18:40:48.029000+00:00] jtraglia: I have a dumb question. When exactly should a builder reveal their payload? My understanding is that they should reveal as soon as they see that 66%+ of attestations support the block. If at `ATTESTATION_DUE_BPS_GLOAS` they do not see enough attestations for the block, they should decide not to reveal the payload. Is this accurate?
[2026-05-15T19:25:29.446000+00:00] potuz: The honest behavior is to submit as soon as they see the block, if they want to be fancy so be it, but the spec should say that they need to reveal when they see a block
[2026-05-15T19:27:33.687000+00:00] jtraglia: But not always. The spec does say this:
[2026-05-15T19:29:14.571000+00:00] jtraglia: I don't fully understand where the 40% number [in this PR](https://github.com/ethereum/consensus-specs/pull/4807) comes from:
[2026-05-15T19:29:16.625000+00:00] jtraglia: 
[2026-05-15T19:32:49.403000+00:00] potuz: *may* is keyword here, and I think that spec also may be very old, from where are you taking this? "payload withheld mesages" sounds like an artifact of my first spec circa 2024
[2026-05-15T19:33:25.489000+00:00] potuz: that 40% is from proposer boost
[2026-05-15T19:34:46.829000+00:00] jtraglia: That's from here, currently in master:
https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/builder.md#honest-payload-withheld-messages
[2026-05-15T19:35:36.995000+00:00] jtraglia: And hmm. Okay so a builder can safely reveal when they see 40% of attestations in favor of the block? Not 66%.
[2026-05-15T21:40:40.899000+00:00] potuz: That is definitely an artifact from the early draft, withheld message doesn't make any sense anymore. Probably the main text was changed and the title of the section was maintained
```

</details>
