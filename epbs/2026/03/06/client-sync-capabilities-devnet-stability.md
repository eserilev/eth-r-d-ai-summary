# Client sync limitations and blob support

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion centers around deployment limitations for an upcoming devnet due to missing sync functionality and blob support. 0xunclebill explains that their client cannot be deployed to the devnet because they lack sync capabilities, and additionally notes that their lack of blob support will cause issues.

When potuz asks for clarification about the type of failure - whether it would cause crashes or result in taking unavailable payloads - 0xunclebill confirms it would be the latter. Potuz indicates this would manifest as reduced attestation participation, with only about 30% of attestations being reliable during the devnet testing.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T22:48:27.689000+00:00] 0xunclebill: we don’t have sync so even if we fix anything we won’t be able to deploy it for this devnet
[2026-03-06T22:56:13.940000+00:00] 0xunclebill: we don’t support blobs, so that’s guaranteed to break us.
[2026-03-06T23:04:57.347000+00:00] potuz: Break you as in crashing or break you as in you'll take the unavailable payload?
[2026-03-06T23:05:32.128000+00:00] potuz: The latter will just show as 30% attestations we can count on
[2026-03-06T23:08:09.706000+00:00] 0xunclebill: well take the unavailable payload
```

</details>
