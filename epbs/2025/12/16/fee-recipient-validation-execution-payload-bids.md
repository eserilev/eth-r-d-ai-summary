# Fee recipient validation in execution payload bids

**Channel:** epbs | **Date:** 2025-12-16

## Summary

The discussion centers on fee recipient validation in execution payload bids within the Gloas specification. bharath.vedartham raised concerns about potential builder spoofing, where builders could set arbitrary fee recipients to capture MEV rewards instead of directing them to the proposer's preferred address. Currently, relays perform this validation, but the consensus specs lack explicit checks during bid processing or P2P gossip validation.

The conversation reveals that current validation logic differs between trusted and trustless payment models. With trustless payments, the fee recipient should match the proposer's address and can be validated at the consensus layer. However, with trusted payments (where builders pay proposers off-chain), the fee recipient is typically the builder's address, making validation more complex. nflaig noted that the current spec assertion `header.fee_recipient == fee_recipient` would fail since the coinbase address isn't used for MEV rewards today. There was discussion of post-execution validation on the EL side to verify payments occurred, though this approach has limitations with off-chain settlements and unrelated transfers.

The group reached tentative consensus that proposer addresses should always be set as the fee_recipient in Gloas bids, regardless of payment method. However, open questions remain about the practical value of fee_recipient fields in pure trusted payment scenarios and how to handle edge cases like off-chain settlements between validators and proposers.

## Participants

- bharath.vedartham
- l03443
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-16T11:20:25.908000+00:00] bharath.vedartham: hey folks, i have a question:
in `process_execution_payload_bid` , we don't validate whether the fee recipient the proposer prefers to receive their rewards: https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/beacon-chain.md?plain=1#L946
i m thinking if there is a chance that a builder can spoof the fee recipient to be an address they own to get all the mev rewards? 
today relays check if the builder is submitting a bid to the correct fee recipient or not. 
i don't see such checks while we process the execution payload bid or when we receive the bid from the p2p gossip topic? i assume this should be easy to check when directly querying the builder for the bid from the builder-api.
[2025-12-16T11:21:01.122000+00:00] bharath.vedartham: could the builder put any fee recipient they desire and get away with it?
[2025-12-16T12:00:48.605000+00:00] bharath.vedartham: I believe The proposer should be checking if the bid.fee_recipient matches the one they expect while conducting the auction. This can’t be checked by consensus.
[2025-12-16T13:36:26.313000+00:00] nflaig: that's what we currently do for the gas limit (there is a [note in the builder spec](https://github.com/nflaig/builder-specs/blob/db7c5ee0363c6449997e2a1d5bc9481ed87ba223/specs/bellatrix/builder.md?plain=1#L285)), we cache gaslimit/fee recipient info, in gloas we would check fee recipieint as well (if trustless bid), same as the rule added to p2p if you receive bid there https://github.com/ethereum/consensus-specs/pull/4777
[2025-12-16T14:16:17.456000+00:00] bharath.vedartham: yeah, today we trust the relay to ensure that the fee recipient is correct. we have specs here in the builder-specs to validate the bid. I believe this logic can live in the consensus specs today?
[2025-12-16T14:32:44.639000+00:00] nflaig: the current spec seems wrong to me, it states
> `fee_recipient` corresponds to the validator's most recently registered fee recipient address
and then it asserts
```python
assert header.fee_recipient == fee_recipient
```
but this will always fail since `fee_recipient` is the coinbase address which is not used nowadays to pay MEV rewards to proposer

> I believe this logic can live in the consensus specs today?
we have `process_execution_payload_bid` but that doesn't cover the fee recipient or gas limit checks based on previous preference from the proposer, might be fine to keep the http validation/processing on the builder-spec
[2025-12-16T14:34:27.289000+00:00] bharath.vedartham: yeah you are right, i missed this. if we do trusted payments, the fee recipient today will be the builders fee recipient and there is no way to check whether the payment tx is being done to the proposer with the bid
[2025-12-16T14:35:17.744000+00:00] bharath.vedartham: ensuring that the fee recipient is right only matters when we do trustless payments i believe
[2025-12-16T14:35:49.079000+00:00] bharath.vedartham: with trustless payments, it should be the proposer's fee recipient
[2025-12-16T14:37:46.042000+00:00] nflaig: but I think you still want the `fee_recipient` to match the proposer fee recipient with trusted payments, I believe there was an idea already to verify the payment on the EL side if `execution_payment` is used, you still lose the payment for that block since you can only do that after the payload is revealed/processed but the builder could be blocked
[2025-12-16T14:42:11.220000+00:00] bharath.vedartham: do you have more context on the idea to verify the payment on the EL side? how could we verfy the whether the payment is happening based on the jsut the bid?
[2025-12-16T14:43:09.253000+00:00] nflaig: you can't do it with just the bid, but after processing the execution payload you could block the builder if the payment didn't happen
[2025-12-16T14:43:50.327000+00:00] bharath.vedartham: yes that makes sense. but i think the problem is that, we assume that `execution_payment` means the payment will happen via the last tx. what if there is an off-chain settlement b/w a validator and a proposer here?
[2025-12-16T14:45:40.451000+00:00] l03443: could also be that a proposer has an incoming transfer in the same block for unrelated reasons. similar issue was discussed here: https://github.com/flashbots/mev-boost/issues/708
[2025-12-16T14:49:10.628000+00:00] bharath.vedartham: will read this! thanks for pointing it out!
[2025-12-16T14:54:06.208000+00:00] nflaig: coming back to that, I think it still makes sense with gloas to always set the proposer address as `fee_recipient` in the bid, it's just different today because it's the execution payload header where it needs to match the coinbase address
[2025-12-16T14:59:37.109000+00:00] bharath.vedartham: yeah that makes sense. i wonder if there is any value setting the `fee_recipient` in the bid if it is a pure trusted payment
```

</details>
