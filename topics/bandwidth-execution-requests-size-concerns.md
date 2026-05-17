# Bandwidth impact and execution requests size concerns

---

## 2026-04-10 (epbs)

The discussion centers on bandwidth concerns related to execution requests being broadcast twice - once in the beacon block and once in the `ExecutionPayloadEnvelope`. Potuz identifies this duplication as the main disadvantage of the current approach and questions the potential size of these requests.

When nflaig suggests removing requests from the `ExecutionPayloadEnvelope`, potuz explains this isn't feasible because fork choice enforcement requires building on full blocks, and attesters need access to requests when payloads arrive to ensure builder safety. Both agree that optimistic sync also requires the requests to be available in the envelope.

The conversation concludes with acknowledgment that while execution requests are included in time-sensitive headers (referencing a GitHub issue about builder specs), they appear to be small in practice despite having high theoretical SSZ size limits. The group notes that requests are typically empty most of the time, and compression techniques like "pandas" might help, though no concrete data analysis has been performed yet.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-10T13:34:46.918000+00:00] potuz: so the only real dissadvantage is the cost of making the beacon block larger by the requests. Do we know how large these can be?
[2026-04-10T13:35:06.982000+00:00] potuz: these get now twice being broadcast in full
[2026-04-10T13:36:24.276000+00:00] nflaig: I was wondering if we can remove them from `ExecutionPayloadEnvelope`, I think this was just added so you can run the state transition without consulting the EL?
[2026-04-10T13:37:07.873000+00:00] potuz: the problem of not having them in the envelope is what I say above that forkchoice will enforce that you build on full
[2026-04-10T13:37:09.162000+00:00] nflaig: maybe pandas could help? the are most of the time empty it seems but their theoretical ssz size limit is high
[2026-04-10T13:37:22.909000+00:00] potuz: and attesters will not be able to take any incoming block as head
[2026-04-10T13:38:00.805000+00:00] potuz: so attesters need to have the requests available when the payload arrives to give builder safety
[2026-04-10T13:38:19.489000+00:00] nflaig: yeah you right i think, also optimistic sync needs it for sure
[2026-04-10T13:40:03.836000+00:00] nflaig: well we do sent them as part of the header right now which is very time sensitive, see https://github.com/ethereum/builder-specs/issues/120, but this is more theoretical, I don't think they are large in practice but also haven't looked at data
```

</details>

---

## 2026-04-13 (epbs)

nflaig analyzed mainnet data from the last 2 weeks to assess the impact of adding `parent_execution_requests` to blocks. The findings show that 91.2% of blocks have zero execution requests, with an average of ~29 bytes per block and a worst case of ~14 KB (74 deposits in one block). While nflaig concluded this impact is almost negligible, especially for storage, they noted the need for further analysis regarding limiting factors and whether the current SSZ limit can be reached without increasing the gas limit.

potuz raised a critical concern about malicious builder attacks, explaining that while the average case isn't problematic, builders could exploit the system by including many invalid requests at no cost to themselves, creating a denial-of-service attack on the next proposer who must process these costly invalid requests. When nflaig suggested using gossip checks against the root in the bid as a potential mitigation, potuz clarified that this wouldn't solve the issue since the root could be valid while still containing the maximum number of invalid deposit requests, shifting the burden from builders (who currently bear the cost) to subsequent proposers.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-13T21:23:55.334000+00:00] nflaig: was curious to get some mainnet data on this and sampled the last 2 weeks worth of blocks to get a better sense of whether or not adding `parent_execution_requests` to the block is big deal, some data points worth noting
- 91.2% of blocks have zero execution requests
- the average is ~29 bytes/block
- the worse case was ~14 KB extra data (74 deposits in one block)
so I do think this is almost negligible, especially in terms of extra storage, but definitely worth to analyse further, also wrt to what the limiting factor here, I doubt we can even reach the current ssz limit without bumping the gas limit?
[2026-04-13T21:25:45.156000+00:00] potuz: The average case is not the problem. The issue is if a builder can cause an ex ante for example by including a ton of invalid requests. It's free for them and broadcast is not an issue for the Payload, but it's costly to the next proposer
[2026-04-13T21:26:30.903000+00:00] nflaig: can that be prevented by having a gossip check against the root in the bid?
[2026-04-13T22:10:35.117000+00:00] potuz: The root can be fine
[2026-04-13T22:10:57.841000+00:00] potuz: It's perfectly fine to gossip the max number of deposit requests that are crap
[2026-04-13T22:11:07.298000+00:00] potuz: Builders can do this today but it hurts them
[2026-04-13T22:11:21.966000+00:00] potuz: Whether with your PR they'll hurt the next proposer
```

</details>

