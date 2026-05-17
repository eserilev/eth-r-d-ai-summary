# EPBS Builder API Implementation and Testing

**Channel:** epbs | **Date:** 2026-03-26

## Summary

Terence inquired about the availability of a preliminary builder API for EPBS (Enshrined Proposer-Builder Separation) implementation and testing. Bharath directed him to the latest specification in PR #138 of the ethereum/builder-specs repository, noting that request authentication remains the main point of contention. The high-level builder API workflow involves querying bids, putting bids in blocks, sending blocks back to builders, and having builders broadcast envelopes, along with a max_trusted_bid broadcast mechanism for setting trust levels.

Currently no builder supports the new API, though Bharath is working on adding support to the buildoor project. When asked about bid signing requirements, Bharath confirmed it's currently defined as mandatory in the specification but acknowledged that making it optional would be reasonable. Terence committed to reviewing the specification, indicating active collaboration on finalizing the API design.

## Participants

- bharath.vedartham
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
