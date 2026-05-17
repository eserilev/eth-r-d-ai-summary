# PR Reviews and Engine API Updates

---

## 2026-04-17 (epbs)

The discussion centers around PR reviews and updates to Engine API specifications. Nero_eth reviewed and approved a PR, then created a follow-up PR updating `engine_getPayloadV6` in the builder specs. There was technical clarification about hash comparison implementation, where Potuz noted that clients need to compare hashes between the BAL (Block Abstraction Layer) included in the payload and the virtually computed one. Nero_eth confirmed this only affects execution layer (EL) clients, as the EL maintains its own block header with the bal_hash for validation.

Barnabasbusa listed several Engine API methods being updated: `engine_newPayloadV5`, `engine_getPayloadV6`, `engine_getPayloadBodiesByHashV2`, `engine_getPayloadBodiesByRangeV2`, `engine_forkchoiceUpdatedV4`, and `PayloadAttributesV4`. Multiple PRs were referenced for potential inclusion, with Potuz characterizing two of his PRs as minor readability improvements that could be merged or closed without issue, but emphasizing that PR #770 should be merged as soon as possible.

**Participants:** barnabasbusa, jtraglia, nero_eth, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-17T13:02:51.263000+00:00] nero_eth: Reviewed your PR, <@755590043632140352> and lgtm, thanks!
I had one small follow-up PR, updating engine_getPayloadV6 in the builder specs.
cc <@592004585506340885>
[2026-04-17T13:04:28.003000+00:00] potuz: I mentioned it because you say that clients will have to compare against an expected hash but I guess this now should be compare the hashes of both the BAL included in the Payload and the virtual computed one
[2026-04-17T13:10:47.607000+00:00] nero_eth: Yeah the EL has it's own block header with the bal_hash inside. That's the one used for comparing and checking the BAL is correct.
So, this only concerns EL clients.
[2026-04-17T13:26:50.509000+00:00] jtraglia: Thanks Toni! The PR: https://github.com/ethereum/consensus-specs/pull/5123
[2026-04-17T13:30:31.915000+00:00] barnabasbusa: `engine_newPayloadV5`
`engine_getPayloadV6`
`engine_getPayloadBodiesByHashV2`
`engine_getPayloadBodiesByRangeV2`
`engine_forkchoiceUpdatedV4`
`PayloadAttributesV4`
[2026-04-17T13:59:52.159000+00:00] barnabasbusa: https://github.com/ethereum/consensus-specs/pull/4892  & https://github.com/ethereum/consensus-specs/pull/4898 <@755590043632140352> we want these in?
[2026-04-17T14:03:04.725000+00:00] barnabasbusa: https://github.com/ethereum/execution-apis/pull/770 also this should probably make it in too? cc <@360491619402776577> <@755590043632140352>
[2026-04-17T15:27:01.858000+00:00] potuz: Both my PRs are airdrop-farming nits, they are fine if merged and fine if they aren't. If no one finds them useful to the readability of the spec they can be closed, I wouldn't mind. 770 should indeed be merged ASAP
```

</details>

---

## 2026-04-22 (epbs)

The discussion centers around an Engine API PR and the semantics of `fee_recipient` fields in execution payloads versus builder bids. M.kalinin questioned why `verify_execution_payload_envelope` doesn't verify that the `fee_recipient` in the bid matches the one in the payload, which led to clarification that these fields have different meanings. The `fee_recipient` in the payload (coinbase) is where on-chain mechanism funds are paid via withdrawal, while the `fee_recipient` in the bid refers to where builders send money through off-protocol payments.

Potuz explained that trusted builders can use dual payment methods - either adjusting the coinbase directly or sending a transaction to the fee recipient while keeping the coinbase for themselves. This creates flexibility for both on-protocol and off-protocol payment mechanisms. The current approach maintains backward compatibility with existing tooling, though m.kalinin suggested using different field names to reduce confusion between the two semantics.

The discussion reveals that even today, builders commonly keep the payload's fee recipient (coinbase) as their own address and make payments to proposers through the last transaction in the block, representing the bid value. No firm conclusions were reached regarding field naming or verification requirements, with m.kalinin indicating they need more time to understand the implications.

**Participants:** barnabasbusa, bharath.vedartham, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-22T10:21:19.064000+00:00] m.kalinin: A PR to the Engine API: https://github.com/ethereum/execution-apis/pull/786
Alternative to the one with the reorg depth limitation in the spec, PTAL
[2026-04-22T10:33:15.425000+00:00] m.kalinin: btw, why `verify_execution_payload_envelope` doesn't do the `fee_recipient` check, i.e. the `fee_recipient` in the bid matches the one in the payload?
[2026-04-22T11:16:47.097000+00:00] barnabasbusa: <@&1460290804898861117> <@&1417820113981145228>
[2026-04-22T11:17:40.004000+00:00] potuz: The Payload is a black box and the fee recipient in it can (and will) be different than in the bid
[2026-04-22T11:18:20.336000+00:00] potuz: The fee recipient is where the funds of the on chain mechanism pays with a withdrawal, not where the builder sends money
[2026-04-22T11:18:21.769000+00:00] m.kalinin: why then the `fee_recipient` is in the bid?
[2026-04-22T11:18:43.880000+00:00] potuz: This
[2026-04-22T11:19:25.436000+00:00] m.kalinin: So, the semantics of  the `fee_recipient` in the payload and in the bid are different, right?
[2026-04-22T11:19:52.658000+00:00] potuz: Yes
[2026-04-22T11:20:33.984000+00:00] m.kalinin: The payload's `fee_recipient` inherits pre-Gloas semantics, while in the bid we have a new field. I think it would be better to put a different name to that field, otherwise, it's confusing
[2026-04-22T11:21:09.005000+00:00] potuz: But notice that trusted builders will continue using the same current semantics and either adjust Coinbase or send a tx on the EL to fee recipient, this is of no concern to the CL
[2026-04-22T11:22:08.760000+00:00] potuz: I don't think so because of this dual way of paying, we support off protocol payments. Changing all the tooling to have different objects to deal with will be more confusing
[2026-04-22T11:22:53.699000+00:00] m.kalinin: I see, it's a bit tricky, let me wrap my head around it
[2026-04-22T11:23:52.739000+00:00] potuz: Already today fee recipient can be two different things on blocks
[2026-04-22T11:30:43.341000+00:00] m.kalinin: I think today `execution_payload.fee_recipient` must match the `fee_recipient/coinbase/beneficiary` on the EL side, no?
[2026-04-22T11:52:59.241000+00:00] potuz: Not necessarily, sometimes the builder sends a tx to the fee recipient
[2026-04-22T11:53:10.874000+00:00] potuz: And keeps Coinbase to its own
[2026-04-22T16:01:34.029000+00:00] bharath.vedartham: yeah usually the builder keeps the fee recipieint of the payload i.e coinbase to his own address and the last tx is usually a payment to the proposer of the slot i.e the value of the bid
```

