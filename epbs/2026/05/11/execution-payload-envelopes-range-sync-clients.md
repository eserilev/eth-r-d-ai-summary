# ExecutionPayloadEnvelopesByRange sync implementation across clients

**Channel:** epbs | **Date:** 2026-05-11

## Summary

The discussion centers around synchronization issues with `ExecutionPayloadEnvelopesByRange` across different Ethereum consensus layer clients. Terence from Prysm reported that their client syncs blocks and payload envelopes in parallel and downscores peers that return empty envelopes when blocks indicate payloads should be available. However, this approach is causing problems as Prysm is receiving empty payload responses from Teku nodes while getting valid block responses from the same peers.

Other client teams (Nimbus and an unnamed client) confirmed they import envelopes and blocks together to avoid mismatched states, and crucially, they don't downscore peers for empty payload responses. Stefan from Teku later clarified that Teku does downscore for inconsistencies, but the evidence suggests Prysm's downscoring strategy may be too aggressive. The issue is causing Prysm nodes to fall out of the devnet entirely, with specific Teku peer IDs identified as returning the problematic empty payload responses.

The main unresolved question is whether Prysm should modify their downscoring logic to be less strict about empty payload responses, as Terence mentioned having a commit that removes downscoring and allows syncing to continue, though he expressed concerns about this approach. The root cause of why Prysm nodes are disconnecting from the network entirely remains unclear and may require further investigation.

## Participants

- 0xunclebill
- barnabasbusa
- nflaig
- potuz
- stefanbratanov
- terencechain
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
