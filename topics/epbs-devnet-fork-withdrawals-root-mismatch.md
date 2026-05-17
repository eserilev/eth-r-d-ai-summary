# EPBS Devnet Network Fork and Withdrawals Root Mismatch

---

## 2026-03-21 (epbs)

A significant network fork occurred on epbs-devnet-0 around epoch 3742 when Lido deposited validators with balances >32 ETH, triggering withdrawals. Lighthouse and Prysm clients began failing to propose blocks due to a `WithdrawalsRootMismatch` error, while Lodestar continued operating normally and proposed full blocks with withdrawals. This created a chain split where Lighthouse and Prysm agreed on a separate fork with no withdrawals between epochs 3742-3758, only resuming block proposals when validator balances dropped and withdrawals stopped.

The root cause was identified as improper handling of the `withdrawals` field in payload attributes when the parent block is empty. Both Prysm and Lighthouse teams confirmed they were not correctly setting `payload_expected_withdrawals` from state in this scenario. The issue is distinct from previous local devnet testing because this case involved validators that started with 0x01 credentials pre-fork and became active post-fork.

Prysm's team quickly identified and fixed the bug with PR #16566, and it was noted that this issue would likely be caught by upcoming consensus spec tests in PR #4962. The network recovery plan was to be coordinated after confirming withdrawal support status across all client implementations.

**Participants:** 0xunclebill, barnabasbusa, nflaig, pk910, terencechain

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-07 (epbs)

pk910 reported a complete outage of epbs-devnet-1, with no new blocks containing payloads produced in the last 12 hours, only empty blocks. The devnet had been running on a single Prysm node (prysm-geth-3) plus bootnode after all other nodes fell out of sync following "gloas activation," but both remaining nodes are now experiencing "context deadline exceeded" errors without clear block production error messages.

terencechain suggested considering a fresh restart or launching devnet2 with a better-coordinated genesis date when more clients are ready. He noted that devnet1's timing during EthCC and the holidays made it difficult to provide adequate attention and debugging resources, implying the current issues may be related to insufficient client readiness and coordination during the initial launch.

No concrete decisions were made in this brief exchange, leaving open questions about whether to attempt recovery of the current devnet or proceed with a new deployment under better conditions.

**Participants:** pk910, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-07T16:59:59.057000+00:00] pk910: Heya,
It looks like there's a full outage of epbs-devnet-1 now.
No new block with payload since 12h - only some empty ones.

I'm not fully up to date on what was going on before,  but I think the last status was that there is one prysm node (prysm-geth-3) + bootnode running the chain as all other nodes fall off sync right after gloas activation.
Both nodes are now throwing context deadline exceeded errors,  but nothing obvious bad like a block production error message :/
[2026-04-07T17:04:22.890000+00:00] terencechain: Does it make sense to restart from scratch, or even do a devnet2 with a more agreed-on genesis date when majority of clients are ready? Devnet1 happened during EthCC and then the holidays, so it was pretty hard to give it full attention, let alone spend time debugging
```

</details>

