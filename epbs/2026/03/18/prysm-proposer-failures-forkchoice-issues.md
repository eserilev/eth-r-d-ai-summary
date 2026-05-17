# Prysm Proposer Failures and Forkchoice Issues

**Channel:** epbs | **Date:** 2026-03-18

## Summary

Prysm proposers experienced failures due to "invalid forkchoice state" errors when communicating with Geth execution clients. The issue manifested as Prysm being unable to prepare payloads for block building, with Geth rejecting forkchoice updates because the "final block not in canonical chain." Terencechain identified this as the main cause of Prysm's periodic missed blocks, noting that other clients don't seem to have this issue and may have implemented the logic correctly.

Potuz clarified the root cause: the finalized hash passed to the engine API should always be the *parent hash* of the block with the finalized root, not the finalized block's hash itself. This applies to both finalized and safe hashes, and will also apply to the fast confirmation rule. The issue stems from Prysm only finalizing the pending node, meaning only its parent hash is guaranteed to be final in the execution layer.

The discussion concludes that this represents an implementation bug in Prysm's forkchoice handling, with an indication that the engine API specification may need clarification if the hash requirements aren't currently properly specified.

## Participants

- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-18T03:40:51.923000+00:00] terencechain: heads up, all the prysm proposers are down with the following error, im looking into it, and cc <@755590043632140352> so he is aware when he wakes up
```
[2026-03-18 03:39:47.06]  INFO rpc/validator: Begin building block sinceSlotStartTime=61.754201ms slot=97539
[2026-03-18 03:39:47.11] DEBUG rpc/validator: Payload ID cache miss headRoot=0x72b7070b740065986645194a575141e52afc4bf89b86935f03213d4f4b4e5659 slot=97539 validatorIndex=794
[2026-03-18 03:39:47.11] ERROR rpc/validator: Finished building block error=rpc error: code = Internal desc = Could not get local payload: could not prepare payload: invalid forkchoice state sinceSlotStartTime=116.9942ms slot=97539 validator=794
```
[2026-03-18T03:42:58.138000+00:00] terencechain: Geth complains with
```
WARN [03-18|03:42:12.665] Final block not in canonical chain       number=88687 hash=3a3274..f267ce
WARN [03-18|03:42:12.665] Served engine_forkchoiceUpdatedV3        conn=[2a01:4f8:1c19:38ee::8]:59638 reqid=30488 duration=1.310008ms err="Invalid forkchoice state" errdata="{\"err\":\"final block not in canonical chain\"}"
```
[2026-03-18T20:44:49.123000+00:00] potuz: I'll leave this here cause it's causing confusion even internally. The finalized hash that is passed to the engine should always be the *parent hash* of the block with finalized root. While it's technically possible to track if validators where using full or empty to vote for target, we're not doing it. We only finalize the pending node, so it's parent hash is the only one with a guarantee to be final
[2026-03-18T20:46:05.389000+00:00] potuz: Same thing applies to the safe hash and the same thing will happen to the fast confirmation rule
[2026-03-18T20:47:10.517000+00:00] potuz: This is independent of missing slot zero or whatever, it's always parent hash of the CL block with the given root
[2026-03-18T21:08:13.249000+00:00] potuz: BTW <@425572898787426305> this should be changed if the hashes are currently specified in the engine API
[2026-03-18T22:01:25.160000+00:00] terencechain: This is the main reason Prysm missed blocks periodically, we returned an incorrect block hash that Geth didn't agree with (i.e. "invalid forkchoice state"). So far it doesn't seem like other clients have the same issue, so they may have implemented it correctly.
```

</details>
