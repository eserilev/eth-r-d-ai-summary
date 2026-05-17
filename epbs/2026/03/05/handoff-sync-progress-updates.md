# Handoff to Sam and Sync Progress Updates

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Barnabasbusa handed off operational responsibilities to Sam as they went to bed, with instructions to remove the commit hash from images.yaml for Prysm nodes and perform an image refresh rollout. However, the team decided to wait before implementing these changes to ensure the current sync process completes successfully.

Potuz reported being at slot 2400 and approximately 600 slots away from head, with an estimated 20 minutes remaining to reach the target. Barnabasbusa confirmed that the sync had "passed the critical bit," suggesting the most challenging part of the synchronization was complete.

The action plan involves Sam taking over monitoring duties and executing the Prysm node updates once Potuz's node successfully reaches head, ensuring the current sync process doesn't encounter issues before making infrastructure changes.

## Participants

- barnabasbusa
- potuz
- samcm

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T02:35:39.958000+00:00] barnabasbusa: I’m off to bed now. good 🍀
[2026-03-05T02:35:53.384000+00:00] potuz: thanks
[2026-03-05T02:35:54.232000+00:00] barnabasbusa: handing it over to <@84562395988508672>
[2026-03-05T02:36:01.442000+00:00] barnabasbusa: he should be awake lol
[2026-03-05T02:37:03.508000+00:00] barnabasbusa: <@84562395988508672> just remove the commit hash form images.yaml from the prysm nodes and roll out and image refresh GL 🤣
[2026-03-05T02:37:23.260000+00:00] potuz: Oh I can proably just rerun the same commands you told me here
[2026-03-05T02:37:33.712000+00:00] potuz: anyway I'll wait to see this node at least get there
[2026-03-05T02:37:52.633000+00:00] potuz: should be just 20 more minutes max hopefully
[2026-03-05T02:37:54.716000+00:00] barnabasbusa: yeah can do that too
[2026-03-05T02:38:00.358000+00:00] potuz: I'm at 2400
[2026-03-05T02:38:15.147000+00:00] barnabasbusa: sounds like it passed the critical bit
[2026-03-05T02:38:33.024000+00:00] samcm: can't wait to ping you in ~1-2h when you're asleep <:troll:763968244863598624>
[2026-03-05T02:40:27.040000+00:00] samcm: just reading context and i can have a look
[2026-03-05T02:41:21.361000+00:00] potuz: It's from here what Barnabas told me to do to wipe the pods and move to the new images
[2026-03-05T02:41:38.747000+00:00] potuz: but we'lldo that after checking that this one actually gets to head <@84562395988508672>
[2026-03-05T02:42:19.224000+00:00] samcm: Yup no worries
[2026-03-05T02:42:24.577000+00:00] potuz: I'm still 600 slots away
```

</details>
