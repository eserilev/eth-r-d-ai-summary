# Prysm Genesis Block Bug and Fix Discussion

**Channel:** epbs | **Date:** 2026-05-06

## Summary

The discussion centers on a genesis block bug in Prysm where the client's genesis block root calculation doesn't match the Ethereum specification or Lighthouse's implementation. While Prysm can pass existing spec tests, this is because the test runner doesn't actually verify the genesis block root against the spec - revealing a gap in the testing coverage.

Sproul created a demonstration PR showing the issue and proposed a three-part solution: update all clients (Prysm, Teku, Lodestar) to include the bid in the genesis block (which is both correct and useful for light clients), merge updates to eth-beacon-genesis, and update the specification with explicit genesis block requirements plus corresponding spec tests. Terencechain submitted a potential fix but acknowledged that Sproul's tests properly verify the genesis case unlike existing tests.

The team recognizes this requires significant work for the Gloas genesis but views it as necessary final effort to ensure all clients have consistent genesis block implementations that can be properly validated against comprehensive spec tests.

## Participants

- sproul
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-06T02:24:07.627000+00:00] sproul: on which branch? I'm trying to repro the bug against your `develop` branch but it's back on alpha.5, and so is `glamsterdam-devnet-2`
[2026-05-06T02:27:25.761000+00:00] terencechain: https://github.com/OffchainLabs/prysm/pull/16750 hopefully i didnt make any mistake here
[2026-05-06T02:32:08.908000+00:00] sproul: looking at your test runner with claude, it never checks the genesis block root calculated by Prysm against the one calculated by the spec
[2026-05-06T02:32:14.474000+00:00] sproul: so you're able to pass the spec tests
[2026-05-06T02:32:27.743000+00:00] sproul: but your genesis block def is not the same as the spec's, nor LH
[2026-05-06T02:32:43.790000+00:00] sproul: I'm working on a patch to demonstrate this now
[2026-05-06T03:13:43.184000+00:00] terencechain: Oh which spec test case it is?
[2026-05-06T03:25:49.036000+00:00] sproul: I don't recommend merging this, but this PR sketches out the issue: <https://github.com/OffchainLabs/prysm/pull/16756>
[2026-05-06T03:32:55.736000+00:00] terencechain: ah i see, your tests actually verify the genesis case 🙂
[2026-05-06T05:24:27.642000+00:00] sproul: how about this for a plan:

- update clients (Prysm, Teku, Lodestar) to include the bid in the block, seeing as this is correct + useful (per <@881905303011086387>’s point about the block header matching the state being useful esp for light clients). This will not break spec tests because this genesis block logic is currently untested
- merge my PR to eth-beacon-genesis (https://github.com/ethpandaops/eth-beacon-genesis/pull/77).
- update spec to include text stating what the genesis block should be, and update spec tests to follow this genesis block definition so clients can check their own impls against sanity_blocks or other tests

I know it’s a lot of work for Gloas genesis and it feels like flogging a dead horse, but hopefully this is the last push and then we can just have something that works 🤞
```

</details>
