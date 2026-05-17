# ExecutionPayloadEnvelopes sync and downscoring behavior

---

## 2026-05-11 (epbs)

The discussion centers around synchronization behavior for `ExecutionPayloadEnvelopes` during early sync and how different Ethereum consensus layer clients handle peer downscoring. Terence from Prysm reported that their client syncs `ExecutionPayloadEnvelopesByRange` in parallel with `BeaconBlocksByRange` and downscores peers that return empty envelopes when block bids indicate payloads should be available. Other teams (Nimbus and another client) confirmed they import envelopes and blocks together to avoid mismatches, while Teku also downscores peers for inconsistencies.

The core issue emerged when Prysm nodes were receiving valid blocks but empty payload responses from the same peers, specifically identified as Teku nodes (including bootnodes). This downscoring behavior was causing Prysm nodes to fall out of the devnet entirely and lose connectivity. Terence tested a fix that removes downscoring for empty payload responses, which allowed syncing to continue, but expressed concern about this approach and noted it doesn't explain why Prysm nodes were disconnecting from the network entirely rather than just from problematic peers.

The discussion revealed a potential incompatibility in peer scoring strategies, with some clients being more lenient about empty payload responses while others (Prysm, Teku) implement strict downscoring. The issue remains unresolved, with concerns about whether Prysm's downscoring is too aggressive or if there's a legitimate inconsistency in how different clients serve payload data during sync.

**Participants:** 0xunclebill, barnabasbusa, nflaig, potuz, stefanbratanov, terencechain, tersec

<details>
<summary>Raw messages</summary>

```
[2026-05-11T16:12:15.938000+00:00] terencechain: Question for other CL teams: do you sync `ExecutionPayloadEnvelopesByRange` during early sync, and do you have envelopes ready to serve alongside `BeaconBlocksByRange` responses? prysm does both in parallel. when a peer empty envelopes but the block bids in that range show payloads got revealed, we mark it invalid and downscore the peer
[2026-05-11T16:20:26.279000+00:00] nflaig: https://github.com/ethereum/execution-
[2026-05-11T16:26:38.564000+00:00] 0xunclebill: during sync we import the envelopes and blocks together so there wouldn't be a situation where we have blocks for some range but not their associated envelopes
[2026-05-11T16:27:12.988000+00:00] tersec: this is at least the goal of Nimbus's design as well
[2026-05-11T16:27:35.594000+00:00] tersec: part of "resolving" (what Nimbus calls it) is the block is the envelope
[2026-05-11T16:29:07.662000+00:00] terencechain: hm we keep getting blocks by range.. response (good) but payload by range response (empty).. from the same peer.. i can find out who those peers are
[2026-05-11T16:29:23.297000+00:00] 0xunclebill: we were getting that from teku
[2026-05-11T16:33:53.460000+00:00] terencechain: but you dont downscore teku because of empty payload by range response right? prysm does and i think that's our problem
[2026-05-11T16:34:10.434000+00:00] 0xunclebill: yeah we are not downscoring them
[2026-05-11T16:53:24.825000+00:00] potuz: do you have a Peer ID?
[2026-05-11T16:59:27.857000+00:00] terencechain: ```
  │                        peer ID                        │            client            │
  ├───────────────────────────────────────────────────────┼──────────────────────────────┤
  │ 16Uiu2HAmUALppyi9RBwftvZjGqtkq3iYEd923BL4Ta1LSts1Tbxt │ teku/v26.4.0+124-g0c0cfeec2c │
  ├───────────────────────────────────────────────────────┼──────────────────────────────┤
  │ 16Uiu2HAmHhgfAFEzZAvbNKhTUQUcfNPaahtdtqn4pzUPD6NoeM4r │ teku/v26.4.0+124-g0c0cfeec2c │
  ├───────────────────────────────────────────────────────┼──────────────────────────────┤
  │ 16Uiu2HAmPYmssiP99kJrSY47y3DNhhdy4tNrx8HMwCX3PrS6Utzn │ teku/v26.4.0+124-g0c0cfeec2c │
```
[2026-05-11T16:59:49.856000+00:00] potuz: ah then you do know
[2026-05-11T17:00:17.643000+00:00] terencechain: i have a commit that dont downscore on empty payload response and keep trying.. i can sync.. but i dont feel good about it 
it doesnt explain why prysm just falls out devnet entirely
[2026-05-11T17:00:18.198000+00:00] potuz: they are Teku, the first one is `teku-geth-2`
[2026-05-11T17:00:54.179000+00:00] potuz: second is `teku-geth-1` and third is `bootnode-1`
[2026-05-11T17:01:04.809000+00:00] potuz: don't know what that last one is running cc <@412614104222531604>
[2026-05-11T17:01:17.567000+00:00] terencechain: if we lose connection to the boot node then that's bad right?
[2026-05-11T17:02:26.002000+00:00] terencechain: i dont know this is the main bug
we should be able to peer with lighthouse.. and everyone else.. and show online..
[2026-05-11T17:04:23.264000+00:00] terencechain: i think if we restart beacon nodes then we should fix it.. but doesnt explain why prysm falls out
[2026-05-11T17:33:01.965000+00:00] barnabasbusa: last one? i only see those three that you listed
[2026-05-11T17:38:19.151000+00:00] barnabasbusa: you can check on dora too
[2026-05-11T17:38:34.133000+00:00] barnabasbusa: client list shows which commit it’s even running
[2026-05-11T19:09:39.489000+00:00] stefanbratanov: Same for Teku and we downscore peers if there's inconsistency
[2026-05-11T19:38:14.411000+00:00] potuz: Terence told me that we may be downscoring unjustly a peer that returns blocks and no payloads
```