</details>

---

## 2026-04-28 (epbs)

Potuz requested review of consensus specs PR 5183, noting that it differs from changes made to the associated EIP and asking for agreement to align the EIP accordingly. The discussion involved coordination between the PR and EIP modifications.

nc1234 questioned whether "payload by range reqresp" should also be removed as part of these changes. However, potuz indicated limited time availability for further EIP work, citing strong pushback from team members and mentioning that a breakout session was scheduled for the following day to make decisions on the direction forward.

The team decided to continue with devnet-0 development while awaiting the outcome of the planned breakout session, rather than investing more time in the EIP changes until the direction is clarified.

**Participants:** nc1234, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-28T11:26:12.436000+00:00] potuz: Can you guys please take a look at this? https://github.com/ethereum/consensus-specs/pull/5183
cc <@1126229231572094976> This is different than your changes in the EIP, tell me if you agree with these so that we fix the EIP
[2026-04-28T13:32:46.618000+00:00] nc1234: don't you want to remove payload by range reqresp too?
[2026-04-28T13:54:12.107000+00:00] potuz: I do, but this is as much time as I will give to this EIP for now, there was a strong push back from <@449019668296892420> and <@795439202732867594> and there will be a breakout tomorrow to decide, it's better not to waste time and continue with devnet-0 until then
```

</details>

