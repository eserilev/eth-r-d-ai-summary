# Beacon API Pre-release for Devnet-0

**Channel:** epbs | **Date:** 2026-02-13

## Summary

nflaig announced the creation of a pre-release version (v5.0.0-alpha.0) of the Beacon API that can be used as a target for devnet-0. This release is up to date with alpha.2 but notably excludes block production APIs, giving clients flexibility to implement block production using whatever approach is easiest for them or alternatively use the approach outlined in PR #580.

The release contains several APIs and events that are out of scope for devnet-0, including the `execution_payload_bid` event/API, anything related to PTC (presumably Payload Timeliness Committee), and various miscellaneous APIs that have been added since the last release. This suggests the pre-release includes more functionality than what will be needed for the initial devnet-0 testing phase.

## Participants

- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-13T21:42:41.491000+00:00] nflaig: created a pre-release which we can use as target for devnet-0 (up to date with alpha.2), but doesn't contain block production apis, clients can do whatever is easiest for them to produce blocks (or https://github.com/ethereum/beacon-APIs/pull/580)

there are other apis/events in the release which are out of scope for devnet-0 like `execution_payload_bid` event/api, anything ptc related and some misc apis we've added since last release

https://github.com/ethereum/beacon-APIs/releases/tag/v5.0.0-alpha.0
```

</details>
