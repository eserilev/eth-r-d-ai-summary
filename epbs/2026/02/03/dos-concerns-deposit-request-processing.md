# DoS concerns with deposit request processing

**Channel:** epbs | **Date:** 2026-02-03

## Summary

Potuz raised concerns about a PR that could create denial-of-service vulnerabilities when processing builder deposit requests, as it requires looping over potentially very large slices multiple times for every imported request. While acknowledging that alternative approaches aren't necessarily better, Potuz noted that sharing the pending queue was something they originally wanted to avoid.

Jtraglia suggested the issue could be optimized through caching signature verifications, though noted this would be out-of-scope for the specifications themselves. There was initially some confusion as Jtraglia mistakenly thought the concern was related to one-time fork operations, but Potuz clarified this affects every request import operation, not just fork-related activities.

The discussion concluded with Jtraglia planning to create a dedicated thread to further discuss these DoS concerns, leaving the issue unresolved for now.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-03T19:13:42.451000+00:00] potuz: Actually I don't like this PR at all, it can make a DOS to process a bunch of builder deposit requests by having to loop over a potentially very large slice a few times 🙁
[2026-02-03T19:14:29.291000+00:00] potuz: But I don't think the alternatives are nicer, and sharing the pending queue is what we wanted to avoid to start with
[2026-02-03T19:17:48.140000+00:00] jtraglia: I think it can be easily optimized. Eg you could cache all of the signature verifications.
[2026-02-03T19:18:02.806000+00:00] jtraglia: But that's out-of-scope for the specs.
[2026-02-03T19:18:43.493000+00:00] jtraglia: And onboarding builders at the fork is a one time operation.
[2026-02-03T19:19:13.985000+00:00] potuz: this has nothing to do with the fork
[2026-02-03T19:19:25.668000+00:00] potuz: this is on every request that is imported
[2026-02-03T19:19:31.950000+00:00] jtraglia: Oh shoot. Sorry my mind is elsewhere.
[2026-02-03T19:19:51.136000+00:00] jtraglia: I was just reviewing teku's changes for alpha.2 😅
[2026-02-03T19:21:24.390000+00:00] jtraglia: Going to make a thread to discuss this.
```

</details>
