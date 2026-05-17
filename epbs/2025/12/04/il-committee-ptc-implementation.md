# IL Committee and PTC Implementation

**Channel:** epbs | **Date:** 2025-12-04

## Summary

The discussion focused on whether the PTC (Payload Timeliness Committee) could be reused for the IL (Inclusion List) committee. barnabasbusa raised this question, and potuz confirmed it's possible but noted that PTC is already a subset of the beacon committee anyway.

terencechain provided additional context, explaining that the IL committee selection specification he wrote actually copied the exact same selection algorithm as PTC. However, he noted that the current implementation is based on an older version and should be rebased to align with the latest PTC selection algorithm version.

## Participants

- barnabasbusa
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-04T10:38:46.078000+00:00] barnabasbusa: qq, could we reuse PTC for IL committee?
[2025-12-04T10:48:31.136000+00:00] potuz: Yes we can, but that's using the beacon committee anyway (PTC is a subset)
[2025-12-04T15:06:24.109000+00:00] terencechain: IL committee selection (i wrote the spec) copied exactly the same PTC selection algorithm. (not the lastest version, we should rebase it)
```

</details>
