# Payload Size Limits and Protocol Overhead

**Channel:** epbs | **Date:** 2026-01-22

## Summary

Arnetheduck highlighted two critical issues with payload size limits in Ethereum's networking protocol. First, compressed payload sizes cannot be used for limits because compression is non-deterministic - different compressors can produce varying results, sometimes even larger than the original uncompressed data. This makes it unsuitable as a reliable boundary for protocol limits.

The second issue concerns protocol overhead calculations when establishing size limits. While it's possible to compute the minimum header size needed for a maximum payload, libp2p implementations may add additional header data that could cause messages to exceed carefully calculated limits. This creates an implementation audit requirement if limits are set with precise header accounting. Additionally, there are security considerations that tie limits to maximum gas usage - smaller limits reduce potential damage from malicious messages before they can be detected and rejected.

## Participants

- arnetheduck

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-22T16:19:43.640000+00:00] arnetheduck: you cannot use "compressed payload size" for any limit since the compression is not deterministically defined - it's best effot and different compressors will yield different payload sizes, even _larger_ than the uncompressed data
[2026-01-22T16:22:05.759000+00:00] arnetheduck: when establishing a limit, you need to account for protocol overhead as well - it's possible to compute this from a "payload size", ie you can know ahead of time the _smallest_ header that is sufficuent for a payload of the "maximum size" - however, the way libp2p is specified, dependign on the implementation, they might add _more_ stuff to the header and thus go over the limit - if we compute the limit to the prescision of exactly accounting for header sizes, implementations need to be audited.

similarly, there are security reasons to tie the limit to the max gas - the smaller the limit, the less damage a message can cause before it's caught
```

</details>
