# Beacon API Endpoints for Builder Bid Preparation

**Channel:** epbs | **Date:** 2026-03-31

## Summary

The discussion centers around the need for Beacon API endpoints to support builder bid preparation, where validator clients can act as builders. Potuz is looking for specifications to prepare bids for validator client signing, beyond just the existing endpoint for publishing already-signed bids. Several GitHub pull requests are referenced (#552, #584, #580) that define various endpoints, but none are currently merged and the API structure remains unsettled, with debate over whether endpoints belong in the `/validator` or `/builder` namespace.

The core technical challenge involves defining endpoints that allow the beacon node to cache payloads and state transitions while builders (including validator clients acting as builders) can request bids and envelopes to sign with appropriate parameters like builder index and fee recipient. Potuz expresses frustration that the current API proposals don't adequately support external builders tweaking execution payment values in bids, making some endpoints potentially "mostly useless" for real builder workflows. The discussion reveals a gap between the theoretical API design and practical implementation needs.

Due to the incomplete state of the standardized Beacon API for builder functionality, Potuz decides to proceed with Prysm's internal gRPC implementation rather than wait for the beacon API to mature. The group acknowledges that no one has actually implemented these builder endpoints yet, making it premature to merge the proposed specifications until there's real implementation experience to validate the design choices.

## Participants

- bharath.vedartham
- jameshe5018
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-31T13:45:48.257000+00:00] potuz: Do we have beacon API endpoint specifications already to prepare a bid? so that the validator client signs it?
[2026-03-31T13:45:52.718000+00:00] potuz: cc
[2026-03-31T13:46:04.158000+00:00] potuz: <@1386598056320831509> <@884836500037066862> <@586161934425128960>
[2026-03-31T13:46:39.546000+00:00] potuz: I see the endpoint to publish the already signed one
[2026-03-31T13:46:47.459000+00:00] potuz: But I mean if there's any spec to prepare it
[2026-03-31T13:47:57.562000+00:00] jameshe5018: https://github.com/ethereum/beacon-APIs/pull/552
[2026-03-31T13:48:40.053000+00:00] nflaig: there is `GET /eth/v1/validator/execution_payload_bid/{slot}/{builder_index}` but it seems wrong that this is a validator api, it should be in the `/builder` namespace
[2026-03-31T13:49:38.103000+00:00] potuz: there's no builder namespace yet right?
[2026-03-31T13:49:48.420000+00:00] potuz: I'll implement it in the validator namespace for now, we can change this later
[2026-03-31T13:50:05.834000+00:00] potuz: thanks for the link, tried looking for it and couldn't see it
[2026-03-31T13:50:34.202000+00:00] nflaig: there was builder namespace which had a single api to get expected withdralwals but this one was deprecated and removed
[2026-03-31T13:50:41.011000+00:00] nflaig: https://github.com/ethereum/beacon-APIs/pull/584 re-introduces it
[2026-03-31T13:55:06.718000+00:00] potuz: ok, navigating that repo is impossible for me. What is the endpoint to request the payload envelope to sign? if the bid is in the validator namespace, I was expecting to see a similar one for the envelope
[2026-03-31T13:55:51.325000+00:00] potuz: I'm fine moving them both to a builder namespace, but whatever we do we need obviously at least these two endpoints to request a bid and an envelope to sign
[2026-03-31T13:56:32.301000+00:00] nflaig: you mean a local build or as external builder, the later is what is defined in #584 I linked above
[2026-03-31T13:57:44.794000+00:00] potuz: local builder and external builder should not be different, but yes I mean external builder, so we do not have anything merged yet for the payload? we have something merged for he bid?
[2026-03-31T14:00:39.944000+00:00] nflaig: local assumes the payload is build by the node itself, while the one for external builders doesn't, it just for the builder the get a envelope with the correct state root so they don't need to re-implement state transition
[2026-03-31T14:01:29.858000+00:00] nflaig: https://github.com/ethereum/beacon-APIs/pull/580 is for local builds
[2026-03-31T14:01:50.638000+00:00] potuz: we may be talking different things then, do we have an endpoint for a local builder that is prepareing a bid/envelope to act as an external builder?
[2026-03-31T14:02:48.152000+00:00] potuz: I want my validator client to act as a builder, what endpoint will it call?
[2026-03-31T14:03:19.016000+00:00] nflaig: https://github.com/ethereum/beacon-APIs/pull/580/changes#diff-70f7de196e41377e66d64da0bb5025d42f118937ee89bf86f8d2e78efa6610d0 should work for that case
[2026-03-31T14:03:30.931000+00:00] nflaig: so it is the same as for local build
[2026-03-31T14:04:00.206000+00:00] potuz: and none of this is merged yet right?
[2026-03-31T14:04:53.603000+00:00] potuz: but no, this doesn't work, the beacon node does not need to cache an envelope for every validator it serves that decides to act as a builder
[2026-03-31T14:05:12.633000+00:00] potuz: it needs to cache just the payload and the state transition function, but the builder index etc needs to be passed to the endpoint
[2026-03-31T14:05:49.197000+00:00] potuz: so is there no working version now? I'll work on the internal grpc version for PRysm then that I can do whatever I want
[2026-03-31T14:06:43.595000+00:00] nflaig: looks like we removed it here https://github.com/ethereum/beacon-APIs/pull/580/changes/0c458594da02947f60bf6c7f4cae876ed2cdb1f8
[2026-03-31T14:07:52.305000+00:00] nflaig: problem is nobody actually has gotten there yet to implement these endpoints, and before that's the case there is also no point merging these PRs
[2026-03-31T14:09:52.454000+00:00] potuz: I'm completely lost with that repo, I'm implementing the whole pipeline now for Prysm of all these endpoints, but the problem is that it looks that our internal implementation (grpc) will be very different than the beacon API (that as far as I can tell is not completely worked out to allow validator clients to act as builders). We were hoping to deprecate the grpc implementation, but it seems harder when we deviate from the beacon API
[2026-03-31T14:12:59.807000+00:00] potuz: ok, but actually it's fine not to have the builder index in that call as long as the beacon node has the beacon block already with the committed signed bid
[2026-03-31T14:13:29.614000+00:00] potuz: if that endpoint assumes the beacon has cached the envelope for the external builder or local proposer that is already committed in the bid, that endpoint as is will work fine
[2026-03-31T14:15:01.729000+00:00] potuz: ahh no, that thing is in `produceBlock` not in the getPayloadEnvelope
[2026-03-31T14:15:22.129000+00:00] potuz: I'll stick to grpc
[2026-03-31T14:17:59.456000+00:00] nflaig: should be the same args either way that you need to pass, but I don't think anyone looked at implementing the "act as a builder" flow, I don't think I want to couple that with the validators client in lodestar and rather have a separate process that just uses some sse events, and might use the beaocn node to build the payload, just not sure yet how that flow will look like but we should make the standard beacon-api in a way that allows this to be easy to implement
[2026-03-31T14:23:48.764000+00:00] potuz: yeah perhaps it's that I had a very clear picture of what this beacon API would be, and assumed that this was the current status of that API:
The builder namespace requires two endpoints
- Get a bid to sign right now:  The beacon is in charge of caching a payload, the fee recipient, etc. The endpoint gets the builder index as a parameter. 
- Get an envelope to sign right now: the beacon is in charge of using the cached payload or alternatively the endpoint takes an entire new payload and requests. It's fine if these are different endpoints, one that uses the cched payload and another one that gets all the data.  In the cached senario, if the current block has the commitment to the cached hash, return the corresponding full payload envelope. 
The beacon needs two endpoints to just broadcast the two signed objects.
[2026-03-31T14:29:04.672000+00:00] nflaig: yeah I think it's good to have separate endpoint under `/builder` what's currently not defined is the case where the beacon node caches the payload, we only have #584 which supplies the payload

