# EPBS Devnet Network Fork and Withdrawals Issue

**Channel:** epbs | **Date:** 2026-03-21

## Summary

A network fork occurred on epbs-devnet-0 around epoch 3742 when Lido deposited validators with balances >32 ETH, triggering withdrawals for the first time on the network. Lighthouse and Prysm clients began failing to propose blocks due to a `WithdrawalsRootMismatch` error, while Lodestar continued functioning correctly and proposed blocks with withdrawals. This caused the network to split, with Lighthouse and Prysm creating a separate chain that essentially ignored withdrawals between epochs 3742-3758 by producing empty blocks, then resuming normal operation once validator balances dropped below the withdrawal threshold.

The root cause was identified as improper handling of the `withdrawals` field in payload attributes when the parent block is empty - specifically, clients weren't properly setting `withdrawals` to use `payload_expected_withdrawals` from state. Both Prysm and likely other affected clients had this bug, which Lodestar had previously encountered and fixed. Terence from Prysm quickly identified the issue and created a fix, while the team noted this would likely cause failures in upcoming consensus spec tests for similar scenarios.

## Participants

- 0xunclebill
- barnabasbusa
- nflaig
- pk910
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-21T06:37:13.495000+00:00] pk910: it looks like something bad happened on epbs-devnet-0 about an hour ago (epch 3742+).
[2026-03-21T06:46:34.267000+00:00] pk910: oh yea,  I see what's going on:
Lido deposited some validaors, which have a balance of >32 ETH since epoch 3742.
so we have withdrawals since then,  and it looks like lighthouse & prysm can't propose blocks anymore:
```
Mar 21 06:40:47.047 ERROR Error whilst producing block                  error: "Some endpoints failed, num_failed: 2 http://beacon:5052/ => RequestFailed(Recoverable(\"Error from beacon node when producing block: ServerMessage(ErrorMessage { code: 400, message: \\\"BAD_REQUEST: failed to fetch a block: EnvelopeProcessingError(WithdrawalsRootMismatch { state: 0x792930bbd5baac43bcc798ee49aa8185ef76bb3b44ba62b91d86ae569e4bb535, payload: 0x380734ca4dda006f1b1a3f4f993780c05427a39749560d25819e6b465af59d5c })\\\", stacktraces: [] })\")), http://beacon:5052/ => RequestFailed(Recoverable(\"Error from beacon node when producing block: ServerMessage(ErrorMessage { code: 400, message: \\\"BAD_REQUEST: failed to fetch a block: EnvelopeProcessingError(WithdrawalsRootMismatch { state: 0x792930bbd5baac43bcc798ee49aa8185ef76bb3b44ba62b91d86ae569e4bb535, payload: 0x380734ca4dda006f1b1a3f4f993780c05427a39749560d25819e6b465af59d5c })\\\", stacktraces: [] })\"))", block_slot: Slot(120044), info: "block v3 proposal failed, this error may or may not result in a missed block"
```

strange, we've tested withdrawals in local devnets, worked fine there
[2026-03-21T07:48:48.477000+00:00] barnabasbusa: <@586161934425128960> looks like lodestar is on a complete different fork than everyone else.

The only difference between now and what we tested is we started with 0x01 creds pre gloas and now they became active post gloas.
[2026-03-21T07:49:28.863000+00:00] barnabasbusa: <@363800010518822915> <@755590043632140352> <@586161934425128960> <@358120958726373381> can you pls confirm whether or not withdrawals are supposed to be working on your client ?
[2026-03-21T07:49:50.684000+00:00] barnabasbusa: then we can try to recover the network afterwards
[2026-03-21T07:50:43.042000+00:00] pk910: I think lodestar was the only working client in this case.
It proposed full blocks with withdrawals in epoch 3742+.
The other clients failed since then or produced empty blocks only
[2026-03-21T07:54:36.492000+00:00] pk910: lighthouse & prysm are on a separate chain because they basically agreed on that there are no withdrawals between epoch 3742 to 3758 - as there were no blocks with payloads.
since 3758 they're proposing blocks again too,  as the balances of Lido validators dropped again so there are no more withdrawals
[2026-03-21T10:49:43.152000+00:00] nflaig: it is this similar to the test you ran with lodestar earlier (before the devnet)? the bug fix was to properly set `withdrawals` in payload attributes to use `payload_expected_withdrawals` from state if parent block is empty
[2026-03-21T13:03:45.748000+00:00] terencechain: Very interesting. Got some logs so I can debug? We pass spec tests so let's find the test gap here
[2026-03-21T13:31:58.659000+00:00] pk910: VC throws:
`[2026-03-21 05:41:11.25] ERROR client: Failed to propose self-build envelope error=failed to get execution payload envelope for self-build: GetExecutionPayloadEnvelope: rpc error: code = Internal desc = could not compute post-payload state root: could not apply execution payload at slot 119746: payload withdrawals do not match expected withdrawals: could not connect pubkey=0xad5536ec9706`
[2026-03-21T13:34:20.863000+00:00] terencechain: Got it. I know where it is. I'll fix it today
[2026-03-21T14:06:34.852000+00:00] 0xunclebill: yeah i see the issue, we are in fact not properly setting the `withdrawal` field in payload attributes when the parent block is empty
[2026-03-21T14:13:48.394000+00:00] 0xunclebill: I assume our current impl would fail these tests once they're merged https://github.com/ethereum/consensus-specs/pull/4962
[2026-03-21T14:23:42.102000+00:00] terencechain: https://github.com/OffchainLabs/prysm/pull/16566
```

</details>
