# Devnet-1 Launch and Client Implementation Status

**Channel:** epbs | **Date:** 2026-03-31

## Summary

The team is transitioning from devnet-0 to devnet-1, with some clients facing implementation challenges. 0xunclebill reported that their client won't be ready for the launch due to local bugs and will need to implement checkpoint sync to join later, as they prefer to test with their planned "full" forkchoice implementation rather than the previous hacky version. Meanwhile, there's uncertainty about forkchoice requirements, with potuz noting they lack a complete forkchoice implementation and missing equivocation guards.

barnabasbusa proceeded with launching a Prysm-only devnet-1 after killing devnet-0, clarifying that full forkchoice isn't required for the initial testing. nflaig quickly provided a branch implementation, and barnabasbusa offered to set up additional Lighthouse validators for testing purposes. The team agreed to use the `epbs-devnet-1` branch for coordinating their implementations, with nflaig successfully pushing their changes to this branch.

## Participants

- 0xunclebill
- barnabasbusa
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-31T07:39:00.759000+00:00] 0xunclebill: probs wont be ready today, working through some bugs were seeing locally
[2026-03-31T10:24:45.985000+00:00] potuz: Forkchoice isn't part of the testing right? We don't have a full fc either, we haven't added the equivocation guard and I don't even know how to add it
[2026-03-31T10:31:10.583000+00:00] barnabasbusa: killing devnet-0 now, and launching prysm only devnet-1 in a few hours.
[2026-03-31T10:31:46.603000+00:00] barnabasbusa: Unfortunately <@358120958726373381> as you won't be ready today, you will have to implement checkpoint sync in order to be able to join this network.
[2026-03-31T10:44:40.896000+00:00] barnabasbusa: Initializing the new network now
[2026-03-31T10:44:55.082000+00:00] barnabasbusa: I also don't think full fc is required for today
[2026-03-31T10:45:01.798000+00:00] barnabasbusa: so if you wanna try without that do let me know
[2026-03-31T10:45:03.896000+00:00] nflaig: I have a branch in like 10 minutes if you wanna wait
[2026-03-31T10:49:37.065000+00:00] 0xunclebill: it’s all good, we should be able to checkpoint sync pretty quickly. Pawan has a working range sync impl and I believe a working checkpoint sync impl that just needs a bit more testing

Id rather test with our “full” fc impl than what we had for devnet 0 (which was a complete hack job by me). and by “full” i mean what were actually planning on merging into our trunk branch. I don’t think we’re handling equivocations correctly yet either
[2026-03-31T10:50:18.228000+00:00] barnabasbusa: if you wanna try something hacky, I can put 2 machines and a few valis for lighthouse
[2026-03-31T10:50:34.338000+00:00] barnabasbusa: not enough to rock the boat but enough to see when things are working
[2026-03-31T10:50:49.149000+00:00] barnabasbusa: just push things to epbs-devnet-1 branch please
[2026-03-31T10:51:09.122000+00:00] nflaig: pushed `epbs-devnet-1` just now
[2026-03-31T11:09:40.164000+00:00] barnabasbusa: pushed `epbs-devnet-1` just now
```

</details>
