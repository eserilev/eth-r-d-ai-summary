# P2P Builder Implementation and Testing

---

## 2026-03-23 (epbs)

terencechain is looking to test P2P builder functionality including gossip for proposer preferences and bids, proposer selection logic based on bid values, and payload release mechanisms. bharath.vedartham confirms that the existing [buildoor](https://github.com/ethpandaops/buildoor) implementation could be extended to support these features, though it currently lacks proposer preference reading capability and would need the beacon API from [PR #584](https://github.com/ethereum/beacon-APIs/pull/584) for proper execution payload envelope construction.

The discussion reveals that buildoor has been tested on local Kurtosis testnets pre-Gloas and already delivers payloads via the builder API, requiring mainly the addition of bid creation and submission machinery plus proposer preferences topic listening. terencechain suggests they could proceed with a lightweight builder approach that doesn't require the full beacon API, and confirms the functionality is available in their devnet0 branch, leaving the implementation approach flexible.

**Participants:** bharath.vedartham, nflaig, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-23T18:06:11.987000+00:00] terencechain: do we have a p2p builder implemented today? for kurtosis or even devnet 0 i want to test the following:
- p2p gossip for proposer preference
- p2p gossip for bids
- proposer uses the p2p bid when its value is higher than local bid
- p2p builder releases the payload after seeing the proposer includes its bid
[2026-03-23T18:33:46.341000+00:00] bharath.vedartham: we have https://github.com/ethpandaops/buildoor but it doesn't support reading proposer preferences yet. we can do that pretty easily shouldn't be a lot of work. 
I think we will need this API in a client to implement an external builder: https://github.com/ethereum/beacon-APIs/pull/584 this api is needed for the builder with the payload and execution requests to construct an execution payload envelope as the envelope construction involves a post state beacon state transition
[2026-03-23T18:34:57.105000+00:00] bharath.vedartham: does prysm support all of these things now? if so, I can work on implementing these things quickly so that you can be unblocked on testing.
We can implement listening to the proposer preferences topic pretty fast
[2026-03-23T18:36:00.334000+00:00] bharath.vedartham: buildoor has been tested pre-gloas on a local kurtosis testnet and delivers payloads via the builder-api. so i believe we would just need to link up the machinery to get it to create the bids and submit them
[2026-03-23T18:44:51.267000+00:00] nflaig: https://discord.com/channels/595666850260713488/1483497365594832917 for visibility, relevant for proposer preferences discussion above
[2026-03-23T20:12:54.952000+00:00] terencechain: i dont think we have the beacon api, but i was thinking we dont even need the beacon api, we could just having a light weight builder that does it.  I dont have a preference which direction we take
[2026-03-23T20:13:15.661000+00:00] terencechain: its in our devnet0 branch!
```

</details>

---

## 2026-03-24 (epbs)

This discussion consists of a single message from bharath.vedartham simply stating "External builder testing" as a topic. No technical details, decisions, or specific points about external builder testing were discussed in the provided messages.

The topic appears to be related to testing external block builders in the Ethereum ecosystem, but no further context, implementation details, or action items were shared in this brief exchange.

**Participants:** bharath.vedartham

<details>
<summary>Raw messages</summary>

```
[2026-03-24T07:34:12.936000+00:00] bharath.vedartham: External builder testing
```

</details>

---

## 2026-03-26 (epbs)

Terence requested access to a preliminary version of the builder API for ePBS (Ethereum Proposer-Builder Separation) implementation and testing. Bharath directed him to the latest specification in [PR #138](https://github.com/ethereum/builder-specs/pull/138) on the ethereum/builder-specs repository, noting that the main contention point is currently around request authentication. The high-level workflow involves querying a bid, putting it in a block, sending the block back to the builder, and having the builder broadcast the envelope, along with a max_trusted_bid broadcast mechanism to set trust levels.

Currently no builder supports the new API, though Bharath is working on adding support to [buildoor](https://github.com/ethpandaops/buildoor). A technical question arose about whether bid signing should be mandatory or optional - while the current spec defines it as mandatory, there's consideration that making it optional could be reasonable. Terence committed to reviewing the specification, indicating active development interest from multiple implementers.

**Participants:** bharath.vedartham, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-26T16:24:10.661000+00:00] terencechain: <@1386598056320831509> is there a a preliminary version of builder api for gloas that we can look into implement and help test?
[2026-03-26T16:25:22.542000+00:00] bharath.vedartham: https://github.com/ethereum/builder-specs/pull/138 the latest spec is here. I should take a look again and make sure its updated. 
The main contention points seem to be on request auth right now from what i understand
[2026-03-26T16:25:59.104000+00:00] bharath.vedartham: outside the builder-api on a high level should be pretty straightforward. query a bid, put the bid in a block, send block back to builder, builder broadcasts the envelope
[2026-03-26T16:26:35.014000+00:00] bharath.vedartham: there is also the max_trusted_bid broadcast, where we send the max_trusted_bid to the builder to set trust levels. The structure should be set IMO and we can iterate on it
[2026-03-26T16:27:11.430000+00:00] terencechain: Thanks! Will review today! 
Do we have a builder that support the new api today?
[2026-03-26T16:27:40.893000+00:00] bharath.vedartham: nope, i m working on adding support to that in: https://github.com/ethpandaops/buildoor might take a bit
[2026-03-26T17:46:05.854000+00:00] terencechain: is signing bid defined as optional right now or mandatory?
[2026-03-26T17:51:22.525000+00:00] bharath.vedartham: right now in the spec we define as mandatory. but I think making it optional is reasonable
```

</details>

