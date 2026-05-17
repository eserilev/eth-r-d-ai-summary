# Devnet-2 as Glamsterdam 0 proposal

---

## 2026-04-13 (epbs)

potuz proposed using devnet-2 as "Glamsterdam 0" with a previous stable branch of Bal, asking what obstacles might prevent this approach. They suggested targeting the first day of interoperability testing for this implementation, with Kurtosis runs planned for the days leading up to the interop event.

The discussion appears to be in early stages, with potuz floating the proposal but no responses or feedback provided in these messages. The suggestion remains an open question awaiting input from other team members regarding potential blockers or feasibility concerns.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-13T22:20:42.141000+00:00] potuz: <@412614104222531604> what's stopping us from running devnet-2 as Glamsterdam 0 with a previous stable branch of Bal?
[2026-04-13T22:21:16.334000+00:00] potuz: We could target that for first day interop with Kurtosis runs the days leading to it
```

</details>

---

## 2026-04-20 (epbs)

The discussion centers around preparing the Glamsterdam devnet with the alpha 5 specification. Barnabasbusa requested that team members notify when they have a ready-to-go `glamsterdam-devnet-0` branch or trunk branch for testing, mentioning that his current PRs contain minor "airdrop-farming nits."

Tomi0x sought clarification on the expected Engine API endpoints for the devnet, initially listing `engine_newPayloadV5`, `engine_getPayloadV6`, and `engine_forkchoiceUpdatedV3`. However, barnabasbusa corrected this by pointing to previous Discord messages and the official execution-apis documentation, confirming that `engine_forkchoiceUpdatedV4` should be used instead of V3.

The main action item is waiting for development teams to prepare their implementation branches for testing with the alpha 5 specification, with the correct Engine API endpoint versions now clarified through reference to the official Amsterdam specification documentation.

**Participants:** barnabasbusa, tomi0x

<details>
<summary>Raw messages</summary>

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

