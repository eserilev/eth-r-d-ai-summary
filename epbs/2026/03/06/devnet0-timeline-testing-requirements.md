# Devnet testing coordination and timing

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion centers around coordinating testing activities on an Ethereum devnet, with initial questions about testing withdrawals and deposits. The team agrees to wait until clients can properly sync and checkpoint sync before proceeding with major tests to avoid issues.

The conversation reveals a phased approach to client readiness, where testing can proceed once Lighthouse (LH) and Lodestar are able to maintain network stability, while Teku and Nimbus are given more time to catch up. The team plans to coordinate additional tests around Tuesday after an ACDT call, with the goal of stress-testing the devnet on existing structures.

Several specific test scenarios are identified for the devnet including deposit requests, withdrawal requests (which may cause network splits), blob transactions, and testing unavailable blocks to observe attestation behavior. There's mention of potentially using Kurtosis for testing setup, and the discussion concludes with someone being assigned to work on the coordination, emphasizing that devnets are meant for breaking and testing edge cases.

## Participants

- barnabasbusa
- hopinheimer
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T13:06:10.057000+00:00] barnabasbusa: should I test a withdrawals/deposit?
[2026-03-06T13:09:14.712000+00:00] potuz: wait until clients can sync/checkpoint sync, or we'll be doomed
[2026-03-06T20:40:03.358000+00:00] potuz: <@641548687813902336> <@654267572107083777> will you be able to sync before ACDT? It'd be able to coordinate extra tests to run them right after the call around Tuesday. Yes I'm frontrunning you <@412614104222531604> and rushing people 🙂
[2026-03-06T20:40:54.753000+00:00] barnabasbusa: I think its fine to do the tests if LH / Lodestar is able to keep up
[2026-03-06T20:41:01.480000+00:00] barnabasbusa: We can have teku/nimbus take their time
[2026-03-06T20:41:33.648000+00:00] barnabasbusa: I just wanna make sure lh and lodestar is able to keep on the netowrk, so we can keep providing a stable testing environment
[2026-03-06T20:44:19.639000+00:00] potuz: But anyway I think we should aim to stress this devnet on the existing structures. Deposits requests first should be relatively easy. Withdrawals next I think it may split but it'll be fun
[2026-03-06T20:44:43.918000+00:00] potuz: We can do a run on Kurtosis perhaps. Do you have a setup for this?
[2026-03-06T20:45:04.307000+00:00] potuz: At any rate devnets are to break stuff
[2026-03-06T20:45:54.742000+00:00] potuz: Also blob txs
[2026-03-06T20:46:12.098000+00:00] potuz: Those should be relatively easy
[2026-03-06T20:46:35.969000+00:00] potuz: An unavailable block, that one i also want to see if its attested
[2026-03-06T21:09:54.679000+00:00] barnabasbusa: <@737658693331714159>
[2026-03-06T21:11:02.842000+00:00] barnabasbusa: <@737658693331714159> hope it makes sense?
[2026-03-06T21:11:41.032000+00:00] hopinheimer: yup, on it!
```

</details>
