# Devnet testing and implementation updates

**Channel:** epbs | **Date:** 2026-04-16

## Summary

The discussion centers around implementation and testing of the BAL (Balance and Slot) precompile for the Glamsterdem devnet. Terence from Prysm initially sought clarification on whether the Amsterdam engine API specification serves as the source of truth for glamsterdem devnet 0, then requested guidance on testing their BAL and slot precompile implementation using Kurtosis with the appropriate Geth image.

The team provided the necessary testing resources, specifically pointing to the `ethpandaops/geth:bal-devnet-3` image and related documentation. A significant architectural change was discussed regarding the removal of the `slot` field from `ExecutionPayloadEnvelope` since the `ExecutionPayload` now contains the `slot_number` field directly - a consensus-breaking change that was deemed timely to implement now.

Terence successfully completed testing of Prysm's implementation with the Amsterdam engine API changes, confirming that finalization and syncing work properly with the specified Geth image. The next steps involve waiting for spec tests and releases to ensure compatibility. Meanwhile, jtraglia initiated test generation for the EIP-7843 branch (including EIP-5094) and has an active spec PR under review.

## Participants

- jtraglia
- qu0b
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-16T18:09:23.977000+00:00] terencechain: may i assume https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md
is the source of truth now for glamsterdem devnet 0?
[2026-04-16T18:28:38.524000+00:00] terencechain: i implemented bal and slot pre compile for prysm, i want to test it on kurtosis, which EL/geth image can i use? 
even better a kurtosis config example that worked for bal devnet will be great
[2026-04-16T18:54:26.975000+00:00] jtraglia: cc <@&1460290804898861117>
[2026-04-16T18:54:59.876000+00:00] qu0b: <https://notes.ethereum.org/@ethpandaops/bal-devnet-3>
[2026-04-16T18:57:50.358000+00:00] jtraglia: Thanks! Specifically: https://notes.ethereum.org/@ethpandaops/bal-devnet-3#Local-testing
[2026-04-16T18:58:05.120000+00:00] jtraglia: `ethpandaops/geth:bal-devnet-3`
[2026-04-16T19:41:48.870000+00:00] jtraglia: I've made some more changes to the eip7843 PR. Now that `ExecutionPayload` contains the `slot_number` field, we can remove the `slot` field from `ExecutionPayloadEnvelope`. Obviously a consensus breaking change, but now is the time to make the change.
[2026-04-16T19:57:11.624000+00:00] terencechain: we can remove `slot` from the `ExecutionPayloadEnvelope` right?
[2026-04-16T20:21:08.997000+00:00] jtraglia: Yeah I think so. That's what I did.
[2026-04-16T22:10:19.175000+00:00] terencechain: quick update: prysm implemented bal, slot in payload changes with amsterdam engine api and can finalize kurtosis with `ethpandaops/geth:bal-devnet-3`. I tested syncing as well 
next step is to wait for spec test and a release to make sure we can pass those
[2026-04-16T22:48:16.636000+00:00] jtraglia: Nice. I just kicked off the tests action for my eip7843 branch here:
* https://github.com/jtraglia/consensus-specs/actions/runs/24537943344
[2026-04-16T22:48:53.483000+00:00] jtraglia: Just the minimal tests. Will take ~1 hour to generate. This includes 5094 too.
[2026-04-16T22:50:24.618000+00:00] jtraglia: And this is the spec PR: https://github.com/ethereum/consensus-specs/pull/4840
```

</details>
