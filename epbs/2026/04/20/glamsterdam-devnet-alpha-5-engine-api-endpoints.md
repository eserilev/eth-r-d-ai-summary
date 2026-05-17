# Glamsterdam Devnet Alpha 5 Engine API Endpoints

**Channel:** epbs | **Date:** 2026-04-20

## Summary

barnabasbusa requested that team members notify him when they have a ready branch (`glamsterdam-devnet-0` or trunk) for testing with the alpha 5 specification. He mentioned that his current PRs are minor "airdrop-farming nits."

tomi0x sought confirmation on the expected Engine API endpoints, listing `engine_newPayloadV5`, `engine_getPayloadV6`, and `engine_forkchoiceUpdatedV3`. barnabasbusa corrected this by indicating that it should be `fcUv4` (fork choice updated v4) instead of v3, referencing a previous Discord message and linking to the execution APIs documentation for Amsterdam on GitHub.

The discussion establishes the correct Engine API endpoint versions for the Glamsterdam devnet alpha 5, with the main clarification being the use of `engine_forkchoiceUpdatedV4` rather than v3.

## Participants

- barnabasbusa
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-20T08:31:13.012000+00:00] barnabasbusa: <@&1417820113981145228> pls let me know when you have a ready to go branch (`glamsterdam-devnet-0`) or trunk branch ready to be tested with alpha 5 spec!
[2026-04-20T08:32:53.111000+00:00] barnabasbusa: Both my PRs are airdrop-farming nits,
[2026-04-20T08:40:36.705000+00:00] tomi0x: to confirm, the expected engine API endpoints are?
`- engine_newPayloadV5  `                                                                                                                
` - engine_getPayloadV6`
 `- engine_forkchoiceUpdatedV3 `
[2026-04-20T08:43:02.921000+00:00] barnabasbusa: https://discord.com/channels/595666850260713488/874767108809031740/1494691534770016277
[2026-04-20T08:43:12.161000+00:00] barnabasbusa: pretty sure its fcUv4
[2026-04-20T08:43:20.239000+00:00] tomi0x: okay
[2026-04-20T08:43:50.632000+00:00] barnabasbusa: https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md?plain=1#L32
```

</details>
