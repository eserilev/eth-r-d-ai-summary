# FCU v3 vs v4 version discussion for devnet

**Channel:** epbs | **Date:** 2026-04-22

## Summary

The discussion centers on which version of the FCU (forkchoiceUpdated) API should be used for the "glam devnet 0" - specifically whether to use v3 or v4. 0xunclebill initially observed that both Geth and Prysm appeared to be using FCU v3 rather than v4, but this turned out to be due to looking at incorrect branches. Upon further investigation, jtraglia confirmed that Geth added v4 support about two weeks prior and that it's included in their devnet branch.

The team reached consensus that FCU v4 should definitely be used for the devnet. The confusion was resolved when participants confirmed they were examining the correct branches - barnabasbusa noted they've been using Prysm's alpha 5 branch with a specific devnet branch coming soon. 0xunclebill identified that their issue was likely a marshalling problem with the slot number field on their end rather than a version compatibility issue.

## Participants

- 0xunclebill
- barnabasbusa
- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-22T04:18:33.640000+00:00] 0xunclebill: seems like that geth branch (and prysm?) are using fcu v3, not v4?
[2026-04-22T04:27:37.556000+00:00] 0xunclebill: is that the plan for glam devnet 0?
[2026-04-22T04:30:38.483000+00:00] jtraglia: We should definitely use v4.
[2026-04-22T04:31:02+00:00] jtraglia: Have you checked prysm/geth source code to confirm?
[2026-04-22T04:32:59.452000+00:00] jtraglia: It's just that I would find that a little surprising.
[2026-04-22T04:35:28.873000+00:00] 0xunclebill: https://github.com/OffchainLabs/prysm/blob/ea9bcf32c9d60c90b117edfa2329ef14c106e307/beacon-chain/execution/engine_client.go#L274
[2026-04-22T04:37:33.954000+00:00] jtraglia: Ah lol. And just to confirm that's from their glamsterdam-devnet-0 branch, whatever that is? One thing to mention is that <@412614104222531604>'s screenshot includes custom changes to Prysm. This could have been one of those changes.
[2026-04-22T04:38:21.203000+00:00] 0xunclebill: ah maybe im looking at the wrong branch
[2026-04-22T04:38:58.969000+00:00] jtraglia: I see that geth added support for V4 about two weeks ago.
https://github.com/ethereum/go-ethereum/commit/f811bfe4fdec2fe1dd384a65db415848787a9ab8
Still checking if it's actually in their devnet branch; I think it is.
Edit: it is.
[2026-04-22T04:39:10.604000+00:00] 0xunclebill: yeah i think it is
[2026-04-22T04:39:39.269000+00:00] 0xunclebill: i geuss thres also a alpha 5 branch for prysm, sorry i must have been looking at the wrong thing
[2026-04-22T04:41:00.274000+00:00] 0xunclebill: i think were just marshalling the slot number field incorrectly on our end lol
[2026-04-22T05:44:28.674000+00:00] barnabasbusa: we def need v4 yes, and Ive been mostly using their alpha 5 branch, specific devnet named branch coming soon
```

</details>
