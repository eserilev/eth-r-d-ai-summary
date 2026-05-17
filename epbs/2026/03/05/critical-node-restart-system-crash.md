# Critical Node Restart and System Crash

**Channel:** epbs | **Date:** 2026-03-05

## Summary

A critical system failure occurred on the Ethereum test network when barnabasbusa deployed the latest commit to `epbs-devnet-0-sync`, causing all Prysm nodes to crash with a fatal blockchain error. The error indicates a bid consistency validation failure during execution payload processing, where there was a mismatch between expected and actual parent block hashes. The running version was Prysm v7.1.3-rc.3 built from a commit pushed by Terence approximately 5 hours earlier.

Potuz expressed frustration that the pods were restarted without consultation and declined to immediately investigate the issue, stating he would remain unavailable while spending time with his children. The conversation reveals tension around deployment procedures and availability expectations, with potuz noting he doesn't know what changes Terence introduced in the recent commit that may have caused the failure.

The issue remains unresolved with no immediate action plan, as the primary developer (potuz) is unavailable and the specific cause of the parent block hash mismatch in the execution payload bid validation has not been diagnosed.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T10:34:57.952000+00:00] barnabasbusa: ```
[2026-03-05 10:34:47.34] FATAL blockchain: could not set up forkchoice: could not set up forkchoice checkpoints: could not update forkchoice's justified checkpoint: could not update justified balances: could not get justified balances: could not process block: could not process execution payload bid: bid consistency validation failed: bid parent block hash mismatch: got 509ee2ee5db1d809fe424518c65591cc131361e0db26c7cf9a6eae9f3044a783, expected 34608ada4877b3e6106c5962be9fba133a0660840262d786a136e37c4f6cff67
```
[2026-03-05T10:35:14.279000+00:00] barnabasbusa: well shit
[2026-03-05T10:36:02.927000+00:00] barnabasbusa: at least all prysm nodes fail at the same parent block hash
[2026-03-05T10:38:22.113000+00:00] barnabasbusa: Hopefully this will get potuz out of bed 😂
[2026-03-05T10:38:29.331000+00:00] potuz: What is this. At least give me time to have coffee
[2026-03-05T10:38:35.801000+00:00] barnabasbusa: too late 😂
[2026-03-05T10:38:37.792000+00:00] potuz: Why in the hell did you restart the pods
[2026-03-05T10:38:45.102000+00:00] potuz: I'm not doing anything
[2026-03-05T10:38:58.205000+00:00] potuz: Let the chain die for all I know
[2026-03-05T10:39:08.729000+00:00] barnabasbusa: we were too stable
[2026-03-05T10:39:18.062000+00:00] barnabasbusa: I just rolled out the latest commit on `epbs-devnet-0-sync`
[2026-03-05T10:39:18.614000+00:00] potuz: Which commit are you running. Terence pushed something last night
[2026-03-05T10:39:59.211000+00:00] potuz: The error you show above should not be a problem necessarily, will keep here with my kids playing with DND miniatures
[2026-03-05T10:40:14.668000+00:00] barnabasbusa: the container crashes
[2026-03-05T10:40:35.382000+00:00] barnabasbusa: `[2026-03-05 10:35:30.25]  INFO main: Prysm Beacon Chain started version=Prysm/v7.1.3-rc.3/040c5c0d721289bea354c059be3bcd47d4852a3d. Built at: 2026-03-05 05:28:51+00:00`
[2026-03-05T10:40:38.759000+00:00] barnabasbusa: this is whats running
[2026-03-05T10:42:01.534000+00:00] barnabasbusa: looking at the commit tree, its indeed the last commit from 5h ago
[2026-03-05T10:54:17.101000+00:00] potuz: Well you shouldn't have restarted it before asking, I'm not going to sit in front of a keyboard now
[2026-03-05T10:54:38.463000+00:00] potuz: I don't even know what <@363800010518822915> pushed last night
```

</details>
