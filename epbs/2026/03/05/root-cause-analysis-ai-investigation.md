# Root Cause Analysis and AI Investigation

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centered on investigating the root cause of a recent Ethereum client issue involving Prysm. Potuz explained that the fixes were already available in the develop branch, but weren't properly deployed due to two human errors: he failed to cherry-pick one fix, and Terence had merged changes to his branch but forgot to push them the night before while believing he had. Potuz described this as an "extraordinary coincidence of things" that led to Prysm isolating itself from the network.

Parithosh was investigating why an AI system incorrectly identified the issue as a Teku bug. Potuz clarified that while Teku did contribute to the problem by producing blocks on top of a very old block (which Prysm didn't handle properly), and Teku continued producing blocks on their own fork, the specific error message about "Bid is not for the right parent block" that the AI flagged was actually from Prysm, not Teku. Potuz referenced a detailed blog post on his website (potuz.net) that provides a short and descriptive analysis of the incident.

## Participants

- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T10:11:30.717000+00:00] parithosh: what was the fix in the end?
[2026-03-05T10:21:07.343000+00:00] potuz: They were already included in develop. One was my mistake for not simply Cherry picking. And the other it was Terence that had merged to his branch but forgot to push the previous night and was convinced he did. The read in my blog post is very short and descriptive. It was an extraordinary coincidence of things that led to Prysm isolating itself
[2026-03-05T10:27:49.731000+00:00] potuz: https://www.potuz.net
[2026-03-05T10:28:40.276000+00:00] parithosh: was there any teku bug by any chance? trying to track down why AI hallucinated that
[2026-03-05T10:29:06.992000+00:00] potuz: Which AI?
[2026-03-05T10:30:02.867000+00:00] potuz: And yes Teku produced a block on top of a very old block and our not handling that properly led to the start of the issue.
[2026-03-05T10:30:15.617000+00:00] parithosh: we have a few, im trying to see which one can root cause the best. claude + this specific loop stefan has seemed to indicate its a teku issue with `Bid is not for the right parent block`.
[2026-03-05T10:31:52.655000+00:00] potuz: Teku kept producing blocks on their own fork
[2026-03-05T10:32:40.784000+00:00] potuz: But that particular message about the bid was in Prysm and was not about Tekus block
```

</details>
