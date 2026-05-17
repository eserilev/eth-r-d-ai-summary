# ePBS Self-Building and Payload Envelope Process

**Channel:** epbs | **Date:** 2026-01-28

## Summary

The discussion focuses on the technical implementation of ePBS (enshrined Proposer-Builder Separation) self-building and payload envelope processes. Potuz outlined a detailed workflow where payload data must be transmitted multiple times between the validator client (VC) and beacon node (BN): first when the BN assembles a block locally instead of using external builders, then when the VC requests a payload envelope for signing, and finally when posting the signed envelope. This results in the full payload data (including requests and withdrawals) traveling at least three times through the system, presenting significant optimization opportunities.

The conversation also addressed how builders handle chain reorganizations and head changes. Bharath.vedartham provided insights into rbuilder's approach, explaining that it builds payloads based on the parent block hash from payload attributes events and actively monitors for canonical chain changes, canceling builds if the head changes to avoid building on non-canonical blocks. The discussion suggests that well-connected beacon clients should typically follow the chain head under normal network conditions.

Key open questions remain around supporting builders that build on different heads and optimizing the payload data transmission process. Potuz indicated they would focus on building on the current head for now, leaving more complex multi-head building scenarios for builders to handle independently.

## Participants

- bharath.vedartham
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-28T15:02:22.390000+00:00] potuz: Cross posting this here, we need to send all of the payload data necessary to the endpoint to produce a payload envelope on the beacon API
https://discord.com/channels/595666850260713488/692078615269212180/1466085824268468357
[2026-01-28T15:11:07.305000+00:00] potuz: so to be clear how even the self-building path would need to be changed (cc <@363800010518822915> cause this is very different than what we had implemented). 

- Previous slot BN sends FCU with payload attributes, triggers block production
- Start of the slot, VC calls to get a beacon block to sign. 
- The beacon node calls get payload, gets bids from builders but decides to locally build. Assembles the block and sends it back to the VC and **it needs to send back everything related to the payload, the requests, the withdrawals everything!** 
- The VC signs the block and sends it back to post and broadcast. 
- The VC now calls and endpoint in the beacon node to get a payload envelope to sign. It needs to send everything back to the beacon node: payload, requests, withdrawals, everything!. The beacon node prepares the payload envelope to sign and it sends it back to the VC (yet again everything needs to go back)
- The VC signs and sends yet again everything to the node to post and broadcast. 

We could potentially optimize this a lot, the full payload is travelling at least 3 times now, but builders/vouch would need the receiving beacon node, when they get a request to produce a payload envelope to sign, they will need all the data to actually produce it.
[2026-01-28T15:11:51.444000+00:00] potuz: Also I am not really sure how we can help to support builders building on top of different heads. I'll leave that up to builders for now. I think it's fine if we just concentrate on building on top of our current head
[2026-01-28T15:42:31.952000+00:00] jtraglia: Unrelated, could I get some eyes on this PR? Is my math correct?
https://github.com/ethereum/consensus-specs/pull/4869
[2026-01-28T16:06:07.216000+00:00] bharath.vedartham: so according to rbuilder's code here: https://github.com/flashbots/rbuilder/blob/e9ea2aa5cd307f00edc761375317a6d43de3dbe9/crates/rbuilder/src/live_builder/payload_events/mod.rs#L184 they build the payload on top of the parent block hash indicated in the payload attributes event. I can also confirm with them on this
It appears that rbuilder also regularly monitors to see if the parent block they are building on is still the canonical block hash to ensure that there is no reorg possibility. If the head changes it looks like they cancel their build.
[2026-01-28T16:09:03.291000+00:00] bharath.vedartham: If I am right payload attributes is called when the client starts building the block for the next slot and it would return the parent beacon block root and parent block hash based on the output of get_head from the forkchoice. I would under no crazy network asynchrony, the builder would run a very well connected beacon client and always be following the head of the chain?
```

</details>