for broadcast these should be `/beacon` endpoints anyway like we have `publishBlockV2` today so I don't think we need dedicated endpoints

what doesn't work for local payloads right now is telling the EL what gas limit to use
[2026-03-31T14:30:50.694000+00:00] nflaig: `POST /eth/v1/beacon/execution_payload_bid` already part of master to broadcast the bid
[2026-03-31T14:32:04.199000+00:00] nflaig: `POST /eth/v1/beacon/execution_payload_envelope` is part of https://github.com/ethereum/beacon-APIs/pull/580/
[2026-03-31T15:14:56.022000+00:00] potuz: https://github.com/ethereum/beacon-APIs/blob/master/apis/validator/execution_payload_bid.yaml
This endpoint does not allow the validator to tweak the values  and `execution_payment` part of the bid, so I doubt any external builder will ever use this endpoint. I'm fine by it cause our internal implementation will make the beacon set these values, but just flagging this that it sounds to me this endpoint will be mostly useless
[2026-03-31T15:16:55.182000+00:00] nflaig: cc <@1386598056320831509> I think we should move that to the builder namespace and make it useful for builders (and CLs that wanna act as a builder)
[2026-03-31T18:00:26.300000+00:00] bharath.vedartham: i doubt an external builder will use that endpoint tbh. An external builder would likely create the bid themselves, sign it, then use an endpoint to create the envelope, sign the envelope and then use an endpoint to publish the envelope to the n/w
```

</details>
