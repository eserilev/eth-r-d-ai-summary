# Forkchoice Implementation Nuances and Documentation

**Channel:** epbs | **Date:** 2026-02-05

## Summary

The discussion centers on documenting implementation nuances for Ethereum's forkchoice algorithm. Potuz mentions that while re-implementing forkchoice in Prysm, he encountered several edge cases that took significant time to resolve again, highlighting the need for better documentation of these implementation details.

He proposes updating an annotated file with these nuances, having already documented three specific issues: pruning of invalid branches with optimistic sync (which can cause bugs by invalidating the empty node), tracking pre-finalized information like finalized payload hash for empty but non-skipped slots, and a dependent root issue previously raised by another contributor. These complications represent implementation details and edge cases that aren't covered in the main forkchoice specification but are critical for client implementations.

The discussion appears to be part of a broader conversation about separating or organizing documentation, though the context for nflaig's incomplete message about having "a separate" something is unclear from the provided messages.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-05T15:20:50.003000+00:00] nflaig: I wonder if we should have a separate
[2026-02-05T23:45:29.302000+00:00] potuz: On the side: I am recalling some nuances as I implement forkchoice in Prysm and since this took me twice time (yeah my memory sucks and I forget that I had to deal with this already over a year ago) I thought about keeping the annotated file updated with these things in the end of the file. So far I've only written a little sentence about the prunning of invalid branches with optimistic sync (which can lead to a bad bug invalidating the empty node so please take care of this case, we should have a test), about keeping track of pre-finalized information like the finalized payload hash for the engine when the checkpoint root is for an *empty* slot that isn't *skipped*. And finally the dependent root issue that <@688748669268132001> raised here a while ago. Will keep that updated, most of the complications are for edge cases that aren't really in the main forkchoice specification, but rather in implementation details on clients.
```

</details>
