# ePBS API updates for chain reorg and builder endpoints

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion focused on two API updates needed for ePBS (enshrined Proposer-Builder Separation). First, bharath.vedartham suggested updating the chain_reorg SSE (Server-Sent Events) event to include both old and new head block parent hashes, since ePBS can have payload-only reorgs while maintaining the same beacon block. Second, they proposed introducing `getStateBuilder` APIs similar to the existing `getStateValidator` endpoints for builder-related state queries.

nflaig agreed with the builder API proposal but recommended returning the spec object directly rather than using a custom object format like the current validator API does. Both suggestions appear to have initial support but would need further implementation details and broader consensus from the development team.

## Participants

- bharath.vedartham
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T10:36:37.864000+00:00] bharath.vedartham: https://github.com/ethereum/beacon-APIs/blob/master/apis/eventstream/index.yaml#L103 should we update the chain_reorg SSE event for ePBS to include the old head block parent hash and new head block parent hash too? because we can have a reorg of just the payload but have the same beacon block?
[2026-03-06T10:40:06.642000+00:00] bharath.vedartham: and should we introduce apis like `getStateBuilder` similar to `getStateValidator` here?: https://github.com/ethereum/beacon-APIs/blob/master/apis/beacon/states/validator.yaml#L2
[2026-03-06T10:41:37.662000+00:00] nflaig: sure why not but we should return the spec object, not a custom object like we validator api does
```

</details>
