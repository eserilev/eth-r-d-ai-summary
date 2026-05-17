# Beacon API ExecutionPayloadEnvelope Generation

**Channel:** epbs | **Date:** 2026-02-05

## Summary

bharath.vedartham identified a need for a new Beacon API endpoint that would allow external builders to generate an ExecutionPayloadEnvelope from component parts including the payload, blobs bundle, execution requests, withdrawals, and beacon block with bid. This was raised as feedback on an existing beacon-APIs pull request that already had extensive discussion (128 comments).

During the discussion, bharath questioned whether the blobs bundle parameter is actually necessary since KZG commitments don't need to be included in the envelope. They also raised implementation questions about whether this should be a separate API versus optional parameters on an existing endpoint, and noted a naming inconsistency - the operation is called `getSignedExecutionPayloadEnvelope` but the beacon API can likely only generate an unsigned `ExecutionPayloadEnvelope` that would require validator client signing.

To avoid further expanding the scope of the already lengthy original PR discussion, nflaig created a separate GitHub issue (#576) to address this beacon API requirement independently.

## Participants

- bharath.vedartham
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-05T13:42:28.376000+00:00] bharath.vedartham: referring to this: https://discord.com/channels/595666850260713488/692078615269212180/1466085824268468357
I think we will require a beacon-API to generate the ExecutionPayloadEnvelope given the payload, blobs bundle, execution requests, withdrawals and beacon block with the bid, so that external builders can create the envelope
raised it here https://github.com/ethereum/beacon-APIs/pull/552/changes#r2769231258
[2026-02-05T13:43:27.993000+00:00] bharath.vedartham: maybe we don't have to send the blobs bundle because we don't need to include the kzg commitments in the envelope?
[2026-02-05T13:45:36.814000+00:00] bharath.vedartham: I wonder if we should have a separate API for this or have the same API but with the payload etc being optional params. Also the operation is called `getSignedExecutionPayloadEnvelope` but can the beacon-api generate a `SignedExecutionPayloadEnvelope`. I think it can only create an `ExecutionPayloadEnvelope` and the VC has to sign it
[2026-02-05T15:29:20.176000+00:00] nflaig: created an [issue](https://github.com/ethereum/beacon-APIs/issues/576) to we can tackle it separately, the original PR takes forever to load at this point because there are 128 comments so we should not increase the scope 😅
```

</details>
