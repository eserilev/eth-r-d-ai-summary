# Proposer Boost Implementation and Coordination Discussion

**Channel:** epbs | **Date:** 2026-01-07

## Summary

The discussion focuses on the implementation challenges of Proposer Boost (PB) changes that have appeared in spectests. fradamt clarifies that PB only helps when there's a single proposer choosing between competing branches, not when different shuffles create multiple competing proposers. Both participants agree that uncoordinated rollout wouldn't worsen the current situation, though there's no urgency for immediate deployment.

potuz indicates they're blocked by the appearance of these changes in spectests and plans to implement the changes in Prysm's next release unless there are objections, with the alternative being to skip forkchoice tests. They confirm that tests are affected, particularly "pulled tips tests" involving attestations on long reorgs, which may now switch heads earlier due to Proposer Boost behavior.

The key technical concern is that rather than immediately applying fixes to pass tests, they should study the affected tests to replace previous intended behavior with new behavior while still maintaining coverage for recovery from long reorgs. potuz references a specific Discord link for further technical details about the issue.

## Participants

- fradamt
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-07T11:17:38.666000+00:00] fradamt: If the shuffle is different in two competing branches, PB doesn't help because there isn't a single proposer. It helps only as long as you have two competing branches but a single proposer that can choose between them
[2026-01-07T11:18:49.002000+00:00] fradamt: Yeah I agree, I don't think rolling out this change in an uncoordinated way would make anything worse than it currently is. On the other hand it doesn't really matter to roll it out soon, so I am fine with either option
[2026-01-07T12:22:42.454000+00:00] potuz: I think we're kinda blocked now because it appeared on spectests. Unless people complain here I'll implement this today and it'll be in Prysm's next release the other option is to skip forkchoice tests
[2026-01-07T12:24:46.457000+00:00] fradamt: Does it change the outcome of any test though? Are there actually tests about applying proposer boost from a non-canonical proposer?
[2026-01-07T12:42:03.904000+00:00] potuz: Yes, I haven't looked at the list yet but I remember the pulled tips tests were putting attestations on very long reorgs, those tests are candidates to have switched head earlier because of PB. 

Perhaps rather than applying this fix now to pass the tests we need to study them to replace the previous intended test with the new behavior, but still test the recovery from long reorgs
[2026-01-07T12:42:53.997000+00:00] potuz: Look here <@520034910149410861> https://discord.com/channels/595666850260713488/1458104535695495282/1458230492724990163
```

</details>
