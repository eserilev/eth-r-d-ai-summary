# Builder Deposit Verification and Attack Prevention

**Channel:** epbs | **Date:** 2026-02-06

## Summary

This discussion centers around builder deposit verification in Ethereum's consensus layer, specifically regarding a GitHub PR (#4897) that deals with integrating builder withdrawal credentials (WCs) into the validator object space. The core technical challenge is determining whether to verify builder deposits, as the current implementation approach creates complexity when comingling builder and validator data structures.

Potuz identifies a critical security concern: without proper verification, anyone could front-run a builder and interfere with their deposit, creating potential attack vectors. Two alternative approaches were discussed but both have significant drawbacks: returning money at the execution layer (which opens new attack possibilities and introduces subtle bugs) or modifying the pending deposits logic to include builder deposits (which was previously explored but has its own issues).

The discussion concludes with ralexstokes indicating they need more time to analyze the trade-offs, leaving the verification approach as an open question that requires further consideration before proceeding with test verification.

## Participants

- potuz
- ralexstokes

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-06T01:13:48.777000+00:00] ralexstokes: <@755590043632140352> <@592004585506340885> sorry if you already saw this but curious about this question

https://github.com/ethereum/consensus-specs/pull/4897/changes#r2771800400

the logic on the PR right now is fine, and i've looked at some of the tests but wondering if we can simplify further

once we align on that question i'm happy to continue verifying the tests

comingling builder WCs into the validators "object" space makes this a bit subtle to do correctly
[2026-02-06T01:16:34.709000+00:00] potuz: Probably better to continue here. The problem of not verifying is that anyone can front run a builder and screw that deposit
[2026-02-06T01:17:16.989000+00:00] potuz: We could "return" the money in the EL but that also opens for attacks and even subtler bugs
[2026-02-06T01:18:13.767000+00:00] potuz: The other option is to change the logic of pending deposits and add builder deposits there as well, which is something we explored before but it also has a number of drawbacks
[2026-02-06T19:09:45.685000+00:00] ralexstokes: right, ok let me chew on it a bit more
```

</details>
