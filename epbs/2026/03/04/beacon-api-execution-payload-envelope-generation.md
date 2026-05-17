# Execution Payload Envelopes Protocol Support

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around a protocol negotiation failure during Ethereum consensus client synchronization. Potuz reported that their client could not request execution payload envelopes by range from peers, with 64 peers failing to support the `/eth2/beacon_chain/req/execution_payload_envelopes_by_range/1/ssz_snappy` protocol. While the client was successfully receiving blocks by range, it was unable to obtain the corresponding execution payload envelopes needed for proper synchronization.

The team investigated which consensus clients support serving execution payload envelopes and confirmed that neither Lodestar nor Lighthouse currently implement this functionality. Jameshe5018 explicitly confirmed that Lodestar lacks this feature, and there was no response regarding Lighthouse's support. This lack of protocol support across major consensus clients means that synchronization requiring execution payload envelopes cannot proceed, as adding more peers would not resolve the underlying protocol compatibility issue.

## Participants

- jameshe5018
- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T17:12:50.774000+00:00] potuz: ```
[2026-03-04 17:12:15.96] DEBUG initial-sync: Could not request execution payload envelopes by range from peer count=64 error=new stream: failed to negotiate protocol: protocols not supported: [/eth2/beacon_chain/req/execution_payload_envelopes_by_range/1/ssz_snappy] peer=16Uiu2HAkwrQaRVHAWah1Csf33wbUKRRghieAyBJkKJAZ2JYrHaXR startSlot=192
`````
May be difficult
[2026-03-04T17:13:55.941000+00:00] potuz: yeah we're not getting any envelope
[2026-03-04T17:14:08.174000+00:00] potuz: does either Lighthouse or Lodestar supports serving them?
[2026-03-04T17:14:24.592000+00:00] potuz: we're getting the blocks by range but not the envelopes by range
[2026-03-04T17:17:33.529000+00:00] potuz: yeah we're not going to sync without peers
[2026-03-04T17:18:36.970000+00:00] jameshe5018: lodestar doesn't have it
[2026-03-04T17:18:53.268000+00:00] potuz: <@358120958726373381> do you guys have that?
[2026-03-04T17:38:28.112000+00:00] parithosh: okay im back, adding more peers now
[2026-03-04T17:38:45.657000+00:00] potuz: no, peers are irrelevant I think neither Lighthouse nor Lodestar supports this
[2026-03-04T17:38:51.742000+00:00] parithosh: ah okay
```

</details>
