# Engine API Updates and PR Reviews

**Channel:** epbs | **Date:** 2026-04-17

## Summary

The discussion centers around Engine API updates and multiple PR reviews for Ethereum specifications. Nero_eth approved a PR and created a follow-up updating `engine_getPayloadV6` in builder specs, while discussing BAL (Block Availability List) hash validation with potuz. The conversation clarified that execution layer clients need to compare hashes between the BAL included in the payload and the virtually computed one, with the EL's own block header containing the bal_hash used for verification.

Multiple PRs were highlighted for review and inclusion, including consensus spec PRs #4892, #4898, and #4840, as well as execution-apis PR #770. Barnabasbusa listed several new Engine API methods being updated: `engine_newPayloadV5`, `engine_getPayloadV6`, `engine_getPayloadBodiesByHashV2`, `engine_getPayloadBodiesByRangeV2`, `engine_forkchoiceUpdatedV4`, and `PayloadAttributesV4`.

The team reached consensus that PR #770 should be merged as soon as possible, while potuz noted that their two PRs are minor readability improvements that could be merged or closed without significant impact. An active review request was made for PR #4840, indicating ongoing coordination needed to finalize these Engine API specifications.

## Participants

- barnabasbusa
- jtraglia
- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-17T13:02:51.263000+00:00] nero_eth: Reviewed your PR, <@755590043632140352> and lgtm, thanks!
I had one small follow-up PR, updating engine_getPayloadV6 in the builder specs.
cc <@592004585506340885>
[2026-04-17T13:04:28.003000+00:00] potuz: I mentioned it because you say that clients will have to compare against an expected hash but I guess this now should be compare the hashes of both the BAL included in the Payload and the virtual computed one
[2026-04-17T13:10:47.607000+00:00] nero_eth: Yeah the EL has it's own block header with the bal_hash inside. That's the one used for comparing and checking the BAL is correct.
So, this only concerns EL clients.
[2026-04-17T13:26:50.509000+00:00] jtraglia: Thanks Toni! The PR: https://github.com/ethereum/consensus-specs/pull/5123
[2026-04-17T13:30:31.915000+00:00] barnabasbusa: `engine_newPayloadV5`
`engine_getPayloadV6`
`engine_getPayloadBodiesByHashV2`
`engine_getPayloadBodiesByRangeV2`
`engine_forkchoiceUpdatedV4`
`PayloadAttributesV4`
[2026-04-17T13:59:52.159000+00:00] barnabasbusa: https://github.com/ethereum/consensus-specs/pull/4892  & https://github.com/ethereum/consensus-specs/pull/4898 <@755590043632140352> we want these in?
[2026-04-17T14:03:04.725000+00:00] barnabasbusa: https://github.com/ethereum/execution-apis/pull/770 also this should probably make it in too? cc <@360491619402776577> <@755590043632140352>
[2026-04-17T14:09:12.332000+00:00] jtraglia: <@&1417820113981145228> would appreciate reviews on this PR:
https://github.com/ethereum/consensus-specs/pull/4840
[2026-04-17T15:27:01.858000+00:00] potuz: Both my PRs are airdrop-farming nits, they are fine if merged and fine if they aren't. If no one finds them useful to the readability of the spec they can be closed, I wouldn't mind. 770 should indeed be merged ASAP
```

</details>
