# Beacon API and Builder Endpoints for Payload Envelope

**Channel:** epbs | **Date:** 2026-01-29

## Summary

Potuz identified a deeper technical requirement beyond the current discussion scope: builders will need a dedicated beacon endpoint that can construct payload envelopes, taking the payload as input data. This is necessary because validator clients cannot construct the payload envelope independently from just the payload - it requires performing a state transition.

Nflaig confirmed that while their recent discussion focused on local block building, the current beacon API PR doesn't address the APIs that builders will need. He noted that they previously had a `builder` route/namespace for expected withdrawals, suggesting the new required APIs would likely be added there. The discussion is continuing in the referenced GitHub issue for the beacon APIs repository.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T01:25:18.759000+00:00] potuz: <@586161934425128960> <@884836500037066862> just finished reading the transcript of acdt and you had a talk about this. I think this goes deeper than vouch. Builders will need a beacon endpoint that constructs the payload envelope. That endpoint needs to take the payload as input data. The validator client can't construct this alone from the Payload, it needs to make a state transition.
[2026-01-29T08:26:21.325000+00:00] nflaig: yeah the discussion was focused on local block building, the current beacon api PR doesn't cover any apis that builders will need, we had a `builder` route/namespace there before for expected withdrawals so I guess the new apis they need would go there
[2026-01-29T08:27:02.346000+00:00] nflaig: there is ongoing discussion here https://github.com/ethereum/beacon-APIs/issues/572
```

</details>