</details>

---

## 2026-05-12 (epbs)

This discussion centers on P2P syncing issues between Ethereum clients, specifically problems with payload serving between Teku and Prysm. The core issue is that potuz implemented code to penalize peers when blocks and payloads aren't compatible, but the implementation incorrectly penalizes peers (particularly Teku) even when they indicate they cannot serve payloads. This leads to Teku being downscored instead of the client requesting payloads from alternative peers.

The technical challenge stems from blocks and payloads being served through separate API calls rather than a unified request. While stefanbratanov agrees that clients should be penalized for not serving canonical payloads, potuz argues that non-responses (due to rate limiting or other issues) should be treated as "ignore" rather than penalties. The team notes that rate limiting complicates this further, as Teku maintains separate rate limits for blocks and payloads, making partial responses unlikely under normal circumstances.

The discussion reveals broader concerns about P2P testing and security. potuz expresses frustration about the complexity introduced by the current syncing design and regrets not pushing harder for EIP-8237 alongside EIP-5094, suggesting these architectural decisions are root causes of many devnet issues. The team acknowledges that malicious P2P behavior testing is insufficient, creating a risk where clients might pass happy-case tests while being vulnerable to attacks in production. An open question remains about whether Teku is actually consistently serving blocks without payloads to Prysm.

**Participants:** barnabasbusa, potuz, stefanbratanov

<details>
<summary>Raw messages</summary>

```
[2026-05-12T13:19:14.098000+00:00] stefanbratanov: Ah does that have to do with the byRange Teku issues that we discussed in ACDT? Our checks while syncing payloads are quite simple, we only downscore if block's bid is based on a full payload and it doesn't exist https://gist.github.com/StefanBratanov/a268520a28040e6bfbcfd7393f6455d8
[2026-05-12T13:24:55.647000+00:00] potuz: I added code that checks if the same peer returned blocks and payloads and to penalize if they aren't compatible in any way. But the problem is that it seems that I screwed up and even if the peer told me that can't give me payloads, I penalize him for giving me blocks
[2026-05-12T13:25:16.129000+00:00] potuz: Teku often times does not serve us the payloads, so we penalize it instead of requesting the payloads from somoeone else
[2026-05-12T13:26:19.509000+00:00] potuz: One could argue that our behavior is correct though: if you return blocks then you should also return the payloads. The problem is that the two calls are independent instead of being one, so the actual correct behavior is to not penalize you if you do not reply to one of them
[2026-05-12T13:50:08.319000+00:00] stefanbratanov: I think you should penalise us if we are not serving canonical payloads to be fair
[2026-05-12T13:52:51.641000+00:00] potuz: yeah but the problem is that the call is separated
[2026-05-12T13:53:07.111000+00:00] potuz: if I send you a call to get the payloads and you don't reply then that should be an ignore
[2026-05-12T13:53:14.756000+00:00] potuz: if you're for example rate limited
[2026-05-12T13:53:48.502000+00:00] potuz: if it were a unique call then it would be simpler... every single day since Svalbard I regret not fighting stronger for 8237
[2026-05-12T13:54:07.084000+00:00] potuz: essentially ALL problems we see on devnets steer from peering and boil down to syncing complexity
[2026-05-12T13:54:31.441000+00:00] potuz: it was utterly stupid not to bag 8237 with 5094
[2026-05-12T14:07:11.317000+00:00] stefanbratanov: ah I see, yes, if we include rate limiting in the scenario, it becomes a bit of a mess, we keep separate rate limit with the same initial number for blocks and payloads, so it's unlikely we return partial responses from one if the other one returns everything
[2026-05-12T14:39:16.717000+00:00] potuz: we consistently see from Teku that you give us blocks and you give us zero payloads. Is this correct <@363800010518822915> ?
[2026-05-12T14:39:21.750000+00:00] potuz: I saw this a few times at least
[2026-05-12T14:42:15.403000+00:00] potuz: testing p2p scoring has been pretty hard since genesis, and these are typically the hardest things to diagnose. Specially cause a client that is "doing well on tesnet" could be simply accepting anything and just trusting that peers won't be malicious. But then if we all do this and there's an actual attack on production we are fucked
[2026-05-12T14:42:43.736000+00:00] potuz: AFAIK we do not test malicious p2p behavior, or at least we do not do it broadly
[2026-05-12T14:43:25.856000+00:00] potuz: but that's the reason why happy case testing is not even possible: if I just disable any downscoring I'll be completely broken but I will pass any test in the happy case
[2026-05-12T14:44:20.017000+00:00] barnabasbusa: you think teku is downscoring prysm and thats why its giving you an empty payload?
```

</details>

