# Dora Integration with Gloas Devnet-0 Setup

**Channel:** epbs | **Date:** 2026-02-13

## Summary

The discussion focused on integrating Dora (an Ethereum block explorer) with the Gloas devnet-0 setup. Stefan asked whether specific Beacon API endpoints, particularly `/eth/v1/beacon/execution_payload_envelope/{block_id}` and certain event streams, needed to be implemented for devnet-0. pk910 clarified that Dora subscribes to `execution_payload_available` and `execution_payload_bid` events and calls the execution payload envelope API by block root.

Stefan confirmed that the necessary Beacon API changes have already been merged for Gloas, resolving the technical requirements. There was brief confusion about whether consensus-specs changes needed to be merged, but tersec clarified this concern was about the consensus specifications rather than Dora itself, and noted that recent refactoring had removed/changed some proposal-related components.

The team confirmed they had previously solved similar integration issues during their interop work, and barnabasbusa noted that while the Beacon API changes are merged, Gloas features won't be integrated into Dora's main branch until much later in development.

## Participants

- barnabasbusa
- pk910
- potuz
- stefanbratanov
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-13T12:33:25.786000+00:00] stefanbratanov: <@412614104222531604> for Dora to run in Gloas, do you reckon we should at least have `/eth/v1/beacon/execution_payload_envelope/{block_id}` beacon API or some of the event streams (https://github.com/ethereum/beacon-APIs/blob/e25942758161fe82009a20f2d3b1868e0ff611d8/apis/eventstream/index.yaml#L42) implemented for devnet-0?
[2026-02-13T12:34:02.551000+00:00] barnabasbusa: <@808969530608451584>
[2026-02-13T12:37:54.058000+00:00] tersec: if so please have these merged, etc as much as possible, the collection-of-random-unmerged-PRs thing isn't really great
[2026-02-13T12:39:32.601000+00:00] tersec: like, if it's decided enough for devnet-0, it's decided enough to merge
[2026-02-13T12:39:35.598000+00:00] potuz: I feel we had solved this already for our interop didn't we <@641548687813902336> ?
[2026-02-13T12:39:37.548000+00:00] pk910: Dora subscribes to the `execution_payload_available` & `execution_payload_bid` events, and calls the `/eth/v1/beacon/execution_payload_envelope/{block_id}` api (by block root)
[2026-02-13T12:40:19.614000+00:00] stefanbratanov: I think event stream was slightly different, have to check the code, it changed quite a bit since then
[2026-02-13T12:40:46.333000+00:00] stefanbratanov: The Beacon API changes are merged for Gloas
[2026-02-13T12:40:59.898000+00:00] tersec: ok
[2026-02-13T12:41:27.231000+00:00] tersec: oh, because the proposal parts were removed/refactored out the last couple days
[2026-02-13T12:41:27.964000+00:00] tersec: right
[2026-02-13T12:50:00.862000+00:00] barnabasbusa: are you talking about dora?
[2026-02-13T12:50:13.172000+00:00] barnabasbusa: we won't be merging gloas features into dora's main branch till a lot later
[2026-02-13T12:50:19.502000+00:00] tersec: no, I mean consensus-specs
[2026-02-13T12:50:22.344000+00:00] barnabasbusa: ah right
[2026-02-13T12:50:35.455000+00:00] tersec: but it's resolved anyway, see above
```

</details>
