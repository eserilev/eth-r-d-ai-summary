# ePBS testing issues on glamsterdam-devnet-3

**Channel:** epbs | **Date:** 2026-05-12

## Summary

721orbit is experiencing issues testing ePBS-related changes for rbuilder against glamsterdam-devnet-3. The problem occurs after the Gloas activation: while all three consensus layer clients (cl-1, cl-2, cl-3) work fine pre-Gloas, cl-3 gradually falls behind once rbuilder starts sending bids and eventually gets stuck on slot 147, stopping block imports entirely. The proposers are falling back to self-building blocks, likely because the rbuilder bids are invalid or not competitive enough to be selected.

The user is running Reth execution clients with Prysm consensus/validator clients using specific devnet images, including a custom Prysm beacon chain image (`bharath-123-buildoor-apis`) with the `--prepare-all-payloads` parameter. Potuz pointed out that this appears to be 721orbit's own branch, suggesting the issue may be related to their custom implementation rather than the base devnet infrastructure.

The root cause remains unclear, with open questions about whether the problem stems from invalid builder bids, the custom CL branch, or other ePBS implementation issues.

## Participants

- 721orbit
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-12T18:50:41.655000+00:00] 721orbit: Im trying to test epbs related changes for rbuilder against glamsterdam-devnet-3 and have been running into the following issue since yesterday. Not really sure why it's being caused, but might be a bug in cl.
  
Basically my cl-3 always falls behind and then gets stuck on a single slot. It stops importing self built blocks. Pre gloas everything is working fine, cl-1, cl-2, cl-3 all peer and follow the chain. Once gloas activates and rbuilder starts sending out bids, cl-3 gradually falls behind (proposers fall back to self build because rbuilders bids aren't being selected, possibly because the bid are invalid/not competitive) and then gets stuck on slot 147 forever.

I make the builder deposit transaction somewhere in epoch 4

images i am using:
```
  - el_type: reth
    el_image: ethpandaops/reth:bal-devnet-6                                                                                 
    cl_type: prysm                                                                                                               
    cl_image: ethpandaops/prysm-beacon-chain:bharath-123-buildoor-apis                                                                      
    cl_extra_params:                                                                                                             
      - --prepare-all-payloads                                                                                                   
    vc_type: prysm                                                                                                               
    vc_image: ethpandaops/prysm-validator:glamsterdam-devnet-3 
    count: 2   
```
[2026-05-12T19:34:53.307000+00:00] potuz: <@1386598056320831509> that's your branch it seems
```

</details>
