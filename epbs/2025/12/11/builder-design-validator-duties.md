# Builder design without validator duties discussion

**Channel:** epbs | **Date:** 2025-12-11

## Summary

The discussion centers on a proposed design change where builders would be separate non-validating actors rather than validators with duties. Builders have expressed preference to avoid deposit churn and forgo validator rewards, which would simplify the specification while maintaining the ability for actual validators to submit P2P bids as a fallback mechanism. The proposal involves builders depositing as little as 1 ETH, making the role highly accessible while providing sufficient collateral, and implementing immediate deposits without churn restrictions after a fork.

Key technical considerations include handling existing 0x03 validators during the fork transition, with suggestions to either put them in the exit queue or immediately convert them to builders by withdrawing 1 ETH and churning the rest. There are concerns about potential spam from unlimited low-cost builder registrations drowning out legitimate bids on the P2P network, though the DoS protection through bid throttling should address security issues.

The simplified builder design without duties would eliminate several complexity points in the specification, including `is_builder_payment_withdrawable` checks and `withdrawable_balance > 0` conditions in `get_expected_withdrawals`, since payments would always be withdrawable. The discussion references ongoing specification work in Justin's WIP branch for implementation details.

## Participants

- fradamt
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-11T09:30:27.580000+00:00] potuz: Hey <@592004585506340885> another issue for the breakout (apologies that I send work your way constantly). Builders seem to be aligned that they would prefer to avoid the deposit churn than to receive rewards from performing duties. This is a reasonable request that actually makes the spec easier to reason about. However, we don't want to lose the fallback of P2P bids from actual validators (that's what actually removes the retirement of self building and that's what will make the auction healthier by having already existing entities sending bids). I think this is a reasonable ask from builders and we should see how invasive of a design it would be. Adding another non-validator builder slice to the state shouldn't be much of an issue. Having both mechanisms in place would be perhaps too complicated. Cc <@425572898787426305> <@607055410104500244> <@520034910149410861> <@586161934425128960> I believe you guys had something to say about this.
[2025-12-11T20:48:05.600000+00:00] fradamt: Moving the spec discussion on Justin's WIP branch here: https://github.com/ethereum/consensus-specs/compare/master...jtraglia:consensus-specs:builders

Tldr: goal is to have builders be separate non-validating actors that can have as little as 1 ETH, which simultaneously satisfies making the role extremely accessible to anyone (not a big deal not to be earning yield on 1 ETH, and 1 ETH is plenty of collateral for a fallback) while also avoiding the churn that comes with making builders be validators, which is something that's better for professional builders
[2025-12-11T20:50:17.989000+00:00] potuz: Are we going to withdraw 0x03  validators at the fork and make them builders?
[2025-12-11T20:50:26.077000+00:00] fradamt: <@755590043632140352> raised the point that not having any churn or limits can be a problem if someone deposits a ton of 1 ETH builders. In terms of state size, it seems to me that it's no worse than status quo because anyway we're forced to keep track of every deposit due to how the deposit contract works (we don't want to burn money so we have no real limits). However I wonder if this would be a problem on p2p, if there are too many potential bids (tbf same problem with 32 ETH, just magnified here)
[2025-12-11T20:51:10.654000+00:00] fradamt: I'd say just put them in the exit queue all at once and do nothing else
[2025-12-11T20:51:52.762000+00:00] potuz: Bids is fine cause they're heavily stopped
[2025-12-11T20:52:24.557000+00:00] fradamt: but how do I get my "honest" bid in if there's 100k bids trying to go around? It's fine from a DoS perspective, but it means that you can drown out "good bids"
[2025-12-11T20:53:46.142000+00:00] potuz: But can't we use the fork transition to make them builders immediately and then have them onboarded at the fork. Otherwise it will be a mess during that time
[2025-12-11T20:56:37.836000+00:00] potuz: We can process exits of 0x03 validators after the fork differently by immediately withdrawing 1ETH and the rest churn it.. it will make 0x03 validators not withdrawable ever, they just transfer to the corresponding builder
[2025-12-11T21:06:04.953000+00:00] fradamt: After the fork, deposits would be immediate since there is no churn, so builders could go online quickly (though it would need some block to be locally built first)
[2025-12-11T21:51:21.623000+00:00] jtraglia: Having builders without duties actually simplifies so much. In addition to the changes I've already made, I think we can get rid of `is_builder_payment_withdrawable` and the `withdrawable_balance > 0` check in `get_expected_withdrawals`. Payments are always withdrawable now & it's impossible to get into a situation where `withdrawable_balance` is zero.
```

</details>
