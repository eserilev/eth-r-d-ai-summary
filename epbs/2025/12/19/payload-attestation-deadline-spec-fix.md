# Payload attestation deadline spec fix

**Channel:** epbs | **Date:** 2025-12-19

## Summary

bharath.vedartham identified a minor specification error and submitted a fix via PR #4801. The issue was that the payload attestation deadline was incorrectly specified as `get_attestation_due_ms` when it should have been `get_payload_attestation_due_ms`.

This appears to be a straightforward correction to ensure the consensus specifications use the proper function name for payload attestation timing requirements.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-19T06:30:26.106000+00:00] bharath.vedartham: a minor fix to the spec: https://github.com/ethereum/consensus-specs/pull/4801 the payload attestation deadline was specified as `get_attestation_due_ms` instead of `get_payload_attestation_due_ms`
```

</details>
