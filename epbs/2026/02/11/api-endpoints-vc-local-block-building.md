# API endpoints for VC local block building

**Channel:** epbs | **Date:** 2026-02-11

## Summary

The discussion centers around API endpoints needed for validator client (VC) local block building, specifically for requesting envelopes and signatures. 0xunclebill asks whether teams are following the endpoints from Shane's original PR in the beacon-APIs repository, referencing a specific commit that outlines the API structure.

nc1234 raises implementation questions about when to request payloads via `ExecutionPayloadEnvelopesByRoot`, identifying two scenarios: when receiving payload attestations with `payload_present = true` for unseen payloads, and when `BeaconBlocksByRoot` calls are triggered. However, they note uncertainty about determining whether a builder has revealed a payload for a given block. jameshe5018 confirms they're focusing on gRPC implementation rather than REST for devnet 0, with assumptions that the beacon node is stateful by default, and that blobs and PTC won't be handled initially.

nflaig indicates that while the specific APIs won't matter much for devnet-0, the PR's APIs closely reflect what a stateful flow would require, making the beacon node caching logic implementation valuable regardless. They also note that the referenced beacon-APIs PR is ready for final review and can be merged otherwise, suggesting the API specification is nearing completion.

## Participants

- 0xunclebill
- jameshe5018
- nc1234
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-11T01:54:53.987000+00:00] 0xunclebill: what is everyone doing for the api endpoints that the vc needs to request the envelope and sign
[2026-02-11T01:54:56.768000+00:00] 0xunclebill: for local block building
[2026-02-11T01:55:25.516000+00:00] 0xunclebill: are we just going off of the endpoints that were originally in shanes PR?
[2026-02-11T01:55:26.469000+00:00] 0xunclebill: https://github.com/ethereum/beacon-APIs/pull/552/commits/bd9a3c89280661b13e285789cf82b38ed4ceac70
[2026-02-11T03:15:17.953000+00:00] nc1234: Just looking at when to request payload via `ExecutionPayloadEnvelopesByRoot`. I think 1) when receiving a payload attestation referencing a block with `payload_present = true` but we haven't seen such payload 2) anything that triggers `BeaconBlocksByRoot` call. But how do we know whether builder has revealed payload for this block or not? Blindly call `ExecutionPayloadEnvelopesByRoot`?
[2026-02-11T03:54:11.384000+00:00] jameshe5018: <@358120958726373381> yeah for the most part, we are focusing on gRPC implementation and probably won't have rest for devnet 0 though. also a lot of assumptions like that the bn is stateful by default, and since blobs + ptc won't be handled i think that works
[2026-02-11T09:44:24.419000+00:00] nflaig: for devnet-0 it won't really matter but the apis from the PR are pretty close to what the stateful flow would look like, so implementing the caching logic on the beacon node will be useful either way
[2026-02-11T13:02:10.750000+00:00] nflaig: https://github.com/ethereum/beacon-APIs/pull/552 in case someone wants to give it a final pass but can be merged otherwise
```

</details>
