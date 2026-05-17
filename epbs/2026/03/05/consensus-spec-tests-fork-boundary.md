# Consensus spec tests for fork boundary

**Channel:** epbs | **Date:** 2026-03-05

## Summary

jtraglia submitted a PR with consensus spec tests and identified an issue regarding fork boundary behavior. The problem is that at the fork transition, `parent.body.signed_execution_payload_bid` will not exist, which affects the `get_parent_payload_status` function. However, they noted that there isn't currently a test that calls this function at the fork boundary.

potuz pointed out that testing this specific scenario is challenging because the `get_parent_payload_status` function is not exported, making it difficult to create unit tests. They mentioned that in previous EPBS (Enshrined Proposer-Builder Separation) tests, they had hardcoded the status to be "full" for the genesis state, which was problematic. jtraglia suggested that while unit testing might not be feasible, integration tests could potentially work, and they expressed willingness to attempt creating such a test.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T14:01:30.908000+00:00] jtraglia: Btw I did submit a PR with tests for this:
https://github.com/ethereum/consensus-specs/pull/4962
[2026-03-05T21:09:15.688000+00:00] jtraglia: Hey <@737658693331714159> hmm I think we should make a change for this.
[2026-03-05T21:09:32.935000+00:00] jtraglia: You're right; at the fork, `parent.body.signed_execution_payload_bid` will not exist.
[2026-03-05T21:10:45.788000+00:00] jtraglia: I guess we don't have a test which calls `get_parent_payload_status` at the fork yet.
[2026-03-05T21:16:54.021000+00:00] potuz: we can't test this I believe since this function is not exported
[2026-03-05T21:17:35.447000+00:00] potuz: and I do not know the status of the test runner now, but I had hardcoded this to be full for the genesis state in the first tests for epbs, this was always a pain in the neck
[2026-03-05T21:18:18.107000+00:00] jtraglia: It doesn't have to be a unit test.
[2026-03-05T21:18:47.195000+00:00] jtraglia: I'm not sure if it's feasible to make a test for this, but I can try.
```

</details>
