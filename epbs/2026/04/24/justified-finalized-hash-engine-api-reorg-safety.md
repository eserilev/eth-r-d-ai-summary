# Justified vs Finalized Hash for Engine API Reorg Safety

**Channel:** epbs | **Date:** 2026-04-24

## Summary

The discussion centers on whether to use justified or finalized hash for reorg safety in the Engine API, referencing a merged GitHub PR. Potuz initially argued that justified hash would be safe for preventing problematic reorgs, but acknowledges it doesn't solve the "unboundedness" issue. However, m.kalinin points out that the execution layer (EL) has no concept of justified blocks, and with Fork Choice Rule (FCR) changes, the safe block may no longer be justified, making finalized the more appropriate choice as the no-reorg point.

The conversation reveals a nuanced distinction between different types of reorgs. While tersec notes that justified blocks can theoretically be reorged past (since they're not finalized), potuz clarifies that for this specific PR, only reorgs to past heads matter - not normal chain reorgs. The key concern is consensus layer reorgs that appear as rollbacks to the execution layer, which potuz suggests "freaks the EL devs about." Tersec confirms that the existing Engine API explicitly prohibits such rollbacks, though the reasoning behind this restriction remains somewhat unclear.

The discussion leaves open the technical question of whether reorgs beyond justified blocks are actually possible in the specific context of head reorgs (as opposed to normal chain reorganizations), with tersec noting they would need to think more carefully about this particular case.

## Participants

- m.kalinin
- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-24T10:46:16.230000+00:00] potuz: fwiw, this merged but just in case, I think justified hash instead of finalized one is safe <@360491619402776577> <@425572898787426305> https://github.com/ethereum/execution-apis/pull/786#issuecomment-4312568113 
Even though it doesn' t change anything in the "unboundedness"  of the problem.
[2026-04-24T10:58:16.374000+00:00] m.kalinin: the EL has no notion of justified, it used to be the safe block before but with FCR the safe block may not be justified anymore. from that perspective i think we should use finalized as the point of no-reorg
[2026-04-24T15:01:41.567000+00:00] tersec: also finalized is the only really gasper one. everything else is a guess (yes, including fcr, kind of)
[2026-04-24T17:37:48.890000+00:00] potuz: wait how can justified be a guess? but I agree with Misha that if justified is not being passed anymore as safe (actually Prysm never sent it) then it doesn't make sense.
[2026-04-24T17:38:16.561000+00:00] potuz: I just felt guilty that I pushed for finalized as something that was theoretically possible when I think it may be actually impossible
[2026-04-24T17:38:27.524000+00:00] potuz: Justified is definitely possible
[2026-04-24T17:38:45.407000+00:00] tersec: it's possible to construct scenarios where one can reorg past justified
[2026-04-24T17:38:54.422000+00:00] tersec: by definition, it's ... not finalized
[2026-04-24T17:40:14.502000+00:00] potuz: yes, but in this context those reorgs are irrelevant
[2026-04-24T17:40:35.858000+00:00] potuz: the only thing that matters for this PR is the reorg to a past head and I believe this cannot happen beyond justified
[2026-04-24T17:40:47.347000+00:00] potuz: but I can definitely be wrong though
[2026-04-24T17:41:02.087000+00:00] tersec: ah, ok, that I'd have to think more carefully about
[2026-04-24T17:41:06.350000+00:00] tersec: that specific case
[2026-04-24T17:41:33.495000+00:00] potuz: reorgs that are also normal reorgs in the EL side are of no concern, the issue here is reorgs in the CL that are just rollbacks from the point of view of the EL
[2026-04-24T17:41:46.248000+00:00] potuz: which is a concept that apparently freaks the EL devs about
[2026-04-24T17:42:48.105000+00:00] tersec: yes, the existing engine API prohibits them pointedly, I was never really sure why
[2026-04-24T17:42:53.599000+00:00] tersec: like, it goes out of its way to highlight this case as invalid
```

</details>
