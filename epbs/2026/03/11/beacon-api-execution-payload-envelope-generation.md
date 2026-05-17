# ExecutionPayloadEnvelope construction API discussion

**Channel:** epbs | **Date:** 2026-03-11

## Summary

The discussion focuses on designing an API for constructing ExecutionPayloadEnvelopes by submitting beacon block roots. Bharath proposes that since `process_execution_payload` fetches the latest bid from beacon state rather than the beacon block, and the envelope requires `slot` and `builder_index` fields, the consensus layer would need to fetch the beacon block by root to populate these fields from the beacon block data.

The proposed API workflow involves: (1) constructing the envelope with the payload and execution requests while setting the beacon block root from the API parameter, (2) fetching the beacon block by root to set slot and builder index, (3) running `process_execution_payload` with verification disabled, and (4) returning the ExecutionPayloadEnvelope with the post-state root. 

Nflaig questions step 2, suggesting that builders could submit beacon blocks beforehand or that nodes likely already have the block from P2P gossip, with the beacon node potentially having the post-block state cached. This raises an open question about whether the explicit beacon block fetch is necessary or if existing caching mechanisms could be leveraged instead.

## Participants

- bharath.vedartham
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T15:39:05.501000+00:00] bharath.vedartham: thinking more, I think we can just submit the beacon block root. Because it looks like constructing the envelope for a given beacon block requires the node to have received the beacon block. Because `process_execution_payload` fetches the latest bid from the beacon state rather than from a beacon block.
In the envelope, we also need to fill up the `slot` and`builder_index`. The CL would have to fetch the beacon block by root and fill the fields from the beacon block.
[2026-03-11T15:45:34.584000+00:00] bharath.vedartham: I think the API would work like the following then:
1. Construct the envelope with the payload and execution requests sent with the API. Set the beacon block root in the envelope to the root passed to the API. 
2. Fetch the beacon block by the root and set the slot and builder index in the envelope corresponding to the one in the beacon block. 
3. Run `process_execution_payload(state, signed_execution_payload_envelope, execution_engine, verify=False)`
4. Return the output `ExecutionPayloadEnvelope` to the builder with the state root set to the post state root after `process_execution_payload`
[2026-03-11T20:43:27.696000+00:00] nflaig: not that certain about 2., can't the builder submit the beacon block beforehand, or most likely the node has already seen it on p2p from the proposer gossiping it, we can still trigger by root if it doesn't have it but it's likely the beacon node also has the post block state cached at that point
```

</details>
