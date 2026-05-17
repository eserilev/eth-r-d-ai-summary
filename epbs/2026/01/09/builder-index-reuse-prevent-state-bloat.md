# Builder Withdrawal Delay and Index Reuse

**Channel:** epbs | **Date:** 2026-01-09

## Summary

The discussion centers around a proposed removal of the builder withdrawal delay, which was originally implemented to prevent state bloat from users repeatedly deploying and withdrawing builders. julianma_ suggests that since builder indices can be reused after withdrawal, the delay may no longer be necessary as attackers cannot continuously bloat state with a fixed budget - they would simply reuse existing indices upon re-entry.

There appears to be some confusion about current implementations, with nero_eth initially stating that builder indices won't be reused in mev-boost implementations, but fradamt clarifying that index reuse for exited builders is indeed part of the beacon chain spec. The conversation reveals that mev-boost has moved away from including builder indices in validator registrations due to complexity concerns around dynamically changing indices, instead potentially using builder pubkeys or endpoint URLs for identification.

potuz raises a potential concern that quick withdrawals could enable cheap denial-of-service attacks on deposit requests per block, though questions whether this attack vector is meaningful or if it's already inexpensive under current conditions. The discussion concludes without a clear consensus on whether to remove the withdrawal delay, leaving open questions about the practical impact of potential DoS attacks and the best approach for builder identification in registrations.

## Participants

- bharath.vedartham
- fradamt
- julianma_
- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-09T14:30:32.471000+00:00] julianma_: Hi! From chatting with <@520034910149410861> , I understand that there is a withdrawal delay in the current spec to prevent people from bloating the state by deploying many builders and then withdrawing and redeploying. However, since (from what I understand) builder indices can be reused, someone cannot bloat the state by continuously reentering and withdrawing. Would it be possible to remove the withdrawal delay?
[2026-01-09T15:54:43.487000+00:00] nero_eth: Based on the current mevboost implementations I saw, builder indices won't be reused and registrations include the builder index. 
We can move towards using builder pubkeys for registrations instead and actually reuse exited builder indices.
But sure this is worth it though.
[2026-01-09T16:06:40.240000+00:00] fradamt: Not sure what you mean about mevboost implementations, reusing indices of exited builders for new builders is currently part of the beacon chain spec: https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/beacon-chain.md#new-get_index_for_new_builder

So if someone has a fixed budget of N eth, they can bloat the beacon state with N builders and then withdraw the eth, but they can't then re-deposit and continue bloating the state, because they'll just end up reusing the indices.
[2026-01-09T17:03:29.704000+00:00] bharath.vedartham: we updated the validator registrations in mev-boost to not include builder index. We generally don't see much value in it and also the builder indices could dynamically change for a given whitelisted builder which can make implementations more complex.
[2026-01-09T18:57:58.100000+00:00] nero_eth: Was referring to this PR:
https://github.com/ethereum/builder-specs/pull/138
In this case, registrations should contain a list of pubkeys instead of indices.
[2026-01-09T18:59:46.091000+00:00] nero_eth: The anti-state bloat re-use of indices makes sense but registration may want to take this into account. cc <@1386598056320831509>
[2026-01-09T19:28:14.909000+00:00] potuz: I don't think there's much need for either builder index nor builder pubkey in the registration. There's a need for them (or just the endpoint url) in the bid request. I am uncertain of allowing builders to withdraw quickly as this allows them to cheaply dos the deposit request per blocks, but not sure if this attack is meaningful nor if it's not anyway cheap today.
```

</details>
