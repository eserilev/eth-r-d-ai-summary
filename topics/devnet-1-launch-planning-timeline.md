# Devnet 1 launch planning and timeline

---

## 2026-03-24 (epbs)

The team is planning to move forward with launching devnet 1 after experiencing difficulties with devnet 0. The new devnet will require PTC (presumably Proof of Stake Committee) selection changes, which terencechain has already implemented and is awaiting spec tests and release. External builder handling will also be required, though the team agrees to delay sending any external builder transactions until they're ready.

Key requirements for devnet 1 include mandatory sync and checkpoint sync functionality, with pawandhananjay noting that a basic version of range sync should be available within a week. potuz suggests running Kurtosis checks with the PTC changes and static vectors for the new state before launch. Initially Monday was proposed as a launch date, but this was ruled out due to the range sync timeline, with Wednesday being suggested as an alternative so progress can be reported at the next ACD (All Core Developers) call.

The discussion leaves the final timeline somewhat open, with the main dependencies being completion of range sync implementation, PTC change testing, and client readiness confirmation from all teams.

**Participants:** barnabasbusa, pawandhananjay, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-24T21:39:26.109000+00:00] barnabasbusa: I think we are struggling to make devnet 0 work again. So I’d propose that we aim to do a launch of devnet 1, and try not to break that 🫣
[2026-03-24T22:54:39.254000+00:00] terencechain: timeline? should we get the PTC window change in? i already implemented it, just waiting for the spec tests + release
[2026-03-24T23:40:33.853000+00:00] potuz: I for once agree with <@412614104222531604>, can we get a thumbs up from clients that they'll be ready to sync on devnet 1, this one will require the PTC selection changes and also handling of external builders but that one we can agree on not sending any until we are ready. However: sync and checkpoint sync should be mandatory cause we're sure to break quickly
[2026-03-24T23:41:30.077000+00:00] potuz: Monday is as good a day as any for me and it happens to be after a weekend which is a plus
[2026-03-24T23:42:19.796000+00:00] potuz: Although probably we want to run some Kurtosis checks with the PTC changes before and static vectors for the new state
[2026-03-24T23:44:40.173000+00:00] pawandhananjay: what's the timeline for devnet 1? I think we should have a basic version of range sync in a week or so
[2026-03-24T23:48:19.491000+00:00] potuz: Then Monday is out of the question we can try to aim for Wednesday? So that we can report at ACD?
```

</details>

---

## 2026-03-25 (epbs)

barnabasbusa proposed restarting devnet 0 to provide a stable testing network while the team takes an additional week to finalize the devnet 1 specification. The suggestion was made to ensure developers have access to a functional network for testing purposes during the extended development period.

tomi0x expressed agreement with the proposal to restart devnet 0. However, the discussion appears incomplete as only one team member has responded, leaving the final decision and implementation timeline unclear.

**Participants:** barnabasbusa, tomi0x

<details>
<summary>Raw messages</summary>

```
[2026-03-25T09:22:21.513000+00:00] barnabasbusa: if you all need another week to do devnet 1 spec, would you find it useful to restart devnet 0 just so we have a stable network ppl can test stuff against?
[2026-03-25T13:14:16.920000+00:00] tomi0x: yes
```

</details>

---

## 2026-03-30 (epbs)

Barnabas Busa is coordinating readiness status for the EPBS (Enshrined Proposer-Builder Separation) devnet-1 branches across Ethereum client teams. He specifically requested an update from one team member about when their epbs-devnet-1 branch will be ready and asked any other client teams that are ready to reach out to him.

This appears to be part of the coordination effort for launching the first EPBS development network, with Barnabas tracking which client implementations have completed their branch preparations for testing.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-03-30T15:22:15.756000+00:00] barnabasbusa: <@358120958726373381> can you please let me know when your epbs-devnet-1 branch is ready?
[2026-03-30T15:22:24.492000+00:00] barnabasbusa: also if any other client team is ready pls ping me
```

</details>

---

## 2026-03-31 (epbs)

The team transitioned from devnet-0 to devnet-1, with 0xunclebill's client team unable to participate immediately due to local bugs they're working through. As a result, barnabasbusa launched a Prysm-only devnet-1 and informed the delayed team they would need to implement checkpoint sync to join the network later, which 0xunclebill confirmed they could do quickly using Pawan's existing range sync and checkpoint sync implementations.

There was discussion about forkchoice (fc) implementation requirements, with potuz noting they don't have a full forkchoice implementation and lack equivocation guards. Barnabasbusa clarified that full forkchoice isn't required for the current testing phase. 0xunclebill expressed preference to test with their planned "full" forkchoice implementation rather than the previous "hack job" from devnet-0, though acknowledged they're not yet handling equivocations correctly either.

The session concluded with nflaig pushing code to the `epbs-devnet-1` branch and barnabasbusa offering to allocate some machines and validators for Lighthouse testing once ready. The immediate action items are for the delayed client team to complete their checkpoint sync implementation and join the network when their bugs are resolved.

**Participants:** 0xunclebill, barnabasbusa, nflaig, potuz

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-01 (epbs)

0xunclebill announced that a Lighthouse (LH) client image should be prepared and ready by tomorrow for devnet-1 deployment. This appears to be a brief status update on the preparation timeline for the Ethereum client software needed for the upcoming development network.

No further discussion, decisions, or technical details were provided in this single message update.

**Participants:** 0xunclebill

<details>
<summary>Raw messages</summary>

```
[2026-04-01T10:28:23.427000+00:00] 0xunclebill: we should have a LH image ready tomorrow for devnet-1
```

</details>

