# Attestation Processing and Payload Requirements

**Channel:** epbs | **Date:** 2026-02-14

## Summary

The discussion centers around a PR that changes how attestation processing works in relation to payload availability. Currently, without the PR, nodes are required to process attestations even when they don't have the corresponding payload, which while spec-compliant, creates implementation complexity particularly for Prysm.

However, potuz clarifies that even with the proposed PR changes, implementations should still queue attestations when payloads are unavailable and wait for the payload to arrive before processing. This suggests the PR may change the timing or requirements around attestation processing but doesn't eliminate the need for queuing mechanisms entirely.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-14T08:08:34.568000+00:00] potuz: Without this PR you're required to process the attestation even if you don't have the Payload. Notice that this is just fine as far as the spec is concerned. No need to queue. Just that for implementation it's very complicated at least for Prysm
[2026-02-14T09:55:45.092000+00:00] potuz: Also note that you should queue these attestations and wait for the Payload to arrive even with this PR
```

</details>
