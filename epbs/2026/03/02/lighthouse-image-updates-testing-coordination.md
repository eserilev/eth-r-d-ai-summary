# Lighthouse Image Updates and Testing Coordination

**Channel:** epbs | **Date:** 2026-03-02

## Summary

0xunclebill merged envelope reprocess queue functionality into their devnet branch and planned to work on `AttestationData::index` logic next. When potuz asked about testing against the updated branch, they discovered that automatic image building wasn't configured as expected, so 0xunclebill offered to prepare a manual image build.

The testing was deferred until the following day as potuz was unavailable for the evening. Later, nflaig confirmed that their latest `epbs-devnet-0` image now correctly implements attestation data index setting based on payload status, indicating progress on the attestation logic that was being worked on.

## Participants

- 0xunclebill
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-02T21:48:17.408000+00:00] 0xunclebill: i just merged our envelope reprocess queue stuff into our devnet branch. will take a look at our `AttestationData::index` logic now
[2026-03-02T21:57:13.547000+00:00] potuz: Do I need to do something to test against your branch?
[2026-03-02T21:57:53.660000+00:00] 0xunclebill: i think the pandas set it up to auto build the image when the branch gets updated. let me double check real quick
[2026-03-02T22:00:35.776000+00:00] 0xunclebill: actually dont think thats happening. ill prepare an image
[2026-03-02T22:00:58.040000+00:00] potuz: No rush. Already fried today on the second glass of wine
[2026-03-02T22:01:05.999000+00:00] potuz: Not testing anything until tomorrow
[2026-03-02T22:01:52.699000+00:00] 0xunclebill: lol sounds good, cheers 🍷
[2026-03-02T22:49:16.002000+00:00] nflaig: our latest `epbs-devnet-0` image now properly sets attestation data index based on payload status
```

</details>
