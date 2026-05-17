# Beacon API PRs and feedback requests

**Channel:** epbs | **Date:** 2026-03-11

## Summary

The discussion centers around two Beacon API pull requests. nflaig shared PR #586 related to serving PTC (presumably Peer-to-Peer Transaction Committee) duties, while bharath.vedartham requested feedback on PR #584, which proposes an API to allow external builders to construct `ExecutionPayloadEnvelope` objects from signed beacon blocks, execution payloads, and execution requests.

There was brief confusion between potuz and nflaig about potential merge conflicts, with potuz initially thinking about a different repository before realizing the discussion was specifically about the Beacon API repo. Potuz left a comment on one of the PRs questioning whether passing the full block creates too much overhead and asking why this approach is needed, indicating some technical concerns about the proposed API design that remain unresolved.

## Participants

- bharath.vedartham
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T12:45:04.586000+00:00] nflaig: https://github.com/ethereum/beacon-APIs/pull/586 related to this, in case anyone looked at serving PTC duties already
[2026-03-11T12:57:21.559000+00:00] bharath.vedartham: would love to get some feedback on this API https://github.com/ethereum/beacon-APIs/pull/584
this is to allow external builders to construct the `ExecutionPayloadEnvelope` given the signed beacon block, execution payload and execution requests
[2026-03-11T13:58:46.223000+00:00] potuz: My PR already has this removal it'll cause conflicts
[2026-03-11T13:58:58.784000+00:00] potuz: ahh you mean from the Beacon API
[2026-03-11T13:59:07.108000+00:00] potuz: yeah, crap I don' t even have those repos in mind 🙂
[2026-03-11T14:01:54.002000+00:00] potuz: left a comment, didn't think much about this but I guess it's too much overhead to pass the full block? why is it needed?
```

</details>
