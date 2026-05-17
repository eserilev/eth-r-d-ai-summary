# EPBS Client Testing and Integration Issues

**Channel:** epbs | **Date:** 2026-02-19

## Summary

pk910 reported testing the first EPBS-ready client combination (Lighthouse + Geth) and encountered several integration issues. The main problems included a database error when requesting older finalized state with parent block hash mismatches, extremely short block building times (5ms) causing Geth to return empty payloads because Lighthouse was requesting blocks too quickly, and missing PTC (Payload Timeliness Committee) aggregates in blocks. Additionally, Lighthouse stopped emitting `payload_attributes` events after GLOAS activation, which affects builder testing.

pk910 successfully fixed the Dora explorer integration issues and provided a working Kurtosis configuration for testing the EPBS devnet. The testing setup uses 3 nodes running Geth (epbs-devnet-0) as execution clients and Lighthouse (epbs-devnet-0) as consensus clients with GLOAS fork activation at epoch 1.

0xunclebill acknowledged the issues and confirmed that PTC duties are intentionally excluded from the current devnet-0 branch, being developed in a separate PR. The team plans to focus on resolving critical devnet-0 issues first before implementing PTC functionality, with fixes to be pushed soon.

## Participants

- 0xunclebill
- pk910

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-19T09:03:29.867000+00:00] pk910: congratulations on being the first epbs ready client 😄
yea, dora doesn't look happy at all - tbh not super surprising. 
I'm looking into it 🙂
[2026-02-19T11:42:55.988000+00:00] pk910: <@358120958726373381> I'm getting this error when requesting a older state (from a finalized gloas block):
`{\"code\":500,\"message\":\"UNHANDLED_ERROR: DBError(LoadingHotStateError(\\\"get state\\\", 0x1f4dea650b6e38cfe0bea431fa978c9643ec88ca3c6521444fdecff833c05383, BlockReplayError(BlockProcessing(ExecutionPayloadBidInvalid { reason: ParentBlockHashMismatch { state_block_hash: 0x616de917c182c8d648ce460f88d25b1b9335d45d2b95ab70261c9968d31ad52c, bid_parent_hash: 0x58ded490393a053c1b9388bb5d30de0845ebc5500d902fe5aeae562515589bd8 } }))))\",\"stacktraces\":[]}`

and I've noticed there are only empty blocks after gloas activation.
lighthouse seems to request blocks too fast, so geth can't build a proper payload and it returns an empty payload:
```
INFO [02-19|12:21:56.002] Starting work on payload                 id=0x03e2890a1ce59701
INFO [02-19|12:21:56.008] Stopping work on payload                 id=0x03e2890a1ce59701 reason=delivery
```
^geth got 5ms to build a block

does the lighthouse validator do ptc votes? There are no ptc aggregates included in blocks.

and for early builder testing (very low prio):
It looks like lighthouse doesn't emit `payload_attributes` events anymore post gloas activation.
[2026-02-19T12:01:07.008000+00:00] pk910: I've fixed dora, works well with lighthouse-geth now 🙂

kurtosis config:
```yaml
participants:
  - el_type: geth
    el_image: ethpandaops/geth:epbs-devnet-0
    cl_type: lighthouse
    cl_image: ethpandaops/lighthouse:epbs-devnet-0
    count: 3
network_params:
  gloas_fork_epoch: 1

additional_services:
  - dora
```
[2026-02-19T19:28:47.912000+00:00] 0xunclebill: Thanks for testing! Going to push up a few fixes soon

We don’t have PTC duties included in our devnet-0 branch. It’s in a separate PR at the moment. Going to focus on polishing devnet-0 critical stuff first and then circle back to PTC
```

</details>
