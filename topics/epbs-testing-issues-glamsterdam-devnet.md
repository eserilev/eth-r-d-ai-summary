# EPBS Testing Issues on Glamsterdam Devnet

---

## 2026-05-12 (epbs)

721orbit reported testing issues with EPBS (Enshrined Proposer-Builder Separation) changes for rbuilder on glamsterdam-devnet-3. The problem occurs after GLOAS activation when one of their consensus layer clients (cl-3) gradually falls behind the chain and gets stuck at slot 147, unable to import self-built blocks. While all three clients (cl-1, cl-2, cl-3) function normally before GLOAS activation, the issue appears when rbuilder starts sending bids that may be invalid or uncompetitive, causing proposers to fall back to self-building blocks.

The user is running Reth execution clients with Prysm consensus/validator clients using specific development images including `ethpandaops/prysm-beacon-chain:bharath-123-buildoor-apis`. They make a builder deposit transaction in epoch 4, after which the synchronization issues begin. Potuz responded noting that the issue appears to be related to 721orbit's branch, suggesting the problem may be in their specific implementation rather than the broader devnet infrastructure.

**Participants:** 721orbit, potuz

<details>
<summary>Raw messages</summary>

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

---

## 2026-05-16 (epbs)

The discussion centers around the timing for launching the "glam devnet 4 branch." barnabasbusa initiated the conversation by asking about this branch, likely seeking clarification on its status or timeline.

terencechain responded that the launch is currently blocked on team member reviews that need to be completed first. They also posed the question of when the team wants to schedule the launch, indicating that while technical prerequisites exist, the actual timing remains to be determined by the team.

The main open question is establishing a target launch date for glam devnet 4, pending completion of the ongoing review process.

**Participants:** barnabasbusa, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-16T05:43:24.524000+00:00] barnabasbusa: glam devnet 4 branch?
[2026-05-16T13:44:11.074000+00:00] terencechain: Waiting for team members to review first. When do we want to launch?
```

</details>

