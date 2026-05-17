# FCU Version Discussion for Glam Devnet

---

## 2026-04-22 (epbs)

The discussion centers on which version of the Fork Choice Update (FCU) protocol should be used for the Glam devnet, with 0xunclebill initially observing that the geth and Prysm branches appeared to be using FCU v3 instead of v4. jtraglia confirmed that v4 should definitely be used and expressed surprise at the v3 usage. After examining the code, they discovered that 0xunclebill had been looking at the wrong Prysm branch - the glamsterdam-devnet-0 branch rather than the correct alpha 5 branch.

The team confirmed that geth added FCU v4 support about two weeks prior and that it's included in their devnet branch. barnabasbusa confirmed that FCU v4 is required and that they've been using the alpha 5 Prysm branch, with a specific devnet-named branch coming soon. The issue appears to have been resolved as a case of examining the wrong code branches, with 0xunclebill noting they may have been incorrectly marshalling the slot number field on their end.

**Participants:** 0xunclebill, barnabasbusa, jtraglia

<details>
<summary>Raw messages</summary>

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

