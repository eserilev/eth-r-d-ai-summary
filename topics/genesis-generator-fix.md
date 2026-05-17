# Genesis Generator Fix

---

## 2026-04-27 (epbs)

barnabasbusa confirmed that the latest Ethereum genesis generator already includes a fix that was being discussed. sproul acknowledged this update positively, noting that having the team working together in person likely expedited the resolution.

The discussion appears to reference a bug or issue in the genesis generator that has been successfully addressed, though the specific nature of the problem is not detailed in these messages. No further action items or open questions were identified in this brief exchange.

**Participants:** barnabasbusa, sproul

<details>
<summary>Raw messages</summary>

```
[2026-04-27T07:04:57.789000+00:00] barnabasbusa: latest ethereum genesis generator has the fix in it already
[2026-04-27T09:50:06.177000+00:00] sproul: awesome, figured that would get done pretty quick with you all together
```

</details>

---

## 2026-05-06 (epbs)

The discussion centers on a bug in Prysm's genesis block root calculation that differs from the Ethereum specification and other clients like Lighthouse. Sproul discovered that while Prysm passes existing spec tests, this is only because the test runner doesn't verify the genesis block root calculated by Prysm against the specification's expected value. To demonstrate the issue, Sproul created a patch (PR #16756) that exposes the discrepancy between Prysm's genesis block definition and what the spec requires.

Sproul proposed a comprehensive fix involving three steps: updating multiple clients (Prysm, Teku, Lodestar) to include the bid in the genesis block (which is both spec-correct and useful for light clients), merging changes to eth-beacon-genesis, and updating the specification with clearer text about genesis blocks plus adding proper spec tests. While acknowledging this creates additional work for the Gloas genesis, Sproul emphasized this should be the final push needed to ensure all implementations work correctly together.

The conversation reveals that current spec tests have a gap in genesis block validation, allowing client implementations to diverge without detection, highlighting the need for more comprehensive testing of genesis block generation across all Ethereum clients.

**Participants:** sproul, terencechain

<details>
<summary>Raw messages</summary>

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

