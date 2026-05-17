# Gossip Event Purpose and Implementation Discussion

**Channel:** epbs | **Date:** 2026-02-15

## Summary

This discussion centers around whether to implement a gossip event for measuring envelope propagation time in Ethereum clients. The proposal aims to track how quickly execution payload envelopes spread across the network, similar to existing `block_gossip` events, which could be valuable for measuring the impact of block size increases and informing decisions about BALs (Beacon API Limits) and gas limit changes.

The conversation reveals some technical nuances: while there can be multiple envelopes, only one can be VALID, and the gossip event would specifically measure initial receipt time rather than full processing (which includes fork choice handling and `process_execution_payload` execution). However, concerns were raised about implementation overhead and "gold plating" - adding features that may not be needed by actual consumers like Contributoor and Dora monitoring tools.

The discussion concludes without a firm decision, with participants agreeing to first verify what monitoring tools actually need before proceeding with implementation. The key open question is whether existing payload receipt events are sufficient or if dedicated gossip timing events provide necessary additional value for network performance analysis.

## Participants

- .paulharris
- nflaig
- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-15T11:44:58.128000+00:00] .paulharris: Why gossip event?
[2026-02-15T12:16:27.972000+00:00] nflaig: to measure envelope propagation time, similar reasoning to `block_gossip`, see https://github.com/ethereum/beacon-APIs/issues/349
[2026-02-15T12:24:10.328000+00:00] tersec: there can be only one envelope though? so the reasoning there, essentially scaling feedback, doesn't apply
[2026-02-15T12:25:26.361000+00:00] nflaig: don't we wanna see how fast the envelope propagates to each node? eg. to measure impact on block size increase, seems relevant for BALs and gas limit increase, etc.
[2026-02-15T12:27:05.793000+00:00] nflaig: because `execution_payload` if we wanna mirror `block` event it would also include fork choice import and running `on_execution_payload` handler
[2026-02-15T18:47:41.475000+00:00] potuz: There can be multiple, take a look at the discussion above. Just one can be VALID though
[2026-02-15T20:46:01.500000+00:00] .paulharris: We should probably be careful about the number we're adding. if they're useful that's fine, but adding by default I'm not sure i'm keen on because we could add a ton that ultimately noone wants, and they dont come for free.
[2026-02-15T20:49:05.074000+00:00] nflaig: definitley not but something like `contributoor` would need those
[2026-02-15T20:49:31.587000+00:00] nflaig: do you mean free in terms of implementation effort or having it implemented and adding overhead? because in our case if nobody subscribes to an event it's 0 overhead
[2026-02-15T20:50:56.948000+00:00] .paulharris: the receipt it needs, not sure about when we gossip, that's very much up to what htey're trying to measure
if nobody ever subscribes to a given event, we have done an amount of implementation and testing for 0 need, in agile you'd call it 'gold plating' - noone wanted it, noone asked but we went ahead and stuffed about with it anyway...
[2026-02-15T20:55:28.995000+00:00] .paulharris: we can ask contributoor / dora ppl if they're happy just knowing payload receipt time for example <@84562395988508672> <@808969530608451584> fyi... and if gossip time also needed then fine, but lets check whats needed...
[2026-02-15T20:56:17.144000+00:00] nflaig: but what event is used for payload receipt time? the `_gossip` should be that
[2026-02-15T20:57:32.771000+00:00] nflaig: the normal event would call fork choice handler and run `process_execution_payload` which includes processing the payload
[2026-02-15T20:58:33.427000+00:00] .paulharris: which ever way its split out, all im suggesting is we check that its what the consumer will need before implementing cos it feels like we've already got kinda a lot to do
[2026-02-15T20:59:15.607000+00:00] nflaig: yeah that's why I raised it here, not even working on a spec PR before we know what we want/need
```

</details>
