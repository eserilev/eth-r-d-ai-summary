# GLOAS fork proposer preferences and timing coordination

**Channel:** epbs | **Date:** 2026-02-17

## Summary

The discussion centers on timing coordination for the GLOAS fork, specifically regarding when proposer preferences should be broadcast to ensure smooth transition to in-protocol builder bids. bharath.vedartham identified a potential issue where `SignedProposerPreferences` for epoch E+1 are normally sent at epoch E, which would mean the first epoch after the GLOAS fork would only have locally built blocks since the P2P gossip rules ignore execution payload bids without valid proposer preferences.

jtraglia proposed a solution to broadcast proposer preferences before the fork activation, noting this wouldn't break consensus and that nodes already subscribe to new gossip topics 256 epochs before forks. This approach would allow block building with in-protocol bids to begin immediately at the fork epoch rather than being delayed by one epoch. bharath.vedartham agreed this would work well, suggesting clients could send preferences when they subscribe to subnets pre-fork.

Client implementers confirmed feasibility, with nflaig noting Lodestar already subscribes to new gossip topics 2 epochs before forks, and 0xunclebill indicating Lighthouse subscribes a few slots before but could easily do so earlier. The consensus appears to be that early preference broadcasting is both technically sound and practically implementable across clients.

## Participants

- 0xunclebill
- bharath.vedartham
- jtraglia
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-17T17:12:36.168000+00:00] bharath.vedartham: `SignedProposerPreferences` for epoch E+1 are sent at epoch E. So when we transition to gloas(let's say at epoch E), proposers should send the preferences for epoch E+1 at the start of that epoch ideally.
This P2P gossip rule: https://github.com/ethereum/consensus-specs/blob/d1227e8f849018ab47149fdd23a418b2666d0851/specs/gloas/p2p-interface.md#L333 ignores bids on the `execution_payload_bids` topic if a valid `SignedProposerPreferences` has not be seen for the slot. 
The ValidatorRegistrations(we may remove it and only send the max_trusted_bid preference) for the builder-API too will be sent at epoch E for epoch E+1. 
This would mean that even if builders are registered at the fork, the first epoch of gloas will be just locally built blocks and builder blocks will actually start appearing at epoch E+1 then?
[2026-02-17T18:18:12.038000+00:00] jtraglia: For in-protocol p2p bids, yes. I'm wondering if we can broadcast proposer preferences before the fork. It wouldn't break consensus & nodes are expected to subscribe to new gossip topics [`EPOCHS_PER_SUBNET_SUBSCRIPTION`](https://ethspec.tools/#specs/v1.7.0-alpha.2/config_vars-EPOCHS_PER_SUBNET_SUBSCRIPTION) (256) epochs before the fork anyway. See [here](https://github.com/ethereum/consensus-specs/blob/d1227e8f849018ab47149fdd23a418b2666d0851/specs/phase0/p2p-interface.md?plain=1#L1363-L1368).
[2026-02-17T18:22:36.547000+00:00] bharath.vedartham: that sounds like a good idea! I would assume clients can just sending the preferences when they subscribe to the subnets before the fork begins. So that by epoch E, the preferences are broadcasted across the network and block building can begin then
[2026-02-17T20:05:25.072000+00:00] nflaig: for Lodestar we already subscribe to new gossip topics 2 epochs before the fork, so that shouldn't be a problem
[2026-02-17T22:55:20.102000+00:00] 0xunclebill: in LH i think we do a few slots before the fork (need to double check) but i dont see why we couldnt subscribe to new topics earlier
```

</details>
