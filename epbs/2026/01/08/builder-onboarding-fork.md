# Pending Deposits Processing Bug and Builder Onboarding

**Channel:** epbs | **Date:** 2026-01-08

## Summary

Potuz identified a bug in the current `process_pending_deposits` logic for 4788 where a validator making a deposit request for a 0x03 validator before the fork could result in both a builder and validator existing with the same key. This occurs when a deposit is made right before the fork (creating a pending deposit), followed by another deposit after the fork (immediately adding the pubkey as a builder), leading to duplicate entries when the pending deposit finally processes at finalization.

The proposed fix involves modifying `process_pending_deposits` to account for builders and changing the fork logic to immediately onboard pending deposits as builders rather than waiting for finalization. However, this raises concerns about the current policy of applying builder deposit requests immediately without waiting for finalization, since reorgs can change the pubkey cache order - the same reason validators wait for finalization before registry addition. Potuz created PR #4817 to address the issue with the simplest approach of onboarding pending deposits immediately as builders.

Fradamt noted that unlike validators (where consistent indexing across branches is mandatory for slashing processing), builders don't have the same strict requirements, though Potuz clarified the real implementation concern is the pubkey-to-index cache used in signature validation hot paths. The discussion leaves open whether the immediate builder deposit policy is acceptable given reorg risks at fork boundaries.

## Participants

- fradamt
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-08T11:38:56+00:00] potuz: <@592004585506340885>, copying <@981249983196041218> that has been raising that not having builders after the fork will be too much of an issue. I believe there is a bug in the current `process_pending_deposits` logic of  4788, so probably we are forced to change it anyway, Let me describe what I think is not nice before, and then how to fix it. 

If a validator makes a deposit request for a 0x03 validator before the fork. Then in [process_deposit_requests](https://jtraglia.github.io/eth-spec-viewer/#v1.7.0-alpha.0/functions-process_deposit_request-electra)  we will simply add the pending deposit for this 0x03 validator. If this happens right before the fork, then when the fork comes we we won't have added yet this validator to the registry, and this will be processed as a 0x03 validator later on [process_pending_deposits](https://jtraglia.github.io/eth-spec-viewer/#v1.7.0-alpha.0/functions-process_pending_deposits-electra) when the deposit finalizes. This is regardless of there being a 0x03 builder or not! the problem is that if this validator deposits as again right after the fork, and before the pending deposit has finalized, then the pubkey is added as a builder immediately! and later on when we process the pending deposit we have both a builder and a validator with the same key!

The fix to this issue should be to change `process_pending_deposits` to also account for builders. This was omitted because we apply deposits immediately for builders, but I think what I described above breaks that assumption.  The good thing about this is that if we do so, then any deposit right before the fork will be applied immediately after the first block as a builder (instead of breaking on finalization we will have to loop looking for 0x03 deposits and apply them immediately). 

Having said so, I think we need to have a discussion about this policy of applying deposits requests for builders immediately. I think this is not acceptable
[2026-01-08T11:41:38.480000+00:00] potuz: The reason is the same as for validators, a reorg can change the order of the pubkey cache, this is the reason why we wait for finalization before adding to the registry. If we do the fix as I say above, then we can onboard builders right at the fork **and** at the same time we can not take any new deposits requests for builders until they are finalized. The problem with this approach is that any reorg at the fork boundary will be a problem. Perhaps my fix above is already enough and we do not need to worry about reorgs changing the pubkey cache. I am not an expert on this topic but I remember this was pretty heated in Kenya. At any rate I think the situation I describe above is a bug. Just woke up late so may be all wrong, I apologize, will have coffee and reread what I wrote in a bit
[2026-01-08T11:43:37.508000+00:00] potuz: The simplest way of dealing with the pending deposits is to change `fork.md` to onboard them immediately as builders and then not deal with pending deposits for builders. That is, if we are happy about depositing builders immediately without finalization (which is not obvious to me)
[2026-01-08T11:44:56.629000+00:00] potuz: I can write a PR for this latest thing
[2026-01-08T14:48:29.919000+00:00] potuz: https://github.com/ethereum/consensus-specs/pull/4817
[2026-01-08T15:53:34.501000+00:00] fradamt: > a reorg can change the order of the pubkey cache, this is the reason why we wait for finalization before adding to the registry.

For validators, it is mandatory that every branch has to have the same index in order to be able to process slashings. This is not the case for builders. It might be convenient for implementations and worth having as a guarantee, but it's not the same problem
[2026-01-08T16:12:52.381000+00:00] potuz: yeah but the problem for validators is beyond spec: we have a cache of pubkeys -> index that is a pain to change
[2026-01-08T16:13:02.183000+00:00] potuz: which is used in a hot path for signature validation
```

</details>
