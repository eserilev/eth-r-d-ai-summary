# Gloas attestation space and committee index changes

**Channel:** epbs | **Date:** 2026-03-01

## Summary

The discussion focused on changes to attestation committee indices in Gloas, where attestations are transitioning from always using committee index 0 to potentially using index 0 or 1 to signal whether payloads have been seen by validators. Paul Harris raised concerns about whether additional attestation space per block would be needed, particularly when validators have inconsistent views regarding payload visibility.

Potuz clarified that the current attestation space should be sufficient, explaining that attestations missing the head will vote with committee index 1, and the existing space is adequate for recovering from any network splits. He noted that if more space were needed in the future, it could be added without significant CPU or bandwidth concerns, as it's not considered a major constraint at this time.

## Participants

- .paulharris
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-01T21:56:09.514000+00:00] .paulharris: in gloas we're going from attestations with 'always 0' committee index, to attestations that can have index 0 or 1 to flag if payloads are seen. Wondering if we need more space available in attestations per block again... i guess it depends how often validators don't have a consistent view wrt payload...
[2026-03-01T23:01:47.418000+00:00] potuz: I don't think we need more attestation space. What's going to happen is that attestations that miss head will vote with index 1
[2026-03-01T23:02:19.083000+00:00] potuz: With the current space it's good enough to recover from any split I think.
[2026-03-01T23:02:40.260000+00:00] .paulharris: ok all good, makes sense i guess
[2026-03-01T23:02:58.547000+00:00] potuz: Anyway we can always add some it's not that important neither CPU nor bandwidth wise
[2026-03-01T23:03:19.053000+00:00] .paulharris: yeh it's not a huge space issue now
```

</details>
