# Engine API PR and fee recipient verification

**Channel:** epbs | **Date:** 2026-04-22

## Summary

m.kalinin shared a PR for the Engine API as an alternative to a previous proposal with reorg depth limitations. The discussion then focused on why the `verify_execution_payload_envelope` function doesn't verify that the `fee_recipient` in the bid matches the one in the payload.

potuz clarified that the payload is treated as a black box where the `fee_recipient` field has different semantics in the bid versus the payload. In the payload, `fee_recipient` retains pre-Gloas semantics (the coinbase field), while in the bid it represents where the on-chain mechanism pays via withdrawal. Trusted builders maintain current practices by either adjusting the coinbase or sending a transaction to the fee recipient, with off-protocol payments also supported.

The conversation revealed that builders typically keep the payload's fee recipient (coinbase) as their own address and make payments to the actual proposer through the last transaction in the block, worth the bid value. While m.kalinin suggested using different field names to reduce confusion, potuz argued that maintaining the same naming supports the dual payment mechanisms and avoids disrupting existing tooling.

## Participants

- barnabasbusa
- bharath.vedartham
- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
