# ExecutionPayloadEnvelopesByRange sync implementation differences

---

## 2026-05-11 (epbs)

The discussion centers on implementation differences between Ethereum consensus layer clients regarding `ExecutionPayloadEnvelopesByRange` syncing. Prysm developers discovered they were downscoring peers (specifically Teku nodes) for returning empty payload responses while providing valid blocks in the same range, which was causing Prysm nodes to fall out of the devnet entirely. Other teams like Nimbus and another unnamed client confirmed they sync envelopes and blocks together in parallel to avoid mismatches, while Teku also downscores peers for inconsistencies between blocks and payloads.

The root issue appears to be that Prysm's strict downscoring policy for empty payload responses may be too aggressive when dealing with legitimate Teku peers that provide blocks but not their associated envelopes. While Prysm has a potential fix to avoid downscoring on empty payload responses, the team remains concerned about the underlying cause of why Prysm nodes are losing peer connections entirely, suggesting there may be deeper networking issues beyond just the downscoring behavior.

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
[2026-05-11T19:09:39.489000+00:00] stefanbratanov: Same for Teku and we downscore peers if there's inconsistency
[2026-05-11T19:38:14.411000+00:00] potuz: Terence told me that we may be downscoring unjustly a peer that returns blocks and no payloads
```

</details>

