# Block Builder Parent Payload Selection and PTC

**Channel:** epbs | **Date:** 2026-01-22

## Summary

Bharath is seeking clarification on how block builders should select the correct parent execution payload when building blocks for a given slot S. The core issue is that PTC (Payload Timeliness Committee) attestations, which determine whether the execution payload from slot S-1 was seen in a timely manner, are only included by the proposer in slot S. This creates a timing challenge for block builders who need to know at the start of slot S which execution payload to build on top of.

The discussion centers around the scenario where if an execution payload is deemed untimely based on PTC attestations, the builder should apparently build on the parent of that untimely payload instead. Bharath notes that while potuz previously explained this concept, confusion remains about the implementation details. He suggests that clients would likely need to expose a beacon API endpoint to fetch the latest canonical execution payload block hash, since the canonical beacon block and canonical execution payload block hash may differ in these edge cases.

The discussion ends with open questions about the specific API mechanisms needed to handle this parent payload selection logic correctly.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-22T06:26:52.096000+00:00] bharath.vedartham: To clarify my undestanding, If I am a block builder and I need to pick the parent execution payload on top of which I need to build my block for a given slot S. The PTC attestations which dictate the timeliness of the payload from slot S-1 will only be included by the proposer in slot S. So at the start of slot S, if a block builders wants to know which is the execution payload on top of which I build my block, I would have to know the PTC attestations too right? to know whether the execution payload was not timely seen or not?
[2026-01-22T06:28:27.833000+00:00] bharath.vedartham: if it is not timely seen, then we need to pick the parent payload of the untimely payload i think?
[2026-01-22T06:29:19.405000+00:00] bharath.vedartham: potuz did explain this to me but i think i m still a bit confused. I would assume clients would expose some sort of beacon api to fetch the latest canonical execution payload block hash?
[2026-01-22T06:29:47.865000+00:00] bharath.vedartham: as i think the canoical beacon block and the canonical execution payload block hash might be different
```

</details>
